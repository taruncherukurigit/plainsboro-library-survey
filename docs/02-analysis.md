# How the analysis was done

The NetSpot Android export (`.netspu`) is a **zip archive containing a SQLite
database**. Everything in this repository was derived from it directly rather than
read off heatmap images.

```bash
unzip "Plainsboro Public Library.netspu"
sqlite3 database.db3 ".tables"
```

## Schema

| Table | Rows | Purpose |
|---|---|---|
| `Zones` | 2 | One per floor, with the scale calibration factor |
| `Snapshots` | 2 | One survey run per zone |
| `ScanPoints` | 67 | Each measurement location, with map x/y coordinates |
| `WlanNetworks` | 1,523 | Every network observation, joined to a scan point |
| `Maps` | 2 | The floor plan images |

`WlanNetworks` carries `BSSID`, `SSID`, `RSSI`, `ChannelPrimary`, `ChannelCenter`,
`ChannelWidth`, `WiFiBand`, and `SecurityMode`. The `Noise` column was null
throughout — the Android build did not report a noise floor, which is why no
SNR-based finding appears anywhere in this analysis.

## Identifying access points from BSSIDs

The 94 distinct BSSIDs are not 94 access points. One physical AP runs two radios
(2.4 and 5 GHz), and each radio advertises several BSSIDs — one per SSID it serves.

The library's radios all share a structured prefix:

- `8a:15:04:XX:XX:Yn` — 2.4 GHz radios
- `8a:15:14:XX:XX:Yn` — 5 GHz radios

The `8a` first octet has the locally-administered bit set, which is typical of
controller-managed enterprise equipment issuing virtual BSSIDs. Grouping on the
middle bytes (`XX:XX`) collapses to **eight access points**, each with a 2.4 GHz
and a 5 GHz radio.

```sql
SELECT substr(BSSID,10,5) AS ap, WiFiBand, ChannelPrimary, ChannelWidth,
       max(RSSI), count(*)
FROM WlanNetworks WHERE BSSID LIKE '8a:15:%'
GROUP BY ap, WiFiBand;
```

This is a transferable technique. Counting SSIDs tells you nothing; counting
distinct BSSIDs overcounts; grouping BSSIDs by their shared radio prefix gives you
the AP count.

## Coverage statistics

For each scan point, the strongest reading from the public SSID in each band:

```sql
SELECT s._id, w.WiFiBand, max(w.RSSI)
FROM WlanNetworks w JOIN ScanPoints s ON s._id = w.ScanPointId
WHERE w.SSID = 'Plainsboro-Public-Library'
GROUP BY s._id, w.WiFiBand;
```

Every point on both floors met −67 dBm on 5 GHz.

## Co-channel exposure

The metric that turned out to matter. At each point, how many *library* radios were
simultaneously audible at usable strength?

```sql
SELECT s._id, w.WiFiBand, count(DISTINCT w.BSSID)
FROM WlanNetworks w JOIN ScanPoints s ON s._id = w.ScanPointId
WHERE w.BSSID LIKE '8a:15:%' AND w.RSSI >= -75
GROUP BY s._id, w.WiFiBand;
```

Mean 7.3 on 2.4 GHz, peak 13. Every co-channel radio a client can hear is another
participant in the same contention domain.

## Cross-floor propagation

Comparing each AP's reach on each floor isolates vertical propagation:

```sql
SELECT substr(w.BSSID,10,5) AS ap, z.Name, max(w.RSSI), count(DISTINCT s._id)
FROM WlanNetworks w
JOIN ScanPoints s ON s._id = w.ScanPointId
JOIN Snapshots sn ON sn._id = s.SnapId
JOIN Zones z ON z._id = sn.ZoneId
WHERE w.BSSID LIKE '8a:15:%' GROUP BY ap, z.Name;
```

Six of eight APs appear on both floors at −67 dBm or better. Three whose peak is on
the second floor still reach −42 to −49 dBm across essentially every ground floor
point — through the double-height central reading room.

This finding came from the data, but it was prompted by a physical observation: the
second floor is open to the first through the central volume. Measurement plus
observation; neither alone would have produced it.
