# Calendar Rewind Travel Data

`travel_places.sqlite` is a derived database of populated places, countries, regions, and airports. Calendar Rewind, a free iOS app for looking back through events saved in Apple Calendar, bundles this same file and uses it on device as a place and airport lookup when recognizing travel plans. The database is published here, on its own. GeoNames-sourced portions are under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). Airport records are a separate source: OurAirports releases its data to the public domain, and the [datasets/airport-codes](https://github.com/datasets/airport-codes) compilation is dedicated under the [Open Data Commons Public Domain Dedication and License 1.0](https://opendatacommons.org/licenses/pddl/1.0/). This repository does not apply one license to the whole mixed file. This repository contains the database and these notes.

Place, country, and region data come from the GeoNames cities5000 dump, `countryInfo.txt`, and `admin1CodesASCII.txt`, downloaded from [https://download.geonames.org/export/dump/](https://download.geonames.org/export/dump/). Airport data come from the datasets/airport-codes compilation of [OurAirports](https://ourairports.com/data/) data. Those inputs were selected, filtered, and converted into this SQLite lookup. This is an adapted dataset, not an untouched GeoNames dump and not an untouched OurAirports file. Non-Latin alternate spellings were left out of name search. The database keeps large airports, medium airports, and other facilities that have an IATA code. Source filenames and origin URLs are stored in the database table `sources`.

## Attribution and license

Place names and coordinates are derived from [GeoNames](https://www.geonames.org/).

The GeoNames extract is used under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). Records come from the GeoNames [cities5000](https://download.geonames.org/export/dump/cities5000.zip) dump, [countryInfo.txt](https://download.geonames.org/export/dump/countryInfo.txt), and [admin1CodesASCII.txt](https://download.geonames.org/export/dump/admin1CodesASCII.txt). They were filtered to populated places and transformed into a lookup database. GeoNames provides the data "as is", without a warranty of accuracy, timeliness, or completeness. This product is not endorsed by GeoNames.

Airport facility records are derived from the [datasets/airport-codes](https://github.com/datasets/airport-codes) compilation of [OurAirports](https://ourairports.com/data/) data. This is not an untouched OurAirports file, and these rows are not GeoNames CC BY data. OurAirports releases its database to the public domain and asks for credit as a courtesy. The datasets/airport-codes maintainers dedicate their selection and formatting under the [Open Data Commons Public Domain Dedication and License 1.0](https://opendatacommons.org/licenses/pddl/1.0/). OurAirports and the datasets/airport-codes maintainers provide the data with no guarantee of accuracy or fitness for use.

Not endorsed by, or affiliated with, OurAirports, IATA, or ICAO. IATA and ICAO may claim rights in their code lists; those terms were not resolved from the source repositories.

These notices apply only to `travel_places.sqlite`. They do not license the Calendar Rewind application source.

## What is in the database

`travel_places.sqlite` is a geographic lookup. A name match does not confirm a trip. A linked airport city is a municipality-name match, not proof that a traveler visited that city.


| Table          | Role                                                                                          |
| -------------- | --------------------------------------------------------------------------------------------- |
| `places`       | Populated place: name, country code, admin1 code, coordinates, and population                |
| `place_names`  | Normalized lookup keys (`canonical`, `ascii`, or `alternate`)                                 |
| `place_lookup` | View joining those keys to place, country, and region names                                   |
| `countries`    | Country display names                                                                         |
| `admin1`       | First-level region display names                                                              |
| `airports`     | Airport and other facility rows, including an IATA code when the source had one              |
| `sources`      | Input filenames, origin URLs, and license notes recorded when the file was created           |


The app looks up a place by a normalized name and an airport by IATA code. Names are case folded, marks stripped, and punctuation turned into spaces. `sources` records the input files and their licenses.

## Provenance

The GeoNames dump was downloaded from [https://download.geonames.org/export/dump/](https://download.geonames.org/export/dump/):

- [cities5000.zip](https://download.geonames.org/export/dump/cities5000.zip)
- [countryInfo.txt](https://download.geonames.org/export/dump/countryInfo.txt)
- [admin1CodesASCII.txt](https://download.geonames.org/export/dump/admin1CodesASCII.txt)

Airport rows come from the [datasets/airport-codes](https://github.com/datasets/airport-codes) compilation of OurAirports data: [https://ourairports.com/data/](https://ourairports.com/data/)

`sources` records those filenames and origin URLs. The airport input was the datasets/airport-codes compilation, `airports.csv`, not the untouched OurAirports CSV.

## Download

Clone this repository, or download `travel_places.sqlite` from GitHub. The file is about 14 MB (`15020032` bytes).

SHA-256:

```text
8eb1566b6488437aff9f44981b01913cde86d1634c9213f36203e6dcd37ab604
```

To confirm a copy matches:

```sh
shasum -a 256 travel_places.sqlite
```
