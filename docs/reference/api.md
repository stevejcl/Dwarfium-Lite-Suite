# Dwarfium Lite API

Dwarfium Lite answers a small HTTP / JSON API on its own port, next to its pages. The catalog page, the Milky Way planner, the *Plan a target* page, Scope Archive and the Hub all use it — and so can your own tools or scripts.

| | |
|---|---|
| **Base URL** | `http://<PC>:8090` (the Hub's port; started by hand, the port printed at startup or set with `--port`) |
| **Format** | JSON in and out (`Content-Type: application/json` on POST) |
| **CORS** | Open (`*`), for `GET`, `POST` and `OPTIONS`: a page served from elsewhere can call it |
| **Authentication** | None |

!!! warning "Local network only"
    There is no password: anyone who reaches the port can call it. Keep Dwarfium Lite on your home network or behind Tailscale, never exposed to the Internet.

## Overview

| Method | Endpoint | Use | Called by |
|---|---|---|---|
| `GET` | [`/api/app-info`](#app-info) | Which app and version answers on this port | Hub |
| `GET` | [`/api/dwarfs`](#dwarfs) | The Dwarfs and their state | catalog, planners |
| `GET` | [`/api/sites`](#sites) | The Sites (location, timezone) | Milky Way planner |
| `GET` | [`/api/programs`](#programs) | Each Dwarf's programs and native schedule | Program page |
| `POST` | [`/api/program`](#program) | Save (and optionally start) a program | Milky Way planner, *Plan a target* |
| `POST` | [`/api/schedule`](#schedule) | Send a native schedule to a Dwarf | catalog, *Plan a target* |
| `GET` | [`/api/task-check/{dwarfUid}`](#task-check) | What the Dwarf saved for a task (View / Check) | Program page |
| `POST` | [`/api/megastack`](#megastack) | Megastack sessions on the Dwarf | Scope Archive |
| `GET` | [`/api/megastack/preview/{dwarfUid}`](#megastack-preview) | The running megastack's image | Lite's pages |
| `GET` | [`/api/identify?ip=`](#identify) | Flash a Dwarf's ring light | Hub, Scope Archive |
| `GET` | [`/api/scope-archive/open/{dwarfUid}`](#scope-archive-open) | Open a session's import in Scope Archive | Program page |
| `POST` | [`/api/open-page`](#open-page) | Show one of Lite's pages in its window | Hub |

## Common rules

**`dwarfUid`** — a Dwarf's identifier in Lite: its Bluetooth name, e.g. `DWARF3_A1B2C3`. `GET /api/dwarfs` lists them.

**`auto:<model>`** — with two Dwarfs or more of the same model, `/api/dwarfs` adds a virtual entry such as `auto:Dwarf 3`. Sent to `/api/program` or `/api/schedule`, it is replaced **at send time** by the best Dwarf of that model: connected and idle first, then connected but busy, then offline.

**Errors** — `{"error": "<message>"}` with an HTTP status:

| Status | Meaning |
|---|---|
| `400` | Invalid JSON or a missing / wrong field (the message names it) |
| `403` | Endpoint reserved to this computer |
| `404` | Unknown `dwarfUid`, or no Dwarf of an `auto:` model |
| `409` | The Dwarf can't do it now (offline, busy) |
| `502` | The Dwarf refused the command (`reason` gives its error code) |

**Times** — programs are scheduled in the Dwarf's **Site** time. A tool running in another timezone sends absolute instants (`startEpochMs`, `endEpochMs`) rather than wall-clock strings.

---

## `GET /api/app-info` { #app-info }

Which application answers on this port, and its version.

```json
{"app": "astro_dwarf_session", "name": "Dwarfium Lite", "version": "3.2.6"}
```

Scope Archive answers the same endpoint with `"app": "dwarfium_scope_archive"`.

## `GET /api/dwarfs` { #dwarfs }

The paired Dwarfs, read from memory (what the dashboard shows).

```json
{
  "devices": [
    {"name": "Dwarf 3", "dwarfUid": "DWARF3_A1B2C3", "model": "Dwarf 3",
     "ip": "192.168.1.42", "connected": true, "busy": false, "hasPending": false},
    {"name": "Dwarf 3 garden", "dwarfUid": "DWARF3_D4E5F6", "model": "Dwarf 3",
     "ip": "192.168.1.43", "connected": false, "busy": false, "hasPending": true},
    {"name": "Any Dwarf 3 (auto)", "dwarfUid": "auto:Dwarf 3", "model": "Dwarf 3",
     "connected": true, "busy": false, "hasPending": false, "virtual": true}
  ]
}
```

| Field | |
|---|---|
| `model` | `Dwarf II`, `Dwarf 3` or `Dwarf Mini` |
| `connected` | Lite is connected to it now |
| `busy` | A command or a program is running on it |
| `hasPending` | A native schedule waits to be sent to it |
| `virtual` | Only on `auto:` entries (no `ip`) |

## `GET /api/sites` { #sites }

The Sites that have a location. The Wi-Fi name and password are never returned.

```json
{"sites": [{"name": "Home", "latitude": 48.85, "longitude": 2.35, "timezone": "Europe/Paris"}]}
```

## `GET /api/programs` { #programs }

For each Dwarf: Lite's programs to come, and the Dwarf's own (native) schedule.

```json
{
  "devices": [
    {
      "name": "Dwarf 3", "dwarfUid": "DWARF3_A1B2C3", "model": "Dwarf 3",
      "connected": true, "busy": false, "armed": true,
      "runningProgram": null,
      "localPrograms": [
        {"filename": "…json", "description": "M 31", "scheduledAt": "2026-10-24 21:30:00",
         "endAt": "…", "due": false, "status": "todo"}
      ],
      "nativeSchedule": [
        {"scheduleId": "…", "name": "…", "startTime": 1761334200000, "endTime": 1761348600000,
         "tasks": [{"name": "M 31", "startTime": "…", "endTime": "…", "shutterName": "15",
                    "gainName": "80", "filterModeName": "…", "count": 0, "stacked": 0,
                    "check": null}]}
      ],
      "nativeScheduleError": null
    }
  ]
}
```

- `armed` — the Dwarf's **Scheduler active** switch.
- The native schedule is read live when the Dwarf is connected and free. Otherwise the last read is returned, with `"nativeScheduleStale": true` and `nativeScheduleFetchedAt`. With no earlier read, `nativeScheduleError` says why: `not_connected`, `device_busy` or `read_failed`.
- `check` — the result of an earlier View / Check of that task, if any.

## `POST /api/program` { #program }

Saves a **program** of Lite — a goto on RA / Dec, then a capture with the Tele or Wide camera — in the Dwarf's **ToDo** list, as if saved from the editor. It runs at its date and time when the Dwarf's **Scheduler active** switch is on (the API never switches it on).

=== "Request"

    ```json
    {
      "dwarfUid": "DWARF3_A1B2C3",
      "target": {"name": "MW Tile R1C1", "ra": "18h 13m 40s", "dec": "-17° 36' 06\""},
      "camera": "wide",
      "exposure": "15",
      "gain": "80",
      "count": 240,
      "startEpochMs": 1761334200000,
      "endTime": "23:30",
      "endEpochMs": 1761348600000,
      "autofocus": true
    }
    ```

=== "Response"

    ```json
    {"ok": true, "mode": "scheduled", "filepath": ".../ToDo/….json"}
    ```

| Field | Required | |
|---|---|---|
| `dwarfUid` | yes | Or `auto:<model>` |
| `target.name` | | Default `target` |
| `target.ra`, `target.dec` | yes | Decimal, or `18h 13m 40s` / `18:13:40` / `-17° 36' 06"` |
| `camera` | | `wide` (default) or `tele` |
| `exposure`, `gain`, `count` | | Default: the Dwarf's usual values |
| `date` + `time` | | Site time, `YYYY-MM-DD` and `HH:MM:SS`. Default: now + 5 min |
| `startEpochMs` | | Start as an instant (ms), converted to the Site time — overrides `date` / `time` |
| `endTime` | | Stop the capture at `HH:MM` if the count isn't reached |
| `endEpochMs` | | The same as an instant (ms); needs `endTime` |
| `autofocus` | | `true`: focus before the capture |
| `binning`, `ircut` | | Tele only (`ircut`: the filter index) |
| `setup_camera` | | Tele only, the Dwarf's own mosaic: `{"doMosaic": true, "framingX": 100–180, "framingY": 100–180, "mosaic_count": 1–249}` (images per panel; 100 = one frame on that axis) |
| `startNow` | | `true`: also start it right away if the Dwarf is connected and idle |

`mode` in the response:

| `mode` | |
|---|---|
| `scheduled` | Saved, runs at its time |
| `started` | Saved and started now (`startNow`) |
| `saved_not_connected` | `startNow` asked, but the Dwarf is offline: saved, runs at its time |
| `saved_busy` | `startNow` asked, but a program is running: saved, runs at its time |

## `POST /api/schedule` { #schedule }

Sends a **native schedule** to the Dwarf: the Dwarf runs it on its own, PC off or not (Tele camera only). [Program or native schedule?](../how-to/capture.md#native)

=== "Request"

    ```json
    {
      "dwarfUid": "DWARF3_A1B2C3",
      "schedule": {
        "scheduleId": "6f1c…",
        "scheduleName": "M 31 - Home - 2026-10-24",
        "startTime": 1761334200000,
        "endTime": 1761348600000,
        "lock": 0, "password": "", "paramsMode": 0,
        "params": {"longitude": 2.35, "latitude": 48.85, "cityName": "Home", "focusMode": 0},
        "shooting_tasks": [
          {"name": "M 31", "ra": 0.7123, "dec": 41.269,
           "startTime": 1761334200000, "endTime": 1761348600000,
           "shutterIndex": null, "shutterName": "15",
           "gainIndex": null, "gainName": "80",
           "filterModeIndex": null, "filterModeName": "…",
           "count": 0, "stacked": 0,
           "schedule_task_id": "9a2e…", "createFrom": 2}
        ]
      },
      "saveToCatalog": false
    }
    ```

=== "Response"

    ```json
    {"ok": true, "mode": "sent", "catalogAdded": []}
    ```

Times are instants in milliseconds. A task may carry a `catalogMeta` block (the catalog page does): with `"saveToCatalog": true` the object is added to `catalog_add_on.json` if not already known — `catalogAdded` lists them. The block is removed before sending either way.

| `mode` | |
|---|---|
| `sent` | Sent to the Dwarf and accepted |
| `deferred` | Starts in more than ~12 h: the Dwarf would refuse it yet. Kept, sent automatically from `syncAt` (ms) |
| `pending` | The Dwarf is offline: sent at its next connection (or *Sync now*) |
| `busy_pending` | The Dwarf is busy: kept as pending, not raced |

Refused by the Dwarf: status `502`, `{"ok": false, "error": "…", "reason": "CODE_SHOOTING_SCHEDULE_TIME_CONFLICT"}` (its own error code).

## `GET /api/task-check/{dwarfUid}` { #task-check }

What the Dwarf saved for a task or a program run — the **View / Check** buttons. Query: `start` and `end` (the task's window, ms), `name` (the target).

```json
{
  "status": "ok", "nameMatched": true, "target": "M 31",
  "shotsStacked": 199, "shotsTaken": 240, "integration": "…",
  "thumbnailPath": "/…/stacked_thumbnail.jpg",
  "thumbnailUrl": "http://192.168.1.42/…/stacked_thumbnail.jpg",
  "archiveUrl": "http://localhost:8080/ImportSession?DwarfId=1&session=…"
}
```

`status`: `ok`, `not_found`, `empty`, `album_error`; `unknown_device` (404). `archiveUrl` is there when the Dwarf is linked to Scope Archive.

## `POST /api/megastack` { #megastack }

The Dwarf stacks several of its sessions of one target together (or restacks one) into `Astronomy/RESTACKED/`. Run now as a program, tracked to Done / Error.

```json
{"dwarfUid": "DWARF3_A1B2C3", "sessions": ["DWARF_RAW_TELE_M 31_EXP_15_GAIN_80_2026-10-20-21-30-00-000", "…"], "target": "M 31"}
```

→ `{"ok": true, "mode": "started"}`, or `409` with the reason (the Dwarf offline, a program or another task running on it) — nothing is saved then.

The checks come next, in the program: the sessions' FITS over FTP, then the Dwarf's own (binning, dark frames). A refusal ends the program in **Error**, with the reason in its steps.

## `GET /api/megastack/preview/{dwarfUid}` { #megastack-preview }

The running megastack's last complete image (`image/jpeg`), or `404` when there is none.

## `GET /api/identify?ip=<ip>` { #identify }

Flashes the ring light of the Dwarf at that IP (3 times), to tell which Dwarf is which on the table.

```json
{"result": "ok"}
```

| `result` | |
|---|---|
| `ok` | It flashed |
| `busy` | Capture, goto or program running: not done, the light would spoil the frames |
| `no_answer` | No Dwarf answered at that IP |
| `occupied` | Held by another app (DwarfLab…) |
| `failed` | The Dwarf didn't accept the command |

## `GET /api/scope-archive/open/{dwarfUid}?media=<path>` { #scope-archive-open }

Opens Scope Archive's import page for the session of that media path on the Dwarf — in Scope Archive's window when it has one.

```json
{"opened": true, "busy": false, "url": "http://localhost:8080/ImportSession?…"}
```

`opened: false` with a `url`: open it in a browser tab. `busy: true`: Scope Archive is still starting, try again. The URL is built from the Dwarf's Settings (Scope Archive URL and Dwarf Id), never from the request.

## `POST /api/open-page` { #open-page }

Shows one of Lite's pages where Lite is open **on this computer** (its window, or a local browser tab), and brings the window to the front on Windows. **Only from this computer** (`403` otherwise).

```json
{"path": "/manual-config?ip=192.168.1.42"}
```

→ `{"shown": true}`, or `{"shown": false}` when no page of Lite is open here (open a browser instead). `path` must start with `/`.

---

## Pages served next to the API

| Address | |
|---|---|
| `/catalog` | DSO catalog page |
| `/mosaic-planner-en`, `/mosaic-planner-fr` | Milky Way planner |
| `/plan-target` | Plan a target (opened by Scope Archive) |
| `/Program-en`, `/Program-fr`, `/Program-en/{dwarfUid}` | Programs and native schedules of every Dwarf |
| `/wake-dwarf` | Wake up a Dwarf from the phone |

## Scope Archive's side

What Scope Archive answers for the other apps (port 8080):

| Method | Endpoint | |
|---|---|---|
| `GET` | `/api/app-info` | `{"app": "dwarfium_scope_archive", "name", "version"}` |
| `POST` | `/api/peer/hello` | Lite says hello every 30 s: `{"app": "astro_dwarf_session", "port", "version"}` |
| `GET` | `/api/peer/status` | Where Lite is: `{"astroDwarfSession": {"url", "version"}}` |
| `GET` | `/api/open-in-app?path=` | Shows one of its pages in its window: `{"opened": true}`, `{"opened": false}` (no window: open a tab), `{"busy": true}` (starting) |
| page | `/ImportSession?DwarfId=&session=` | Import one session from the Dwarf, then its Transfer page |

## Example: plan a Wide capture from a script

```python
import requests

LITE = "http://192.168.1.10:8090"

dwarf = next(d for d in requests.get(f"{LITE}/api/dwarfs").json()["devices"]
             if d["model"] == "Dwarf 3" and not d.get("virtual"))

r = requests.post(f"{LITE}/api/program", json={
    "dwarfUid": dwarf["dwarfUid"],
    "target": {"name": "Cygnus wide", "ra": "20h 22m", "dec": "40° 15'"},
    "camera": "wide", "exposure": "15", "gain": "80", "count": 120,
    "date": "2026-10-24", "time": "22:00:00",
})
print(r.json())   # {"ok": true, "mode": "scheduled", "filepath": "..."}
```

Then switch **Scheduler active** on for that Dwarf (Programs page), and the capture runs at 22:00, Site time.
