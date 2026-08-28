# Client-side evidence

Three screenshots from the survey device, corroborating findings from the beacon
data with an independent measurement path (the phone's own network stack, not
NetSpot).

| File | Time | Contents | Supports |
|---|---|---|---|
| `wifi-list-5ghz.png` | 18:44 | Connected to `Plainsboro-Public-Library` on 5 GHz, Signal strength "Excellent", **Security: weak · None** | F-04 — Android's own classifier confirms the public network is unencrypted |
| `wifi-linkrate.png` | 18:44 | Transmit link speed 96 Mbps, Receive link speed 78 Mbps, cropped from Network Details | F-03 — a link rate at roughly a fifth of the device's 433 Mbps ceiling under near-ideal signal, independent evidence of airtime contention |
| `wifi-list-24ghz.png` | 18:55 | Same network, 11 minutes later, now on **2.4 GHz**, Signal strength "Good", again **Security: weak · None** | Confirms the open-network finding a second time, independently, and shows the client roamed bands during the visit |

## Redaction note

`wifi-linkrate.png` is a crop of the Android Network Details screen. The original
also showed the client's IP address, gateway, subnet mask and DNS server — all
library infrastructure, not published. Only the two link-speed fields were kept.
