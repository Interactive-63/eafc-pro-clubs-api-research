# Raw Player Data

This directory contains raw player statistics collected from EA Sports FC 27 Pro Clubs games through the EA API using a script.

## Data

The csv contains 51,395 rows of player data that was used for identifying correlation between events/event clusters and stats that could be confirmed.  

Each row represents a player's statistics for a match. The dataset contains match identifiers, timestamps, club and player information, standard player statistics, calculated percentages and raw event counters.

The event columns represent raw numeric event counters returned by the API. Not every possible event ID is present in the dataset - only what was present in the data that was scraped.

## Limitations

* The dataset represents collected EA FC 27 matches and may not cover every possible match situation or event.
* Some event IDs may be absent because they did not occur in the collected matches.
* Some statistics may be incomplete or affected by undocumented API behaviour.
* Findings derived from this dataset may not apply to other EA FC releases.

## Disclaimer

This dataset was collected from the EA FC 27 API for research purposes. This repository is not affiliated with or endorsed by EA Sports.
