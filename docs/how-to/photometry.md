---
tags:
  - Lite
  - Scope Archive
  - Processing
---

# Time variable stars (photometry)

A Dwarf with nothing else to do can follow a variable star: an eclipsing binary's minimum, a pulsating star's maximum. **Dwarfium Lite** plans and captures it, the **Dwarfium Archive Selector** Siril script measures it, and **Scope Archive** keeps the light curves next to the sessions.

## Find tonight's minima and maxima { #minima }

**App:** Dwarfium Lite → a Dwarf's **Programs** page → variable stars button (top right) → program editor → **Variable star: min / max**

The button at the top right of the Programs page turns the feature on for this run of the app. It is off at each start, and it loads the stars' elements from the AAVSO's VSX.

1. In the program editor, click **Variable star: min / max**. The list shows the next minima of eclipsing binaries (I = primary, II = secondary) and the maxima of pulsating stars (**max**), seen from the Dwarf's site. It starts **tonight**; **Night of** picks another night.
2. Each line gives the time, the altitude, the window around the event, and whether all of it is dark and high enough (**Whole**).
3. Click a line. The target, RA/Dec, a description with the predicted time and its HJD, the start and end times and the image count are filled in. The filter cutting the infrared is set too (VIS on a DWARF 3, IR cut on a DWARF II).

The window is half the eclipse plus 30 min on each side when its duration is known, else ± 1.5 h. For a pulsating star it covers a whole cycle (0.6 period on each side, 30 min to 1.5 h).

**Stars and their elements:** the name alone is enough, the rest comes from VSX. Epoch and period typed in (e.g. Krakow's up-to-date elements) are kept over VSX's. **Update from VSX** refreshes them all.

!!! tip "Don't saturate the star"
    A star that saturates is measured too faint at its brightest. Start around **gain 60–80** and **60–90 s** exposures, and check them first with [Check exposure](#check-exposure).

## Check the exposure before the session { #check-exposure }

**App:** Siril → Dwarfium Archive Selector → **🔆 Check exposure**

Measures the variable star in a few test frames (raw FITS):

- its peak against the 12-bit saturation, and the longest safe exposure at that gain;
- its S/N, and the comparison stars that would saturate;
- the star's amplitude (VSX), and the Moon's phase and distance from the target;
- why a plate solve failed, when it does.

Needs Siril 1.4.0+ and Internet (plate solve, APASS comparison stars, VSX).

## Measure the light curve { #measure }

**Apps:** Scope Archive → Explore → **Prepare for Siril**, then Siril → Dwarfium Archive Selector

1. In Scope Archive, [Prepare for Siril](process.md#siril) the session. The darks from the [Dark Library](process.md#darks) come with it, and the frames the Dwarf rejected (`failed_`) are left out.
2. In Siril, run the **Dwarfium Archive Selector** script and tick **📈 Photometry**.
3. **📡 Load Dwarfium JSON** on the session's `siril_session.json`. It copies the session and writes a `01_photometry_<star>.ssf` script.
4. **🚀 Build Sessions** runs it:
    1. calibration and the green channel (AAVSO TG band);
    2. plate solve of the whole sequence by `seqplatesolve` (about a minute for 500 images), then one by one any image it left unsolved;
    3. comparison stars (APASS), the same in every image;
    4. the script's own differential photometry.
5. At the end you get `light_curve_*.csv` and its plot, the check star's scatter, and a survey of the field's stars (`stars_variability.csv`).

What it takes care of:

- **Times:** DATE-OBS (the Dwarf's local time) is converted to UTC, with JD, HJD and BJD at mid-exposure, and the airmass for each point.
- **Saturation:** stars above **3300 ADU** are left out, and comparison stars with a neighbour in their aperture too. You are asked whether to keep the target's saturated images: they are then flagged, drawn apart and kept out of the means.
- **Long sessions:** the lights are linked, not copied, and the intermediate images are deleted as it goes. 500 images would otherwise take about 40 GB.

!!! tip "Siril closed, or crashed, during a long session"
    The files made so far are kept, and the script says how to resume:

    1. Start Siril again and run the script.
    2. **📡 Load Dwarfium JSON**, then **Resume (keep the files)**.
    3. **🚀 Build Sessions** picks up from the plate solve, skipping the images already solved.

## See a star's light curves { #results }

**App:** Scope Archive → Explore → a session → **Photometry** tab

The results are archived at the archive's root, by star: `_PHOTOMETRY/<star>/<session>/`. The tab shows the session's curve and its CSV. Above it is the **combined curve** of all the star's sessions, from every backup drive: each session is offset onto the others through their common comparison stars. **Refresh** reads them again.

The files open on the PC: from a phone, these buttons say *Available on Desktop only*.
