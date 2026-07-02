# MARWIS Road-Weather Survey — 2026-07-02

Mobile road-weather measurement campaign in NE Belgrade (Pančevo bridge area, Zrenjaninski put through Kotež/Borča/Ovča) during a summer rain event. A Lufft MARWIS sensor logged road-surface condition at 1 Hz while a phone (phyphox) recorded GPS; the two streams were merged by time after verifying clock agreement (offset 0 ± 2 s).

**Interactive map: [webmap/](webmap/)** — pannable OSM map with the measurement track colored by water film height, friction, road condition, or surface temperature; field notes are shown as markers (⭐ = the two >1000 µm inflections caught at speed).

## Contents

| Path | Description |
|---|---|
| `webmap/` | Self-contained Leaflet viewer (`index.html` + `data.js`), no build step |
| `merged/marwis_gps_merged.csv` | Merged dataset: one row per MARWIS tick (5,525 rows, 98.5% georeferenced) with all 14 channels, interpolated GPS position/speed/accuracy, and QC flags |
| `marwis_*.csv` / `marwis_*.sqlite` | Raw MARWIS session logs (long format; CSV and SQLite are exact mirrors) |
| `Location GPS *.zip`, `gps_extracted/` | Raw phyphox GPS export (`Time (s)` is experiment time — map to UTC via `meta/time.csv` START/PAUSE anchors) |
| `marwis-snimanje-beleske.txt` | Handwritten field notes (Serbian, local time = UTC+2) |

## Sessions (UTC)

| Session | Time | Ticks | Notes |
|---|---|---|---|
| `131951Z` | 13:19:52–13:38:44 | 1,122 | Dry drive; one wet-strip event 13:37:37 |
| `134159Z` | 13:42:00–13:42:02 | 3 | False start over puddle 1 (recovered from SQLite) |
| `134224Z` | 13:42:25–13:45:55 | 209 | Stationary puddle test |
| `134859Z` | 13:48:59–14:59:39 | 4,191 | Main rain drive |

## Known caveats

- **Water film height saturates**: hard ceiling at 6,000 µm in downpour, and the sensor reads only ~1.0–1.15 mm over deep standing water (two documented cases vs >6 mm actual depth). Treat high readings as lower bounds.
- **Friction is censored** to [0.550, 0.820]. Raw float32 values carry epsilon noise above the floor — test saturation as `friction <= 0.5505`. Rows with `saturated = 1` (97 total) should be treated as censored, not measured.
- **Position uncertainty** is typically 4–8 m (GPS accuracy ~3.8 m median + ≤2 s clock bound × speed). Lane-level claims are not supported.
- 84 ticks are not georeferenceable (`gps_gap = 1`): 47 during a mid-drive GPS pause (14:10:41–14:11:28 UTC) and 35 at campaign end; their coordinates are intentionally null.
- `road_condition` flickers at wet/damp boundaries — smooth (e.g. 5 s mode filter) before using as a label.
