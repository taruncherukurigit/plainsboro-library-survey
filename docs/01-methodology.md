# Methodology

## Survey type

**Passive.** The device listens to broadcast beacon frames from every access point
in range and records signal strength, channel, width, band, BSSID and advertised
security capability at each measurement location. It does not associate for the
purpose of measurement, does not transmit probe traffic, and does not capture other
users' traffic.

This matters for scope: everything in this survey is information any 802.11 device
receives passively by existing in the building. No library infrastructure was
touched.

## Instrument

| | |
|---|---|
| Software | NetSpot for Android, Survey mode |
| Device | Motorola Moto G Stylus 5G (2024) |
| Chipset | Snapdragon 6 Gen 1 |
| Radio | Wi-Fi 5 (802.11ac), dual-band, 1×1 single spatial stream |
| Bands | 2.4 GHz and 5 GHz only — **no 6 GHz** |

The 1×1 single-stream configuration was confirmed by the observed 433 Mbps
association rate on an 80 MHz channel: 80 MHz × 1 stream × MCS9 × short guard
interval = 433 Mbps. A 2×2 client would show 866.

## Preparation

Two Android settings determine whether a survey produces real data or noise:

1. **Location permission (precise).** Android returns zero scan results to an app
   without it. Not optional.
2. **WiFi scan throttling disabled** (Developer Options). Android 10+ otherwise
   caps background scans at roughly four per two minutes, which would return
   near-duplicate readings across the entire building.

## Spatial method

NetSpot has no indoor positioning. The surveyor is the positioning system: stand
still at a real location, identify that location on the floor plan, tap it, wait
for the scan to complete, move on.

Floor plans came from the architect's published drawings. Scale was calibrated from
the graphic scale bar printed on each drawing rather than by pacing.

Measurement discipline held constant throughout:

- Device at chest height, screen up, same orientation at every point
- Held away from the body — a human torso attenuates 3–6 dB
- Stationary during collection

Grid spacing targeted 15–20 ft in open areas, with additional density at room
boundaries, both sides of doorways, and between bookshelf rows.

## Coverage achieved

| Zone | Points | Snapshot created (local) |
|---|---|---|
| Ground floor | 36 | 16:22 |
| Second floor | 31 | 17:11 |

On site 15:30–19:00. Approximately 50 people in the building, roughly 25 on the
ground floor — so these measurements reflect a moderately loaded network rather
than an empty building.

> **Note on timestamps.** The NetSpot database stores times in UTC. Raw values are
> 20:22 and 21:11; converted to local time throughout this documentation.

## A note on "map coverage %"

NetSpot reports a map-coverage percentage that reads as a quality score. It is not.
It measures **spatial spread** — how much of the floor plan area falls within
interpolation range of at least one measurement point — and says nothing about
density or accuracy.

An early practice run reported "75%, good" from six data points. Six points cannot
characterise a library floor. The final survey reported lower percentages than that
practice run purely because a finer grid square was selected, increasing the
denominator.

Point count and grid spacing are the meaningful density figures, and both are
reported here so a reader can judge the survey's weight independently.

## Exclusions

| Area | Reason |
|---|---|
| Third floor (children's level) | Excluded by the surveyor's decision. An unaccompanied adult walking a measurement grid through a children's area is inappropriate regardless of policy. |
| Mechanical room | Staff-only. Not entered. |
| Room 210 | Booked, but occupants had not vacated. Room 208 measured instead once it became free. |

Documented exclusions are normal in site survey work. A survey that claims complete
coverage of a working public building is usually not being honest about where it went.
