# Niš Public Transport — interactive map

Interactive, poster-grade map of the public transport network of **Niš**:
62 city and suburban lines of JGSP Niš-Ekspres, out to Niška Banja, Malča and
the villages of the Nišava valley — 702 stops, 1 647 km.

## Live

Local build on port 8172 (`npm run serve`).

The feed is generated from JGSP's own timetables by **Nedžad Beus's
gtfs-generators on GitLab** — the source Transitous uses for Serbia — and
published as a generic package with a `latest` pointer.

| mode | route_type | graph |
|---|---|---|
| buses | 3 | OSM roadways |

**Two feed habits had to be answered here.**

1. There is ONE ROUTE ROW PER PATTERN — 195 rows for 62 line numbers — and the
   row's long name is that pattern's own "A - B". That turned out to be a gift,
   because the trips carry no headsign at all and `direction_id` is 0 on every
   one of them: without a destination the engine would key both directions the
   same and draw only the longer one. A small `headsignOf` hook reads the
   destination out of the route row instead, which is where this feed actually
   keeps it.
2. Every pole is named with its direction in tow — "22. decembar ( ka centru )",
   "Ledena Stena (od centra)". That is the direction, not the name, so the tail
   comes off. "(ulaz)" stays: that one really does tell two poles apart.

## Pipeline

`npm run download` fetches the feed and cuts the OSM extract. **The OSM
data comes from Geofabrik, not Overpass** — the public mirrors were answering
504 to every request on the day this map was built, even for a single small
city box — so `pipeline/pbf-tiles.py` (needs `pip3 install --user osmium`)
clips the tiles out of `serbia-latest.osm.pbf`, writing exactly the JSON shape Overpass would
have returned, node ids included.

`npm run build` map-matches every line (HMM/Viterbi on the OSM graph) and
writes GeoJSON to `data/out/`; `npm run lines` adds the line-by-line view.
`npm run serve` hosts the map at <http://localhost:8172>.

Data: JGSP Niš-Ekspres via gtfs-generators ·
base map © OpenFreeMap / OpenMapTiles / OpenStreetMap contributors.
