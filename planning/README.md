# Predictive model — proposed remediation

Not yet built. This directory holds the NetSpot Planning project that models the
recommended channel and power changes against the measured survey.

## What goes here

| File | Contents |
|---|---|
| `current-state.netspu` | Planning project with walls drawn, the eight APs at surveyed positions, current channel plan |
| `proposed.netspu` | Same geometry, recommended channel plan and reduced transmit power |
| `ground-current.png` / `ground-proposed.png` | Rendered comparison |
| `second-current.png` / `second-proposed.png` | Rendered comparison |
| `calibration.md` | Wall attenuation values used, and how they were tuned to match measurement |

## Method

1. Load the same floor plan images, calibrate scale identically to the survey.
2. Draw walls using the materials in the findings report, section 2.3 — brick cavity
   wall over CMU for the wings, curtain wall with low-E glazing for the central
   volume, gypsum board partitions, dense masonry at the feature walls.
3. Place the eight access points at their surveyed positions.
4. Set the current channel plan from `data/ap-inventory.csv`.
5. **Calibrate.** Render, then compare predicted signal against measured values in
   `data/scan-points.csv`. If the model predicts −60 where −50 was measured, adjust
   wall attenuation until they agree. Record what was changed and why — this step is
   what separates a model from a guess, and it belongs in the write-up.
6. Duplicate the project. Apply the recommended plan: 2.4 GHz rebalanced across
   1/6/11, 5 GHz moved into UNII-1 and UNII-2, widths standardised at 40 MHz,
   transmit power reduced for 15–20% cell overlap, vertically adjacent APs near the
   atrium separated in channel.
7. Render both and present side by side.

## What the comparison should show

Not better coverage — coverage is already 100% against threshold. The proposed model
should show **fewer co-channel radios audible per location** while maintaining the
−67 dBm floor. That is the actual improvement, and saying so correctly is more
impressive than claiming a coverage win that isn't there.
