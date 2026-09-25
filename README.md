# Fibre Tip Measure — lab tool

Open `index.html` by double-click (Chrome recommended). No install, no server, nothing uploaded:
images are read in the browser and stay on that computer.

## Use
1. Drop section images in (or "Choose files…"). Any image your browser opens: TIFF exports, PNG, JPEG, screenshots.
2. Set the scale for each image: type **µm per pixel**, or click **Calibrate by clicking 2 points**, type the known
   length in µm, and click the two ends of it (a scale bar, or a structure you have measured).
3. Place three marks: **1 fibre tip**, **2 dorsal**, **3 ventral**. Put the dorsal and ventral marks on the midline —
   at the brain surface if you want depth from the surface.
4. Optionally type the animal ID and the AP level (mm from bregma) you matched the section to.
5. **Copy table** (tab separated, pastes into Excel/Prism) or **Save file** (.json, reloadable with "Load saved").

Keyboard: 1/2/3 choose mark, arrows nudge (Shift x10), [ and ] change image.

## What it reports, per section
- `ml_um` — distance from the midline, perpendicular to your dorsal→ventral axis. Signed: **+ is right of the axis
  looking at the image**, which equals anatomical right only if the section is mounted the usual way up.
- `dv_below_dorsal_um` — depth along the axis from your dorsal mark. Mark the dorsal brain surface for depth from surface.
- `above_ventral_um` — height above your ventral mark. Use this to compare across images whose dorsal surface is
  cropped or damaged.
- `dorsoventral_span_um` — distance between your two midline marks. A scale check: compare it against an atlas plate.
- `axis_tilt_deg` — how far your midline axis is off vertical. Measurements are taken along your axis, so tilt does
  not bias them; the number is there to flag a badly rotated or obliquely cut section (flagged past 25°).

## Notes and limits
- Measurements are in the plane of the section. AP is whatever you type; the tool does not estimate it.
- A mirrored (face-down) mount flips left and right, and no image can reveal it — check against your surgery records.
- Work is kept in that browser, per computer. **Save file** is the real save; use it before clearing browser data,
  and to move work between machines or hand it to someone else.
- Scale is per image, so images from different microscopes or magnifications can sit in the same session.

Geometry is unit-tested (rotation invariance, sign convention, incomplete-input handling).

## Atlas reference (added)

**Atlas reference…** under the AP box opens an Allen Mouse Brain Atlas plate beside your section.
Step through plates with the slider or `,` and `.` (Shift for ×5), match the outline, ventricles and
white matter, then **Use this AP** — it writes the bregma value into the AP box and records which plate
you chose (`ap_plate_idx` in the results, so a reviewer can see the match you made).

131 coronal plates, +5.33 to −7.99 mm from bregma in 0.1 mm steps, from the Allen CCFv3 25 µm average
template. Plates are built into the file — no download, works offline.

**The bregma values are approximate.** The CCF has no bregma; the conversion is a linear fit
(`bregma = -1.024 × AP_mm + 5.428`) and drifts near the poles, where the skull landmark and the atlas
diverge most. Treat it as ~0.1–0.2 mm, not exact, and say in a paper that AP came from atlas plate matching.
