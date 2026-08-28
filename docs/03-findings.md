# Findings

Six findings. Each states an observation, a probable cause, an impact, and a
recommendation. Numbers come from `data/`.

---

## F-01 · High · 2.4 GHz channel plan is heavily unbalanced

**Observation.** Five of the eight access points operate on 2.4 GHz channel 6. Two
use channel 11, one uses channel 1. Across the survey, 24 distinct BSSIDs were heard
at −80 dBm or stronger on channel 6, against 16 on channel 1 and 7 on channel 11.

**Probable cause.** Channels 1, 6 and 11 are the only non-overlapping options in
2.4 GHz. Concentrating five APs on one of the three forces them into a single
collision domain.

**Impact.** Co-channel contention. The affected radios hear one another and must
take turns transmitting. Aggregate throughput falls as client count rises, even
though signal strength is excellent.

**Recommendation.** Redistribute across 1/6/11 — roughly three, three and two.
No hardware cost.

---

## F-02 · High · 5 GHz radios concentrated in UNII-3 with mismatched widths

**Observation.** Three APs share channel 149 (one at 20 MHz, two at 80 MHz). Two
share channel 153 (20 and 80 MHz). One uses 157 at 40 MHz. Only two sit outside
this range — channel 36 and channel 108.

**Probable cause.** An 80 MHz channel with primary 149 occupies 149 through 161,
overlapping both 153 and 157 entirely. Six of eight radios contend within one
crowded band segment while UNII-1 (36–48) and UNII-2 (52–144) sit almost unused.

**Impact.** Both co-channel and adjacent-channel interference, in the band that
should carry most client traffic. Mixed widths on overlapping channels compound it.

**Recommendation.** Move radios into the unused UNII-1 and UNII-2 ranges and
standardise on a single width. In a dense multi-AP deployment 40 MHz is usually the
better trade than 80 — it halves per-client peak rate but doubles the number of
non-overlapping channels available.

---

## F-03 · Medium · Access point density exceeds coverage requirement

**Observation.** At a typical location the survey device heard 7.3 library radios on
2.4 GHz and 6.6 on 5 GHz at −75 dBm or stronger, peaking at 13. Meanwhile 100% of
measured points met −67 dBm on 5 GHz with substantial margin.

**Corroborating evidence.** While associated at approximately −50 dBm, the client
reported a 96 Mbps transmit and 78 Mbps receive link rate against a device ceiling
of 433 Mbps. A link rate at a fifth of capability under near-ideal signal conditions
indicates contention, not a signal problem — measured independently of the beacon
survey.

**Probable cause.** Designed for coverage on a floor plan rather than tuned for a
multi-AP environment. High transmit power on every radio maximises overlap.

**Impact.** Every audible co-channel radio is another participant in the same
contention domain. Excess overlap also encourages sticky-client behaviour.

**Recommendation.** Reduce transmit power so cells overlap by roughly 15–20% rather
than blanketing each floor. **Do not add access points** — the data does not support
a coverage-gap argument.

---

## F-04 · Medium, unverified · Hidden BSSIDs advertise a legacy security capability

**Observation.** Eighteen BSSIDs on the library's access points broadcast with no
SSID and a capability string Android renders as WEP. The eighteen visible
`Plainsboro-Public-Library` BSSIDs report no encryption at all, and Android's own
network list labelled the public network "Security: weak".

**Probable cause — read carefully.** Android reports WEP when a beacon sets the
privacy bit without carrying a modern RSN information element. That is consistent
with genuine WEP, but *also* with certain captive-portal and legacy configurations.
**This finding is not confirmed and must not be presented as fact without
verification by someone with administrative access.**

**Impact if confirmed.** WEP has been considered broken since 2001. Separately,
hiding an SSID is not a security control — all eighteen non-broadcast BSSIDs were
enumerated passively during a routine survey.

**Recommendation.** Have the library's IT provider verify the actual configuration.
If WEP is in use anywhere, migrate to WPA2 or WPA3. Consider Enhanced Open (OWE) for
the public network, which encrypts guest traffic without requiring a password.

---

## F-05 · Medium · Coverage propagates freely between floors

**Observation.** Six of eight APs are audible on both floors at −67 dBm or better.
Three whose strongest readings are on the second floor still reach −42, −46 and
−49 dBm on the ground floor, across essentially every ground floor point. The
strongest ground floor AP (−30 dBm) is still heard at −51 dBm throughout the second.

**Probable cause.** The building's central volume is a double-height reading room
open between levels — confirmed visually during the survey and consistent with the
architect's cross section. A vertical void propagates signal between floors far more
freely than the surrounding floor slabs.

**Impact.** The floors are not separate RF domains. Channel planning that treats
each floor independently will produce co-channel conflicts between vertically
adjacent APs, and the overlap feeds directly into F-03. Clients near the void see a
large set of similarly-strong candidates, encouraging sticky-client behaviour.

**Recommendation.** Plan channels for the building as a single three-dimensional
volume. When applying F-01 and F-02, ensure vertically adjacent APs near the atrium
differ in channel, not merely their same-floor neighbours.

---

## F-06 · Low · External RF environment is active but not dominant

**Observation.** Thirty-plus non-library networks detected, strongest at −51 dBm:
four printers broadcasting direct-connect SSIDs, at least three personal phone
hotspots, `xfinitywifi`, and several residential networks.

**Probable cause.** The architect's site plan places attached single-family
residences directly adjacent to the building. Printer direct-connect radios are
commonly left enabled by default.

**Impact.** Modest additional 2.4 GHz contention on channels 1 and 6, where the
residential networks also sit. Not a primary driver of F-01 or F-02, which are
internal.

**Recommendation.** Disable direct-connect radios on library printers that are
network-attached anyway. Factor neighbouring channel usage into the 2.4 GHz replan.

---

## Recommendations by effort

| Tier | Action | Addresses |
|---|---|---|
| **No cost — configuration** | Rebalance 2.4 GHz across 1/6/11 · Move 5 GHz into UNII-1 and UNII-2 · Standardise channel width · Reduce transmit power · Plan channels as one 3D volume · Disable printer direct-connect radios | F-01–F-03, F-05, F-06 |
| **No cost — verification** | Confirm the security configuration of the non-broadcast BSSIDs · Confirm whether any 6 GHz equipment exists | F-04 |
| **Low cost** | Evaluate Enhanced Open (OWE) for the public network | F-04 |
| **Not recommended** | Adding access points. No measured coverage deficit exists; more radios would worsen F-01 to F-03. | — |
