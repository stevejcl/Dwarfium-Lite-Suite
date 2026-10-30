---
tags:
  - Lite
  - Capture
  - Programs
---

# Capture and schedule

## Take pictures now, by hand { #live }

**App:** Dwarfium Lite → a Dwarf's page

- **Camera settings** — exposure, gain, binning, filter (per model), **Frames** (the number of images, for both cameras: sent on Enter or when leaving the field).
- **Actions** — calibration, **EQ Solving** (with the live azimuth / altitude correction: which way to turn the knobs), goto, capture, reboot, close the lenses, positions…
- **Live view** — Tele and Wide panels, each foldable; a **Stacked / Last frame** switch; **Full screen** opens the [live stacking view](#full-screen).
- The lights and power-indicator buttons work during a capture too. **Shut down** (far right of *Disconnect*) powers the Dwarf off after a confirmation.

!!! note "Live view needs FFmpeg"
    The RTSP live view uses FFmpeg. Without it, the Dwarf's page and the log file say so.

## Follow the stacking full screen { #full-screen }

**App:** Dwarfium Lite → the full-screen button of a Dwarf's card, or **Full screen** in its camera section

- **The last stacked image**, replaced as soon as the next one is loaded.
- **Over it:** the Dwarf and its target, *10 stacked · 12/20 taken · ends at 23:45*, the camera, exposure, gain and filter. **ⓘ** hides them (remembered by the browser).
- **Display adjustments** (the sliders button): brightness, contrast, saturation, black point, gamma, Reset. They belong to the Dwarf — its [watch views](remote.md#watch) show the image adjusted the same way — and change nothing on the Dwarf or in the files.
- **Zoom**: mouse wheel or pinch, drag to move, double click or double tap to reset. Each viewer zooms on their own.
- **DWARF mini**: the only one stacking with both cameras; the other one shows small in a corner — a click swaps them, a button hides it.
- During a [megastack](#megastack), its stack and its progress.
- The full-screen button of the browser, and **✕** to go back.

The image goes through Dwarfium Lite: the Dwarf is asked once per new image, whatever the number of viewers, and a phone over HTTPS or Tailscale sees it too.

## Create a program and run it later { #program }

**App:** Dwarfium Lite → a Dwarf's **Programs** page

A **program** is a list of steps run in order: start position, goto (solar system / manual RA-Dec / none), calibration, EQ Solving, auto focus or infinite focus, camera settings, capture (optionally a Dwarf mosaic), what to do after the session.

1. **Add** a program, set the date and time (calendar / clock, *now + 5 min*).
2. Fill the target: [catalog, Stellarium](plan.md#catalog) or RA/Dec — decimal or `18h 13m 40.3s`, `18:13:40.3`, `-17° 36' 06"`.
3. **Save**. The program waits in **ToDo**.
4. Leave **Scheduler active** on for that Dwarf. The scheduler runs due programs in the background, whatever page is open, and retries a failed step (*Max retries*).

The program then moves through **ToDo → Current → Done / Error**. The Programs page lists the newest first, 20 at a time (**Show more**).

!!! tip "Same program, another Dwarf"
    **Import a program from another device/model** reuses a program; values the other model doesn't accept (gain, exposure) are adjusted and said.

## Stop a capture at a given time { #end-time }

**App:** Dwarfium Lite → program editor → **End time**

If the requested count isn't reached by then, the capture stops cleanly instead of running into the dawn. Crossing midnight is handled. The *Use best slot* button fills it from the altitude chart.

## Use the Dwarf's own schedule { #native }

**App:** Dwarfium Lite → a Dwarf's page → *Shooting schedule (on device)*

| | Lite **program** | Dwarf **native schedule** |
|---|---|---|
| Who runs it | Dwarfium Lite (must stay running) | The Dwarf itself (PC can be off) |
| Steps | Everything (EQ Solving, start position, end of night…) | What the Dwarf's firmware offers |
| Same as | — | The DwarfLab app's own scheduling |

- **Add a target** (catalog and altitude chart available, *Next free slot*), then **Sync to device**.
- The list shows every schedule on the Dwarf with its state (Planned, In progress, Completed, Expired) and each target's progress; delete one, **View** a completed task, **Check** a failed one.
- Dwarf offline or busy? The schedule is kept as **Pending** — a banner offers **Sync now**, or it is sent automatically at the next connection.

!!! warning
    While a native schedule is planned, Lite's end of the night never powers the Dwarf off: it must stay on, it sleeps and wakes up on its own.

## Send targets to "any free Dwarf" { #any-dwarf }

**App:** Dwarfium Lite, from the `/catalog` page

With two or more Dwarfs of the same model, choose **Any Dwarf 3 (auto)** (or the model you have) when sending: Lite picks whichever Dwarf is actually free at send time.

## Resume a program that failed while the Dwarf kept capturing { #force-resume }

**App:** Dwarfium Lite → Programs → **Results** tab → **Force resume**

After a network drop, a run can be marked as failed while the Dwarf is still capturing its target. *Force resume* follows it again to its end.

## Repair a capture that failed (not in the Album) { #repair }

**Apps:** Dwarfium Lite → Programs → **Results**, then Scope Archive → **Mosaic** → **Repair**, then the Dwarf app

A capture that ended badly (the Dwarf hung, an empty battery, a reboot) keeps its images on the Dwarf, but its stacked FITS is missing: the session is in neither the Album nor the Megastack. DwarfLab's procedure, done by the apps:

1. **Lite, Results:** on the program in Error, **Repair session**. The same program runs again with 2 images (same target, exposure, gain and filter): a new session in the Dwarf's Album.
2. **Lite, Results:** once it is done, **Finish the repair in Scope Archive**. Scope Archive's Repair opens with the repair session (primary) and the failed capture (secondary) already found on the Dwarf.
3. **Scope Archive:** the Dwarf connected (USB or network), check both sessions, **Start Repair**. A summary says what will be done; **Repair**. The new session's own images are removed, the failed capture's images, thumbnails, `shotsInfo.json` and stacked files copied into it, renamed to its name and date (their time kept). The failed session itself is left as it is.
4. **Reboot the Dwarf**, then in the Dwarf app run a **Megastack** on that session. Its Album keeps the 2-image figures the Dwarf saved; the Megastack stacks all the images.

!!! tip
    - Choose a **work directory** in Scope Archive first: the new session's files the repair replaces are saved there (`<session>_before_repair`), a way back.
    - The repair session can also be taken by hand (same target, exposure, gain, filter): then choose both sessions yourself in Scope Archive's Repair.
    - The same way megastacks two sessions of one region under different names ("Unknown", a star near the target).

## Megastack / restack on the Dwarf { #megastack }

**App:** Dwarfium Lite → the Dwarf's **Astro Sessions** explorer → **Megastack** (top right)

The Dwarf stacks several of its sessions of one target together — or restacks a single one — into `Astronomy/RESTACKED/`, like the official app. The raw FITS must still be on the Dwarf.

1. Only stackable sessions are shown, grouped by target. Tick one or more of the same target (single captures or mosaics, not mixed; filters can be mixed).
2. **Megastack**. The Dwarf checks first (binning, target, filter) and says why if it refuses. Missing dark frames don't stop it — as in the official app, it stacks without them and the check step says how many were missing.
3. Progress shows everywhere: *Megastack in progress · 436/598 · 399 stacked*.

Scope Archive can ask for it too, from its Explore page. Meanwhile the Dwarf does nothing else: a due program waits.
