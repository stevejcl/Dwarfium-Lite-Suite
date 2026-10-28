---
tags:
  - Lite
  - Scope Archive
  - Planning
---

# Choose and plan targets

## Know what to shoot tonight { #tonight }

**App:** Scope Archive → **What to shoot tonight** (menu *Tonight's Targets*)

The targets well placed tonight, each with its picture, an Aladin button and a plan button. Sections group the new targets, the ones you started and the ones already well covered by your archive.

## Plan a target — or a whole night — on a Dwarf { #plan-a-target }

**Apps:** Scope Archive → Dwarfium Lite (*Plan a target* page)

Both apps must be running (Scope Archive knows where Lite is: Lite says hello to it every 30 s).

**One target:** on *What to shoot tonight*, the plan button of a target → **Plan this target tonight**. Lite's **Plan a target** page opens with the target and its **best slot** tonight.

**Several targets (tonight's plan):** **Add to tonight's plan** on each target (5 by default — see Scope Archive's Settings), then open the plan from the floating *📅 Tonight's plan (n/5)* line. Lite chains the targets over the night: the first to set goes first, each slot inside its own window, 5 min apart. Edit a slot, or untick a target to leave it out — the others re-flow.

Then, on the *Plan a target* page:

1. Pick the **Dwarf**.
2. Choose how it is sent:
    - **Program** — saved on Lite's Programs page, run by Lite's scheduler (Lite must be running);
    - **Native schedule** — sent to the Dwarf itself, which runs it on its own ([what's the difference?](capture.md#native)).
3. Camera, autofocus, and per target the exposure, gain and filter (prefilled from the defaults; ↺ puts a target back on them). Lists only offer what the chosen Dwarf accepts.
4. **Send**. Your choices are remembered.

## Pick from the catalog and see the best slot { #catalog }

**App:** Dwarfium Lite → program editor or native schedule editor

- **Pick from catalog** — ~880 objects (Messier, NGC, IC, Caldwell, bright stars), plus your favourites, recent targets and own objects ([below](#my-objects)): search by name, filter by type, with the highest altitude reached in the dark that night. The ⓘ next to the title shows the help and the catalog files.
- **Altitude chart** — the target's height over the night with the Sun (darkness) and the Moon (phase, distance), and its compass direction. Set the darkness level and the minimum altitude; the **best slot** is highlighted.
- **Use best slot** fills the start and end times; a click on the curve sets the start time.
- In the native schedule editor, the targets already added are drawn in red, so the next one fits in the gaps.

!!! info "Shared catalog"
    Lite uses Scope Archive's catalog when it is installed next to it (or pointed to by `DWARFIUM_ARCHIVE_DIR`), otherwise its own copy (`assets/dso_catalog.json`). Your own objects are in `catalog_add_on.json`: next to Scope Archive's catalog (it reads them too), else in `~/.astro_dwarf_session/`.

## Search, favourites, recent targets and my objects { #my-objects }

**App:** Dwarfium Lite → program editor → **Pick from catalog**

- **Search** finds what you mean: case, accents, spaces and leading zeros don't matter, and *Messier* is read as *M*. `m42`, `M 42`, `Messier 42` and `NGC 01976` all find M 42. It searches the designation and the other names (`cocoon`, `orion nebula`), best match first. **Enter** picks the first result.
- **⭐ Favourites** — click the star of an object. Using a target doesn't make it a favourite.
- **🕘 Recent** — the targets of the programs already run on your Dwarfs (Done / Error), the last one first, with its date. A recent target that isn't in the catalog is picked with its program's description; the pin keeps it in *My objects*.
- **📌 My objects** — objects of your own. A name, RA and Dec are enough (type, magnitude and notes are optional). **Add an object**, or edit ✏️ / remove 🗑 one.
- The list shows the favourites first, then the recent targets, your objects and the catalog; the buttons above it show one group only.

**Keep a target when saving a program:** for a target from Stellarium, typed in by hand, a variable star or a recent one, tick **Keep in my objects** in the program editor. The choice is remembered. An object already in the catalog is not added twice.

On a phone the window keeps the designation, its name and type under it, and the altitude; the other columns are hidden.

## Take the target from Stellarium { #stellarium }

**App:** Dwarfium Lite

Set *Stellarium IP* and *Stellarium port* in the Dwarf's **Settings** (Stellarium's Remote Control plugin), select the object in Stellarium, then **Get from Stellarium** in the program editor.

## Use the catalog page and my Dwarf list { #catalog-page }

**App:** Dwarfium Lite → `/catalog` (open `http://<PC>:8090/catalog`, also from a phone)

A large DSO catalog with a sky map, a *Best of Tonight* plan and a mosaic planner.

- **+ Dwarf list** on an object's detail pane adds it to your list (5 objects at most). The **Dwarf list (n/5)** chip opens it → **Program to the Dwarf**: the objects are planned for tonight at the active site and sent to Lite. Objects not usable tonight are named and left out.
- When sending, **Keep these targets in astro_dwarf_session's catalog** adds objects that aren't in the shared catalog (Sharpless, LDN, LBN, vdB…) to `catalog_add_on.json`, so they show up in *Pick from catalog* too.
- Choose a specific Dwarf, or **Any Dwarf 3 (auto)** — see [any free Dwarf](capture.md#any-dwarf).

## Import a Telescopius plan { #telescopius }

**App:** Dwarfium Lite → program editor → **Import Telescopius/Mosaic CSV**

A Telescopius object list, mosaic list or custom mosaic list becomes one program per row, added to the queue. Review them on the Programs page.
