# EA FC Pro Clubs API Research

An independent, community-maintained reference for the EA Sports FC Pro Clubs API. This repository documents discovered endpoints, response structures, event IDs, player statistics and other findings from researching the game's API.

The aim is to make the available data easier to understand and give developers a central place to share and verify their own findings.

This is an ongoing research project. Some information has been extensively validated, while other findings are still being investigated.

## Contents

* [About](#about)
* [Disclaimer](#disclaimer)
* [Supported games and platforms](#supported-games-and-platforms)
* [API endpoints](#api-endpoints)
* [Event IDs and player statistics](#event-ids-and-player-statistics)
* [Decoded stat formulas](#decoded-stat-formulas)
* [Contributing](#contributing)

## About

EA FC Pro Clubs exposes a range of data through its online API. However, the API is largely undocumented, and many of its response fields and event IDs have no publicly available descriptions.

This repository aims to document that information through direct API observations, statistical analysis and independent verification.

The information is intended for developers building applications, Discord bots, statistical tools and other projects that use Pro Clubs data.

## Disclaimer

This is an independent, community-maintained research project. It is not affiliated with, endorsed by or maintained by EA Sports.

The API is undocumented and may change without notice. Endpoint availability, response structures and event mappings may vary between game releases.

All findings should be treated as observations of the API rather than official EA documentation unless explicitly stated otherwise.

## Supported games and platforms

| Game            | Status              | Notes                                  |
| --------------- | ------------------- | -------------------------------------- |
| EA Sports FC 27 | Active research     | Current focus of the project           |

The API platform identifier observed for the current generation is `common-gen5`.

API responses and endpoint availability should be verified against the specific game being investigated. Findings from previous releases may not apply to newer versions.

## API endpoints

The following endpoints have been identified during research. They are undocumented EA endpoints and may change or become unavailable.

## Raw data endpoints
These endpoints are for raw data returned in JSON format.
All endpoints below use the `https://proclubs.ea.com/api/fc` base path. 

| Endpoint | Parameters |
|---|---|
| `/allTimeLeaderboard/search` | `platform`, `clubName`, `maxResultCount` |
| `/currentSeasonLeaderboard/search` | `platform`, `clubName`, `maxResultCount` |
| `/club/playoffAchievements` | `platform`, `clubId` |
| `/clubs/info` | `platform`, `clubIds` |
| `/clubs/overallStats` | `platform`, `clubIds` |
| `/clubs/matches` | `platform`, `clubIds`, `matchType`, `maxResultCount` |
| `/members/career/stats` | `platform`, `clubId` |
| `/members/stats` | `platform`, `clubId` |

Note the specific usages of `club` vs `clubs` and `clubId` vs `clubIds`.

## PNG asset endpoints
These below endpoints are for club crest and other png assets.

| Endpoints | Parameters |
|---|---|
| `https://eafc24.content.easports.com/fifa/fltOnlineAssets/24B23FDE-7835-41C2-87A2-F453DFDB2E82/2024/fcweb/crests/256x256/l{param}.png` | `teamId` for real badges OR `crestAssetId` for custom badges (See note below) |
| `https://media.contentapi.ea.com/content/dam/eacom/fc/pro-clubs/notfound-crest.png` | None |
| `https://media.contentapi.ea.com/content/dam/eacom/fc/pro-clubs/reputation-tier{param}.png` | `0`, `1`, `2`, or `3` | 
| `https://media.contentapi.ea.com/content/dam/ea/fc/fc-24/common/features/fc24-hero-md-welcome-to-clubs-light-bg-7x2-xl.jpg.adapt.{param}w.jpg` | `768`, `1024`, `1456`, or `1920`  | 
| `https://media.contentapi.ea.com/content/dam/eacom/fc/pro-clubs/divisioncrest{param}.png` | `1`, `2`, `3`, `4`, `5`, or `6`| 

Note: 
For getting the correct club crest for a team programatically, use the `TEAM` variable in a club's info within the call to the match endpoint. In *most* cases, the `TEAM` variable will equal `teamId` if the club is using a real-life badge and will equal `crestAssetId` if they are using a custom badge.
In rare cases, all three are different and searching for the crest using `TEAM` will not return anything. Either fall back to the `notfound-crest` or fallback to `crestAssetId`.

## Event IDs and player statistics

The API includes numeric event IDs that represent different in-game actions and statistics.

The event data can be compared against named player statistics to identify relationships and, in some cases, reconstruct the formulas used to calculate displayed statistics.

Not every event ID represents a standalone statistic. Some are components of a larger statistic, while others may be overlays, subcategories or counters that only appear in certain circumstances.

The current research separates findings into three groups:

* **Confirmed or high-confidence mappings:** relationships that have been validated against a substantial dataset or independently confirmed.
* **Partial mappings:** relationships that are supported by the data but do not account for every instance.
* **Exploratory findings:** correlations and hypotheses that require further testing.

## Decoded stat formulas

Each player row in the `/clubs/matches` response carries four fields, `match_event_aggregate_0` to `match_event_aggregate_3`. Each is a comma-separated list of `event_id:count` pairs. An event ID that is absent from every bucket has a count of `0`.

```json
"match_event_aggregate_0": "0:2,109:3,112:3,121:2,145:5,157:3,164:2,174:19,215:21,216:5,217:1,24:17,25:5,26:1,30:8,31:3,32:3,34:10,35:2,36:3,6:1",
"match_event_aggregate_1": "97:10",
"match_event_aggregate_2": "",
"match_event_aggregate_3": ""
```

Here `24:17` means `event_24` fired 17 times for that player in that match. The tables below refer to these as `event_N`. Where a formula uses more than one event, the arithmetic is shown in the Event column.

### Confirmed / high confidence

| Stat | Event | Notes |
|---|---|---|
| Passes completed | `event_215`| Excludes offside passes |
| Passes failed | `event_216` | |
| Passes completed (forward) | `event_30` | |
| Passes completed (backward) | `event_32` | |
| Passes completed (sideways) | `event_34` | |
| Passes completed (direction unknown) | Passes completed − (`event_30` + `event_32` + `event_34`), minimum 0 | Derived. The 3 direction events sum to Passes completed 98.0% |
| Passes completed (short) | `event_24` | |
| Passes completed (medium) | `event_26` | |
| Passes completed (long) | `event_28` | |
| Passes completed (cross) | `event_36` | Includes set-piece crosses |
| Passes completed (length unknown) | Passes completed − (`event_24` + `event_26` + `event_28` + `event_36`), minimum 0 | Derived. The 4 length events sum to Passes completed 97.7% |
| Passes failed (forward) | `event_31` | |
| Passes failed (backward) | `event_33` | |
| Passes failed (sideways) | `event_35` | |
| Passes failed (direction unknown) | Passes failed − (`event_31` + `event_33` + `event_35`), minimum 0 | Derived. The 3 direction events sum to Passes failed 77.5% |
| Passes failed (short) | `event_25` | |
| Passes failed (medium) | `event_27` | |
| Passes failed (long) | `event_29` | |
| Passes failed (cross) | `event_37` | Open play only |
| Passes failed (length unknown) | Passes failed − (`event_25` + `event_27` + `event_29` + `event_37`), minimum 0 | Derived. |
| First time passes | `event_143` | |
| Switch of play passes | `event_144` | |
| Fancy/flair passes | `event_147` | |
| Through passes | `event_152` | Successful through balls |
| Shots | `event_217 + event_218` | |
| Shots on target | `event_217` = `event_13 + event_18` | Blocked shots can fold in.|
| Shots on target (inside box) | `event_13` | |
| Shots on target (outside box) | `event_18` | |
| Shots off target | `event_218` = `event_14 + event_19` | |
| Shots off target (inside box) | `event_14` | |
| Shots off target (outside box) | `event_19` | |
| Shots saved | `event_202` | |
| Goals | `event_214` | |
| Goals (first-time shot) | `event_128` | Describes a goal. Does not sum to goal total. |
| Goals (weak foot) | `event_131` | Describes a goal. Does not sum to goal total. |
| Goals (off the post) | `event_136` | Describes a goal. Does not sum to goal total. |
| Assists | `event_11` | |
| Second assists | `event_115` | |
| Assists from through balls | `event_118` | Describes an assist. Does not sum to assist total. |
| Good direction saves | `event_267` | |
| Dribbles completed | `event_174` | |
| Dribbles carried | `event_97` | |
| Dribble style: Beat | `event_112 − event_38` | `event_112` is Beat + Skill Move Beat |
| Dribble style: Skill move beat | `event_38` | |
| Offside passes | `event_153` | |
| Standing tackles won | `event_229` | |
| Sliding tackles won | `event_230` | |
| Tackles won | `event_0` | |
| Tackles lost | `event_1` | |
| Dangerous tackles | `event_163` | |
| Successful tackles on the ball / clean tackles | `event_164` | |
| Interceptions | `event_6` | |
| Opponent dispossessed | `event_158` | |
| Yellow card (total) | `event_95` + `event_213` | |
| Yellow card (immediate) | `event_95` | Yellow card when play stops immediately |
| Yellow card (delayed) | `event_213` | Yellow card when play continues with advantage and is brought back |
| Fouls conceded (total) | `event_2` + `event_3` | |
| Fouls conceded | `event_2` | |
| Fouls conceded (defensive third) | `event_3` | Approximately defensive third occuring |
| Fouls won | `event_4` | That player was fouled |
| Free kicks for bad tackles? | `event_171` | High confidence in what causes the event but the name of it is a bit *eh*|
| Penalties conceded | `event_94` | |
| Corners conceded | `event_10` | |
| Corners attempted | `event_145` | |
| Crosses blocked | `event_156` | |
| Possession lost (defensive third) | `event_105` | |
| Possession lost (middle third) | `event_106` | |
| Possession lost (attacking third) | `event_107` | |
| Possession won (defensive third) | `event_108` | |
| Possession won (middle third) | `event_109` | |
| Possession won (attacking third) | `event_110` | |
| Aerial duels won (attacking) | `event_265` | |
| Aerial duels won (defending) | `event_266` | |
| Out of position (1/5 severity) | `event_99` | |
| Out of position (2/5 severity) | `event_100` | |
| Out of position (3/5 severity) | `event_101` | |
| Out of position (4/5 severity) | `event_102` | |
| Out of position (5/5 severity) | `event_103` | |
| Out of position (Total) | `event_219` | Sum of `event_99` to `event_103` on all 51,395 rows |
| In position | `event_111` | Times the corner feedback told the player they had good positioning |
| Match Feedback (MF): Use the ball | `event_212` | MF refers to the top corner feedback after something happens to that player |
| MF: Pick your pass | `event_207` | |
| MF: No good option | `event_175` | |
| MF: Good option taken | `event_176` | |
| MF: Best option taken | `event_177` | |
| MF: Should have passed elsewhere | `event_182` | |
| MF: Chose to pass | `event_183` | |


### Partial mappings

| Stat | Event | Notes |
|---|---|---|
| Bad passes? | `event_151` | Hospital passes/poor attempts etc. 4/4 matches |
| `229` duplicate | `event_121` | 98.5% match with `229`, some tackle success indicator? |
| Crosses attempted | `event_157` | Open play only. Add `event_145` when the corner is crossed |
| Red card trigger/severe foul indicator | `event_96` | 82.7% precision, 16.6% coverage |
| Offsides (times caught) | `event_104` | Presence correct, count often under |
| Saves (sub-type) | `event_49` | ~18-20% of goalkeeper rows |
| Saves (sub-type) | `event_50` | ~18-20% of goalkeeper rows |
| Goals by technique | `event_12` | Tags overlap. 85.8% summed with `event_124`, `event_128`, `event_137` |
| Goals by technique | `event_124` | |
| Goals by technique | `event_137` | |
| Goals by technique | `event_140` | ~99% precision |
| Goals by technique | `event_93` | ~99% precision |
| Goals by technique | `event_238` | ~99% precision |
| Goals by technique (Chip) | `event_123` | ~99% precision |
| MF: Should have shot | `event_179` | 2/2 cases |
| Tiki taka playstyle pass? | `event_150` | 1/1 case, unsure of label |
| Tackles ending a dribble? | `event_159` | 1/1 case, unsure of label |


### Exploratory findings

Goal-technique tags found by sweeping for events that almost never fire without a goal. None is assigned to a named technique yet.

| Stat | Event | Notes |
|---|---|---|
| Goals by technique | `event_46` | ≥98.6% precision, 5.91% recall |
| Goals by technique | `event_141` | ≥98.6% precision, 6.38% recall |
| Goals by technique | `event_126` | ≥98.6% precision, 5.52% recall |
| Goals by technique | `event_17` | ≥98.6% precision, 3.65% recall |
| Goals by technique | `event_125` | ≥98.6% precision, 2.57% recall |
| Goals by technique | `event_15` | ≥98.6% precision, 3.18% recall |
| Goals by technique | `event_239` | ≥98.6% precision, 1.93% recall |
| Goals by technique | `event_138` | ≥98.6% precision, 1.85% recall |
| Goals by technique | `event_139` | ≥98.6% precision, 1.09% recall |
| Goals by technique | `event_130` | ≥98.6% precision, 0.71% recall |
| Goals by technique | `event_135` | ≥98.6% precision, 0.77% recall |
| Goals by technique | `event_134` | ≥98.6% precision, 0.69% recall |
| Goals by technique | `event_203` | 81.1% precision |
| Goals by technique | `event_21` | 66.1% precision |
| Goals by technique | `event_173` | 68.1% precision |
| Goals by technique | `event_194` | 62.3% precision |
| Goals by technique | `event_117` | 43.0% precision |
| Goals by technique | `event_48` | 100% precision, 1-124 total fires |
| Goals by technique | `event_132` | 100% precision, 1-124 total fires |
| Goals by technique | `event_122` | 100% precision, 1-124 total fires |
| Goals by technique | `event_199` | 100% precision, 1-124 total fires |
| Goals by technique | `event_168` | 100% precision, 1-124 total fires |


## Contributing

Contributions are welcome, particularly from developers who have independently investigated the API or identified additional event mappings.

You can contribute by:

* Identifying new API endpoints or response fields.
* Documenting event IDs and their potential meanings.
* Providing evidence for existing or proposed stat formulas.
* Identifying changes between game releases.
* Correcting inaccurate or outdated information.
* Improving the documentation and examples.

Please see [`CONTRIBUTING.md`](.github/CONTRIBUTING.md) before submitting a change.

For new event mappings, include the evidence supporting the proposed interpretation. A correlation or single match observation is useful as a starting point, but should not automatically be treated as a confirmed formula.

## Acknowledgements

If you use this research in your own project, attribution to this repository is appreciated.

---

*Last updated: September 2026*
