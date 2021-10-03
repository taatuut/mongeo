# mongeo
Code for working with geo data in MongoDB

# introduction

## OpenStreetMap - OSM

Get some data in protobuf format. Start small with `Antarctica` first, download some `*.osm.pbf` from https://download.geofabrik.de/

Get gdal with `brew install gdal` or similar.

Use `ogrinfo` to check geometry types in source (pdf) file

`ogrinfo north-america-latest.osm.pbf`

Displays overview of geometry types.

```
INFO: Open of `north-america-latest.osm.pbf'
      using driver `OSM' successful.
1: points (Point)
2: lines (Line String)
3: multilinestrings (Multi Line String)
4: multipolygons (Multi Polygon)
5: other_relations (Geometry Collection)
```

Run `ogr2ogr` to convert to geosjosn.

`ogr2ogr antarctica-latest.osm.json antarctica-latest.osm.pbf points`

Above command extracts points and creates a json file with a FeatureCollection, you can similar for lines or polygons or multi* geometries

(Always) use GeoJSONSeq as output format, gives you just one geojson feature per line ready for import. So you avoid all kind of jq/cat/split/for..do preparation and processiong scenarios later on.

So instead of pervious command better use

`ogr2ogr -f GeoJSONSeq antarctica-latest.osm.json antarctica-latest.osm.pbf points`

You can do much more with `ogr2ogr` to only extract the data you want, add fields etc etc.

When happy with your workflow repeat for other files and add these to same collection in MongoDB to build an even bigger data set.

Repeat for the 10GB for North America at https://download.geofabrik.de/

Check with Compass or QGIS (see repo https://github.com/taatuut/mongeovisqgis)

### Tips

Use `-explodecollections` on multi* geometries like `multipolygons` to get one feature per document.

Storing multiple geometry types in one collection works (and can make sense), but depending on client (GIS) system might add some extra configuration/limitations.

Use `-skipfailures` when you just wat a set and don't care about erroneous features

`ogr2ogr -explodecollections -skipfailures -f GeoJSONSeq north-america-latest.osm.json north-america-latest.osm.pbf multipolygons`

`mongoimport --uri mongodb://127.0.0.1:27017/test --collection polygons --file north-america-latest.osm.json --type json`

2021-10-03T16:05:56.932+0200	[###.....................] test.polygons	5.80GB/38.7GB (15.0%)
2021-10-03T16:05:59.177+0200	error inserting documents: BSONObj size: 30477049 (0x1D10AF9) is invalid. Size must be between 0 and 16793600(16MB) First element: insert: "polygons"
2021-10-03T16:05:59.932+0200	[###.....................] test.polygons	5.86GB/38.7GB (15.1%)

A 30Mb polygon fails to insert...

Loading the resulting 40Gb file with 66M records takes hours on my laptop... Better prep some decent Atlas environment

Create 2dsphere index fails

```
Index build failed: 165d1184-51f1-481a-90db-6310439343ba: Collection test.polygons ( a57eabdd-a73e-485b-8100-932bd278c791 ) :: caused by :: Can't extract geo keys:
{ _id: ObjectId('6159b67cfb9ff107f19ed0d4'), type: "Feature", properties: { osm_id: "8027", type: "multipolygon", natural: "water", other_tags: ""water"=>"river"" }, geometry: { type: "Polygon", coordinates: [ [ [ -137.3265104, 62.7841879 ], [ -137.3347999, 62.785039 ], [ -137.340364, 62.7827365 ], [ -137.3421156, 62.7821497 ], [ -137.342577, 62.7821228 ], [ -137.3428935, 62.782032 ], [ -137.3442786, 62.7816459 ], [ -137.3451144, 62.7816615 ], [ -137.3453611, 62.7817376 ], [ -137.3456347, 62.7818332 ], [ -137.3458117, 62.7819019 ], ...
```

Is this is about speed and limiting polygon size versus accuracy, add `-simplify .1` to the `ogr2ogr` command

`ogr2ogr -explodecollections -skipfailures -simplify .1 -makevalid -f GeoJSONSeq north-america-latest.osm.json north-america-latest.osm.pbf multipolygons`

# todo

* Compare input json file size and MongoDB size
* View in QIS

# other

```
brew install pigz
pigz -9 -k north-america-latest.osm.json
```

# links

https://www.compose.com/articles/how-to-transform-and-use-openstreetmap-data-into-geojson-using-gdal/
https://github.com/jgoodall/us-maps
