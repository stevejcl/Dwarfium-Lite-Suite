# JavaScript helpers

Dwarfium Lite serves a few JavaScript files at `/js/…`. The [catalog page](../how-to/plan.md#catalog-page) uses them to send targets to a Dwarf. One of them, **`dwarf-scheduler.js`**, also works in a page of your own. Under the hood, they all call the [HTTP API](api.md).

| File | Kind | Reusable | What it does |
|---|---|---|---|
| [`dwarf-scheduler.js`](#dwarf-scheduler) | ES module | **yes** | Builds a native schedule from a list of targets, and opens the *Program session to the Dwarf* panel that sends it |
| [`app.js`](#app-js) | ES module | example | Wires a "program the ticked targets" button to the panel |
| [`dwarf-selection.js`](#catalog-only) | classic script | catalog only | *My Dwarf list* (up to 5 objects) |
| [`renderMosaic_dwarf.js`](#catalog-only) | classic script | catalog only | The catalog's Dwarf mosaic planner |

---

## `dwarf-scheduler.js` { #dwarf-scheduler }

```js
import { openDwarfProgramPanel, buildDwarfShootingSchedule } from 'http://<PC>:8090/js/dwarf-scheduler.js';
```

### `openDwarfProgramPanel(plans, meta)`

Opens the **Program session to the Dwarf** panel over the page:

- the targets, each with its time slot — untick one and the others re-flow;
- **Target Dwarf**: the Dwarfs read from `/api/dwarfs`, `auto:` entries included;
- **Keep these targets in astro_dwarf_session's catalog** — remembered;
- **⇪ Send to bridge** → `POST /api/schedule`, then the result: sent, pending, deferred, or the Dwarf's refusal code;
- **⧉ Copy JSON** / **⬇ Download .json** — the schedule as built;
- the address of Dwarfium Lite (*astro_dwarf_session URL*). When the page itself is served by Lite, it is the page's own address; otherwise `http://127.0.0.1:8765/api/schedule` until changed. It is remembered.

| Argument | |
|---|---|
| `plans` | One target or an array of targets (see below). Give each one its own `id`: the panel ticks targets by `id` |
| `meta` | **Required** (pass at least `{}`): `title` names the schedule, e.g. `{title: 'Tonight'}` |

### `buildDwarfShootingSchedule(plans, meta)`

The same schedule, without the panel: it returns the object to `POST` to `/api/schedule` (inside `{"dwarfUid", "schedule"}`).

```js
const schedule = buildDwarfShootingSchedule(plans, { title: 'Tonight' });
await fetch('/api/schedule', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ dwarfUid: 'DWARF3_A1B2C3', schedule })
});
```

### A target (`plan`)

| Field | | |
|---|---|---|
| `name` | text | Name shown on the Dwarf |
| `id` | text | **Unique** in the list. Inside the catalog, its catalog id: it also fetches RA / Dec and the object's details |
| `ra` | number | **Hours** (0–24) |
| `dec` | number | Degrees |
| `usableStart`, `usableEnd` | `Date` or ms | When the target can be shot tonight. Default: from now, for 4 h |
| `filterRec.subSec` (or `exp`) | text / number | Exposure in seconds, e.g. `'15'` |
| `filterRec.gain` (or `gain`) | text / number | Gain, e.g. `'80'` |
| `filterRec.filterId` (or `filterId`) | text | `dwarf-astro` → Astro Filter, `dwarf-duo` → Duo-Band Filter, `dwarf-vis` → VIS Filter (DWARF 3 only) |

### How the time slots are made

The Dwarf has one camera: it refuses tasks whose slots overlap. So the targets are not given their whole visibility window, but **consecutive slots**:

1. The target that sets first goes first: they are sorted by `usableEnd`.
2. The night, from the earliest `usableStart` to the latest `usableEnd`, is shared out in **equal slots**, **5 min apart** (the Dwarf needs that time to slew and calibrate).
3. The first slot never starts less than 5 min from now.
4. Slots shorter than 1 min: no task at all.

Each task is sent with `count: 0` (shoot until the end of its slot) and only the **names** of the exposure, gain and filter. Lite turns them into the Dwarf's own indexes when it sends the schedule.

!!! info "The site"
    Inside the catalog, the schedule carries the catalog's active location (longitude, latitude, name). In another page there is none: the Dwarf keeps its own.

### Example: a page of your own

```html
<button id="go">Program M 31 and M 33 tonight</button>
<script type="module">
  import { openDwarfProgramPanel } from 'http://192.168.1.10:8090/js/dwarf-scheduler.js';

  const tonight = (h) => { const d = new Date(); d.setHours(h, 0, 0, 0); return d; };

  document.getElementById('go').onclick = () => openDwarfProgramPanel([
    { id: 'M31', name: 'M 31', ra: 0.712, dec: 41.27,
      usableStart: tonight(21), usableEnd: tonight(25),
      filterRec: { subSec: '15', gain: '80', filterId: 'dwarf-astro' } },
    { id: 'M33', name: 'M 33', ra: 1.564, dec: 30.66,
      usableStart: tonight(22), usableEnd: tonight(27),
      filterRec: { subSec: '15', gain: '80', filterId: 'dwarf-duo' } },
  ], { title: 'Triangulum and Andromeda' });
</script>
```

In the panel, set the address of Dwarfium Lite (`http://192.168.1.10:8090/api/schedule`) if your page isn't served by Lite, pick the Dwarf, then **Send**.

---

## `app.js` { #app-js }

A small example of how to use `openDwarfProgramPanel`. When the page has a `#btn-program-selected` button, a click programs every ticked `.target-checkbox`. Each checkbox carries `data-id`, `data-name`, `data-ra` (hours) and `data-dec`, with 15 s, gain 80 and the Astro filter.

```html
<input type="checkbox" class="target-checkbox" data-id="M31" data-name="M 31" data-ra="0.712" data-dec="41.27">
<button id="btn-program-selected">Program the ticked targets</button>
<script type="module" src="http://<PC>:8090/js/app.js"></script>
```

---

## Catalog only { #catalog-only }

`dwarf-selection.js` and `renderMosaic_dwarf.js` are classic scripts written for the catalog page. They rely on its global functions (`_mosaicTarget`, `getActiveLocation`, `astroNightWindow`, `renderMosaic`…) and don't work in another page.

| File | Calls | Remembers (browser storage) |
|---|---|---|
| `dwarf-selection.js` — *+ Dwarf list*, *Dwarf list (n/5)*, *Program to the Dwarf* | `openDwarfProgramPanel` | `dso_dwarf_selection` (the list) |
| `renderMosaic_dwarf.js` — the *Planner → Dwarf* mosaic planner, *Send to Dwarf* | `GET /api/dwarfs`, `POST /api/program` (one per tile, with `startEpochMs`, and `setup_camera` for the Dwarf's own mosaic) | `dso_mosaic_mode`, `mosaicPlan_dwarf` |

Fields of view used by the mosaic planner (degrees):

| | Wide | Tele |
|---|---|---|
| DWARF II | — (Wide not supported) | 3.19 × 1.79 |
| DWARF 3 | 44.1 × 24.8 | 2.94 × 1.66 |
| DWARF mini | 43.6 × 24.5 | 2.13 × 1.20 |

## Browser storage keys

| Key | |
|---|---|
| `dso_dwarf_bridge_url` | Dwarfium Lite's address in the panel |
| `dso_dwarf_keep_in_catalog` | The *Keep in catalog* box |
| `dso_dwarf_selection` | *My Dwarf list* |
| `dso_mosaic_mode` | Catalog planner: ASIAIR or Dwarf |
| `mosaicPlan_dwarf` | Last Dwarf mosaic plan |
