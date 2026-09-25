# Fibre Tip Measure — lab tool

Open `index.html` by double-click (Chrome recommended). No install, no server, nothing uploaded:
images are read in the browser and stay on that computer.

## Use
1. Drop section images in (or "Choose files…"). Any image your browser opens: TIFF exports, PNG, JPEG, screenshots.
2. Set the scale for each image: type **µm per pixel**, or click **Calibrate by clicking 2 points**, type the
   known length in µm, and click the two ends of it (a scale bar, or a structure you have measured).
   Measure on a PNG or JPEG export — browsers cannot display TIFF, and dropping one just says so.
   If the export is a downscaled copy of the slide, the scale must be the export's µm/px, not the slide's.
3. Place the marks: **1 fibre tip**, **2 dorsal**, **3 ventral**, and optionally **4 bolus** (the centre of the injection bolus, ventral of the tip). Put the dorsal and ventral marks on the midline —
   at the brain surface if you want depth from the surface.
4. Optionally type the animal ID and the AP level (mm from bregma) you matched the section to.
5. **Copy table** (tab separated, pastes into Excel/Prism) or **Save file** (.json, reloadable with "Load saved").

**Dorsal up, ventral down.** Work with the section the right way up: if it is upside down, hit **Flip 180°**
first. Measurements are defined by your two midline marks, so the tool reads correctly either way, but the
orientation matters for two things it tells you — `axis_tilt_deg` is reported relative to the view you are
working in (a flipped upside-down section reads ~0°, not 180°), and the panel warns **dorsal is below ventral**
if the two marks end up the wrong way round. **Swap D↔V** exchanges them; note this reverses the axis, so the
ML sign flips with it — which is the point, as the sign is defined relative to the dorsal→ventral direction.

**Flip 180°** rotates the view for sections mounted the other way up (or scanned upside down), so you can work
with dorsal at the top. It only changes what you see: marks are stored against the image itself, so flipping
before or after marking gives the same measurements, and you can toggle it freely. The results record it in a
`flipped` column. Note a half-turn preserves left and right — it is not a mirror — so the ML sign stays correct.

Keyboard: 1/2/3 choose mark, f flip 180°, arrows nudge (Shift x10), [ and ] change image.

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

## Stereotaxic DV estimate

With an atlas plate chosen and the tip and ventral marks placed, the panel estimates how deep the tip sits:

    DV below bregma = (atlas depth from bregma to the midline ventral surface at that AP) - (tip height above your ventral mark)
    DV below dura   = (atlas dorsal-to-ventral midline distance at that AP)               - (tip height above your ventral mark)

The ventral midline surface is the anchor because it is crisp in every section and survives trimming and damage,
unlike the dorsal surface. The atlas depth is also flat along AP in the brainstem (7.0-7.1 mm from bregma between
-5.8 and -7.1), so a plate chosen 0.2 mm out barely moves the answer.

Both references are always written to the results (`dv_from_dura_mm`, `dv_from_bregma_mm`) — report whichever
matches how the implant was set.

**Snap ventral to surface** walks down your dorsal->ventral axis and puts the ventral mark on the last tissue
pixel, so the anchor does not depend on how carefully someone clicked. It handles fluorescence and brightfield
(it reads the corners to decide which way round tissue and background are) and tells you how far it moved the mark.

**Shrinkage correction** (checkbox) scales measured distances by `tissue span / atlas span`, taken from your two
midline marks against the atlas midline at the same plate. Processed tissue usually comes out 5-15% smaller, which
makes an uncorrected tip read too deep. The ratio is always shown and written out (`tissue_atlas_ratio`), and is
flagged if it falls outside 0.8-1.1 - which usually means the dorsal mark is not on the brain surface, or the
plate is wrong. Correction needs the dorsal mark on the dorsal surface; without that, leave it off.

**Limits.** The CCF has no bregma; its position here is the standard landmark convention, and CCF axes are tilted
a few degrees from the skull-flat frame. Treat these as estimates good to a few hundred microns, and say in a paper
that DV was estimated from atlas-matched sections rather than measured in vivo.

## Bolus mark (4)

Optional fourth mark for the centre of the injection bolus. It is measured in the same frame as the tip, so you get:

- `tip_to_bolus_um` — straight-line distance from tip to bolus
- `bolus_ventral_of_tip_um` — the component along your dorsal→ventral axis. Positive is ventral of the tip;
  negative (bolus dorsal of the tip) is shown in orange, since the usual case is a bolus below the fibre
- `bolus_ml_um`, `bolus_above_ventral_um` — its own position, same conventions as the tip
- `bolus_dv_from_dura_mm`, `bolus_dv_from_bregma_mm` — its own stereotaxic DV estimate

## Scale bar

The bar picks a round length near a fifth of the image width (10 µm up to 10 mm) and prints the µm/px underneath.
Its length is computed from the µm/px for that image, so **if the bar looks wrong, the scale is wrong** — the
usual cause is entering a µm/px from the full-resolution slide while measuring on a downscaled export. Check it
against something you know, or set the scale with **Calibrate by clicking 2 points**.
