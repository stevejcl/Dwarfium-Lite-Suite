# What's new

The highlights of the latest versions, with the page that explains them. Every detail is in each app's changelog:
[Dwarfium Lite](https://github.com/stevejcl/astro_dwarf_session/blob/NiceGui_V3_multi/CHANGELOG.md) ·
[Scope Archive](https://github.com/stevejcl/dwarfium-scope-archive/blob/main/CHANGELOG.md) ·
[Hub](https://github.com/stevejcl/dwarfium-hub/blob/main/CHANGELOG.md)

## Dwarfium Lite

### 3.2.6 — October 2026

- **Full-screen stacking view**: the stacked image, the session's progress and settings over it, display adjustments shared with the watch views, zoom. → [Full screen](how-to/capture.md#full-screen)
- **Pick from catalog**: a search that finds `m42` as `Messier 42`, **favourites**, **recent targets**, **my objects** (kept from the program editor), a phone layout. → [My objects](how-to/plan.md#my-objects)
- A **Dwarf's name** shown everywhere and renamed in its Settings.
- **Variable stars**: eclipsing binary minima and pulsating star maxima from tonight, one click to fill the program. → [Minima](how-to/photometry.md#minima)
- **HTTPS for phones**: Dwarfium Lite installed full screen on Android, with Dwarfium's certificate reused. → [HTTPS](how-to/remote.md#https)
- **Archive from the phone** opens Scope Archive on the phone (LAN, Tailscale). → [Phone](how-to/remote.md#phone)
- Manual positioning pad on a phone: no more runaway mount, Stop always sent.
- **After the session and end of the night** — leave the end-of-session screen, close the lenses or power off, initial position, bulky accessory. → [Unattended](how-to/night.md)
- **Megastack on the Dwarf** from the explorer (or asked by Scope Archive); missing darks no longer stop it. → [Megastack](how-to/capture.md#megastack)
- **Wake up a Dwarf from the phone** (`/wake-dwarf`). → [Wake up](how-to/connect.md#wake-up)
- Milky Way planner: **foreground pass**. → [Foreground](how-to/mosaic.md#foreground)
- Catalog's Mosaic Inspector reads each frame's `.wcs` file. → [Mosaic](how-to/mosaic.md#dwarf-mosaic)
- Camera settings: **Frames** on the Dwarf's page.
- **Repair a failed capture**: a 2-image repair session, then Scope Archive's Repair opened with both sessions. → [Repair](how-to/capture.md#repair)
- **Connect during a capture** no longer switches the Dwarf's mode (it could hang it); a capture whose counts no longer move is said on the card; a capture ended short says *battery empty* when it was; **Connect all** connects every Dwarf again. → [Troubleshooting](reference/troubleshooting.md)
- The five previous logs kept. → [Logs](how-to/maintain.md#logs)
- **A started capture is never stopped by Lite** for a network or answer problem; reconnection every 5 min while a program is in progress. → [Capture](how-to/capture.md#force-resume)
- **Your own elements** for a variable star (e.g. Krakow's), over VSX's. → [Minima](how-to/photometry.md#minima)

### 3.2.3 – 3.2.5

- **Plan a target** page and **tonight's plan**, opened from Scope Archive. → [Plan](how-to/plan.md#plan-a-target)
- Manual configuration from the **IP alone**; **identify** a Dwarf by its light (Hub). → [Add a Dwarf](start/first-dwarf.md)

### 3.2.0 – 3.2.2

- **Archive in Dwarfium Scope Archive** in one click. → [Archive](how-to/archive.md#one-session)
- **View / Check** what the Dwarf saved. → [View / Check](how-to/explore.md#view-check)
- Catalog: **Dwarf mosaic planner** and **my Dwarf list**. → [Catalog page](how-to/plan.md#catalog-page)
- Add a **language** without changing the code. → [Translate](how-to/maintain.md#language)

## Dwarfium Scope Archive

### V3.4.6 — October 2026

- **Variable star photometry** in the Siril script: Check exposure, light curve, plate solve by `seqplatesolve`, resume after a stop, the flat used, cloudy images left out. → [Measure](how-to/photometry.md#measure)
- **Time of maximum / minimum** with its error and the **O–C**. → [Extrema](how-to/photometry.md#extrema)
- Explore: **Photometry** tab, a star's sessions put together across the drives. → [Light curves](how-to/photometry.md#results)
- Result folders at the archive's root (`_PHOTOMETRY`, `_FIXED_FOREGROUND`, `_WCS_MOSAIC`). → [Files](reference/ports-files.md#where-the-data-lives)
- Analyze everything: **Stop**, and shown again when you come back to it. → [Re-sync](how-to/archive.md#analyze-all)
- Darks of one dark session at two temperatures used together. → [Darks](how-to/process.md#darks)
- On a phone: readable folder picker, *Available on Desktop only* for what needs the PC. → [Phone](how-to/remote.md#phone)
- Mosaic page, **Milky Way** tab: assembly by astrometry. → [Milky Way](how-to/mosaic.md#milky-way-assembly)
- Explore: **Fix the foreground** of a wide session. → [Fix foreground](how-to/process.md#fix-foreground)
- Explore: a session's **frames**, reject the bad ones. → [Quality](how-to/explore.md#quality)
- Sky Map: **best session per object**, hide a session. → [Sky map](how-to/explore.md#sky-map)
- Mosaic page, **Repair** of a failed capture (not only a mosaic), with a backup in the work directory. → [Repair](how-to/capture.md#repair)
- Photometry: comparison stars without Siril's server (local Gaia, VizieR mirrors, AAVSO), the **measuring circle chosen** on the check star, **variable comparison stars** left out. → [Measure](how-to/photometry.md#measure)
- Photometry: **the star's last session's comparison stars** reused, **your own elements** for the O–C. → [Measure](how-to/photometry.md#measure)
- Explore: a **failed capture** marked, repaired from there. → [Repair](how-to/capture.md#repair)

### V3.4.2 – V3.4.5

- **Analyze everything**. → [Re-sync](how-to/archive.md#analyze-all)
- **What to shoot tonight**: plan a target or tonight's plan in Dwarfium Lite. → [Plan](how-to/plan.md#plan-a-target)

## Dwarfium Hub

- **Installations** tab: home and Tailscale instances, FTP reachability. → [Remote site](how-to/remote.md#remote-site)
- **Dwarfs** tab: link, create, identify. → [Link](how-to/connect.md#link)
- **Synchronize** two Lite installations. → [Laptop](how-to/remote.md#laptop)
