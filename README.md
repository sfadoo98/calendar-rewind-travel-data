# Calendar Rewind Travel Data

This repository provides [`travel_places.sqlite`](travel_places.sqlite), an offline geographic reference database used by Calendar Rewind's Travel recognition. It holds populated places, country names, first-level administrative regions, and airports so the app can match place names and airport codes without a network service.

Download [`travel_places.sqlite`](travel_places.sqlite) (15,020,032 bytes). SHA-256: `8eb1566b6488437aff9f44981b01913cde86d1634c9213f36203e6dcd37ab604`.

`PRAGMA integrity_check` is `ok`. `PRAGMA user_version` is `2`. The file's `meta` table records schema version 2, data version 1, and the build profile `slim_latin_aliases_passenger_airports`.

A row in this file is reference data. It is not a record of anyone's calendar or travel.

## What the file contains

Counts below were queried from this file.

| Object | Rows | Contents |
| --- | ---: | --- |
| `places` | 69,816 | Populated places: GeoNames id, name, feature code, country code, admin1 code, latitude, longitude, population |
| `place_names` | 311,411 | Normalized lookup keys (`canonical`, `ascii`, or `alternate`) |
| `place_lookup` | view | Those keys joined to place, country, and admin1 names |
| `countries` | 252 | Country display names |
| `admin1` | 3,865 | First-level region display names |
| `airports` | 9,763 | Airport and other facility rows |
| `sources` | 4 | Input filenames and origin URLs recorded at build time |
| `meta` | — | Schema, profile, input sizes, and input SHA-256 values |
| `build_stats` | — | Accepted, omitted, and alias counts from the build |

Of the 9,763 airport rows, 9,051 have an IATA code (9,051 distinct codes) and 712 do not. Facility types present: large airports (1,176), medium airports (4,104), small airports (4,230), seaplane bases (153), and heliports (100).

Calendar Rewind opens the file read-only. It looks up a place by an exact normalized name, and it looks up an airport by IATA code in a separate query. Country and region names are used as labels. A name match does not decide that a calendar event is a trip, and a linked airport city is only a name association.

Name keys are built by Unicode NFKD, removing combining marks, casefolding, and turning other characters into spaces. `place_names` stores those keys, not a prefix or substring index.

## GeoNames

Place names, coordinates, country names, and first-level region names are derived from [GeoNames](https://www.geonames.org/).

The GeoNames portions are used under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

The downloadable GeoNames dump is published at [https://download.geonames.org/export/dump/](https://download.geonames.org/export/dump/). The inputs recorded in this database are:

| File | Origin recorded in `sources` |
| --- | --- |
| `cities5000.txt` | [cities5000.zip](https://download.geonames.org/export/dump/cities5000.zip) |
| `countryInfo.txt` | [countryInfo.txt](https://download.geonames.org/export/dump/countryInfo.txt) |
| `admin1CodesASCII.txt` | [admin1CodesASCII.txt](https://download.geonames.org/export/dump/admin1CodesASCII.txt) |

The GeoNames dump readme describes `cities5000.zip` as cities with population greater than 5,000, or seats of a first-order administrative division, and it licenses that work under CC BY 4.0. GeoNames provides the data "as is", without a warranty of accuracy, timeliness, or completeness. The export terms ask for credit to GeoNames. See the dump [readme.txt](https://download.geonames.org/export/dump/readme.txt) and the [export terms](https://www.geonames.org/export/).

This file is an adapted dataset, not an untouched GeoNames dump. The builder (`build_travel_places.py`) selected and converted those inputs:

- Every non-empty line of the local `cities5000.txt` extract was accepted (69,816 places). The builder keeps feature class `P` (populated places). No city row was skipped in this build.
- Stored place columns are the id, names, feature code, country code, admin1 code, coordinates, and population. Elevation, timezone, lower admin codes, and per-record modification dates are not stored.
- Country and admin1 names were loaded into their own tables. They were not inserted into the place-name lookup.
- Alternate spellings were filtered before name search. The file's build statistics record 206,456 non-Latin alternates omitted, 8,570 code-like three-letter alternates omitted, 4,214 short Latin alternates omitted, 38 numeric alternates omitted, and 1 overly long alternate omitted. Short official names can remain on the place row without becoming lookup keys (162 in this build).
- `alternateNamesV2.zip` was not used. The alternates that remain come from the unlabeled convenience column in `cities5000.txt`.

GeoNames does not sponsor or endorse Calendar Rewind.

## Airports

Airport rows come from the [datasets/airport-codes](https://github.com/datasets/airport-codes) compilation of [OurAirports](https://ourairports.com/data/) data. The origin URL recorded in this database is the compilation's [`airport-codes.csv`](https://raw.githubusercontent.com/datasets/airport-codes/master/data/airport-codes.csv). The upstream OurAirports list is [ourairports.com/data/airports.csv](https://ourairports.com/data/airports.csv).

This is not an untouched OurAirports file, and these rows are not GeoNames CC BY data.

OurAirports states that its downloadable data is released to the public domain, with no guarantee of accuracy or fitness for use, and that credit is requested but not required. See [OurAirports data](https://ourairports.com/data/).

The datasets/airport-codes maintainers dedicate their selection and formatting under the [Open Data Commons Public Domain Dedication and License 1.0 (PDDL)](https://opendatacommons.org/licenses/pddl/1.0/). Their published compilation combines latitude and longitude into one coordinates column and drops several OurAirports columns. The snapshot used for this database was that compilation. It was not re-checked against a later copy of the GitHub `master` file, which that project regenerates.

The builder then:

- Kept every `large_airport` and `medium_airport`, plus any other facility with a valid three-letter IATA code. The build statistics record 86,258 airport lines read and 76,495 omitted as not passenger under that rule.
- Split the compilation's coordinates field into latitude and longitude, and stored ident, type, name, IATA, ICAO, country, region, and municipality. Elevation, GPS code, and local airport code are not stored. A national or local code is not treated as an IATA code.
- Linked an airport to a place only when the municipality matched one canonical or ASCII place name in the same country. For the United States, Switzerland, Belgium, and Montenegro, a tie can be narrowed with the ISO admin1 code. Coordinates and population are not used to choose a city. This file has 5,084 linked, 193 ambiguous, and 4,486 unlinked airports. A link is not proof that a traveler visited that city.

Not endorsed by, or affiliated with, OurAirports, IATA, or ICAO. OurAirports does not sponsor or endorse Calendar Rewind.

IATA and ICAO may claim rights in their code lists. The datasets/airport-codes maintainers say those terms are unclear, and they also say they found no statement that clears OurAirports' own inputs. Those questions were not resolved for this database.

## Licensing scope

- GeoNames-sourced places, place names, countries, and admin1 names are under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- Airport rows follow the OurAirports public-domain release and the datasets/airport-codes [PDDL 1.0](https://opendatacommons.org/licenses/pddl/1.0/) dedication, with the unresolved code-list and upstream-input questions above.
- Those terms are not the same. This repository does not apply CC BY 4.0, or any other single license, to the whole mixed file.
- No license is declared here for Calendar Rewind application source. This notice covers `travel_places.sqlite` only.

## No guarantees

Geographic and airport data can be incomplete or outdated. GeoNames supplies its data "as is", without a warranty of accuracy, timeliness, or completeness. OurAirports and the datasets/airport-codes maintainers provide their data with no guarantee of accuracy or fitness for use.
