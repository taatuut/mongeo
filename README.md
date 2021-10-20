# mongeo
Code for working with geo data in MongoDB

# introduction

## OpenStreetMap - OSM

### 20211014

Prep test

```
rm -rf /tmp/data/test
mkdir -p /tmp/data/test
mongod --dbpath /tmp/data/test
```

### Metadata

mgeneratejs metadata.json -n 10 | mongoimport --uri mongodb://127.0.0.1:27017/test --collection metadata

#### OSM Gelderland polygons

Use Compass to connect to `localhost` and create database `test`, with collection `osm-gelderland-latest-polygons` and 2dsphere index on field `geometry`

In Compass `mongosh`

```
use test

db.getCollection('osm-gelderland-latest-polygons').drop()
db.createCollection('osm-gelderland-latest-polygons')
db.getCollection('osm-gelderland-latest-polygons').createIndex( { "geometry" : "2dsphere" } )
```

Alternative to remove documents without deleting collection hence indexes. Can be slower.

```
db.getCollection('osm-gelderland-latest-polygons').deleteMany({})
```

TEST
db.getCollection('polygons').deleteMany({})
db.getCollection('polygons').createIndex( { "geometry" : "2dsphere" } )

https://jira.mongodb.org/browse/TOOLS-2971

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection polygons --type json --file antarctica-latest-polygons.seq.osm.json 2>&1 | tee antarctica-import.txt

In terminal

```
use test

db.getCollection('osm-gelderland-latest-polygons').drop()
db.createCollection('osm-gelderland-latest-polygons')
db.getCollection('osm-gelderland-latest-polygons').createIndex( { "geometry" : "2dsphere" } )
```

```
cd data
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 gelderland-latest-polygons.simplify.osm.json gelderland-latest.osm.pbf multipolygons
ogr2ogr -explodecollections -skipfailures gelderland-latest-polygons.explode.osm.json gelderland-latest-polygons.simplify.osm.json multipolygons
ogr2ogr -f GeoJSONSeq gelderland-latest-polygons.seq.osm.json gelderland-latest-polygons.explode.osm.json

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection osm-gelderland-latest-polygons --type json --file gelderland-latest-polygons.seq.osm.json 2>&1 | tee osm-gelderland-latest-polygons-import.txt
```

Open QGIS and add polygon layer

#### OSM Gelderland points

```
use test

db.getCollection('osm-gelderland-latest-points').drop()
db.createCollection('osm-gelderland-latest-points')
db.getCollection('osm-gelderland-latest-points').createIndex( { "geometry" : "2dsphere" } )
```

```
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 gelderland-latest-points.simplify.osm.json gelderland-latest.osm.pbf points
ogr2ogr -explodecollections -skipfailures gelderland-latest-points.explode.osm.json gelderland-latest-points.simplify.osm.json points
ogr2ogr -f GeoJSONSeq gelderland-latest-points.seq.osm.json gelderland-latest-points.explode.osm.json

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection osm-gelderland-latest-points --type json --file gelderland-latest-points.seq.osm.json 2>&1 | tee osm-gelderland-latest-points-import.txt
```

Open QGIS and add points layer
Open Compass and add points layer

#### OSM Antarctica polygons

```
use test

db.getCollection('osm-antarctica-latest-polygons').drop()
db.createCollection('osm-antarctica-latest-polygons')
db.getCollection('osm-antarctica-latest-polygons').createIndex( { "geometry" : "2dsphere" } )
```

```
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 antarctica-latest-polygons.simplify.osm.json antarctica-latest.osm.pbf multipolygons
ogr2ogr -explodecollections -skipfailures antarctica-latest-polygons.explode.osm.json antarctica-latest-polygons.simplify.osm.json multipolygons
ogr2ogr -f GeoJSONSeq antarctica-latest-polygons.seq.osm.json antarctica-latest-polygons.explode.osm.json

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection osm-antarctica-latest-polygons --type json --file antarctica-latest-polygons.seq.osm.json 2>&1 | tee osm-antarctica-latest-polygons-import.txt
```

#### OSM Antarctica points

```
use test

db.getCollection('osm-antarctica-latest-points').drop()
db.createCollection('osm-antarctica-latest-points')
db.getCollection('osm-antarctica-latest-points').createIndex( { "geometry" : "2dsphere" } )
```

```
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 antarctica-latest-points.simplify.osm.json antarctica-latest.osm.pbf points
ogr2ogr -explodecollections -skipfailures antarctica-latest-points.explode.osm.json antarctica-latest-points.simplify.osm.json points
ogr2ogr -f GeoJSONSeq antarctica-latest-points.seq.osm.json antarctica-latest-points.explode.osm.json

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection osm-antarctica-latest-points --type json --file antarctica-latest-points.seq.osm.json 2>&1 | tee osm-antarctica-latest-points-import.txt
```

#### OSM North-America polygons

osm-north-america-latest-polygons
north-america-latest-polygons.seq.osm.json

```
use test

db.getCollection('osm-north-america-latest-polygons').drop()
db.createCollection('osm-north-america-latest-polygons')
db.getCollection('osm-north-america-latest-polygons').createIndex( { "geometry" : "2dsphere" } )
```

```
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 north-america-latest-polygons.simplify.osm.json north-america-latest.osm.pbf multipolygons
ogr2ogr -explodecollections -skipfailures north-america-latest-polygons.explode.osm.json north-america-latest-polygons.simplify.osm.json multipolygons
ogr2ogr -f GeoJSONSeq north-america-latest-polygons.seq.osm.json north-america-latest-polygons.explode.osm.json

../tools/mongoimport --uri mongodb://127.0.0.1:27017/test --collection osm-north-america-latest-polygons --type json --file north-america-latest-polygons.seq.osm.json

Do not store 'invalid' data, will be quite big...

 2>&1 | tee osm-north-america-latest-polygons-import.txt
```

head -n 10 north-america-latest.simplify.osm.json
head -n 10 north-america-latest.explode.osm.json
head -n 5 north-america-latest.seq.osm.json

QGIS fails for north-america polygons layer, too big RAM exhausted

`Error: cursor id 2392613233041230777 not found, full error: {'ok': 0.0, 'errmsg': 'cursor id 2392613233041230777 not found', 'code': 43, 'codeName': 'CursorNotFound'}`

### Notes

Following command fails... does not like - in name?

`db.osm-gelderland-latest-polygons.deleteMany({})`

Works

`db.getCollection('osm-gelderland-latest-polygons').deleteMany({})`

Download OSM data in pbf format from https://download.geofabrik.de/

### 20211005

```
rm -rf /tmp/data/db
mkdir -p /tmp/data/db
mongod --dbpath /tmp/data/db
```

`ogrinfo antarctica-latest.osm.pbf`

Cannot do all `ogr2ogr` stuff in one pass, eg using command below does not `explodecollections` for `multi*` geometeries. Always good to check resulting file with `ogrinfo`.

`ogr2ogr -explodecollections -skipfailures -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 -f GeoJSONSeq north-america-latest.osm.json north-america-latest.osm.pbf multipolygons`

Instead do multiple steps (and remove temp in between files after confirming successful display in QGIS)

```
ogr2ogr -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 antarctica-latest.simplify.osm.json antarctica-latest.osm.pbf multipolygons

ogr2ogr -explodecollections -skipfailures antarctica-latest.explode.osm.json antarctica-latest.simplify.osm.json multipolygons

ogr2ogr -f GeoJSONSeq antarctica-latest.seq.osm.json antarctica-latest.explode.osm.json
```

`mongoimport --uri mongodb://127.0.0.1:27017/test --collection polygons --type json --file antarctica-latest.seq.osm.json 2>&1 | tee antarctica-import.txt`

Other help commands

```
history | cut -c 8-

db.polygons.deleteMany({})

db.polygons.deleteOne( { "_id" : ObjectId("615c627e02e90dfb3d807556") } )

Error: Check geometry in object 615c627e02e90dfb3d8078cc or 615c627e02e90dfb3d807557

db.polygons.findOne( { "_id" : ObjectId("615c627e02e90dfb3d8078cc") } )

db.polygons.find( { 'properties.place': { $exists: false } } ).count()


db.polygons.findOne( { 'properties.place': { $exists: false } } )
{
        "_id" : ObjectId("615c561502e90dfb3d7f6c00"),
        "type" : "Feature",
        "properties" : {
                "osm_id" : "977615",
                "name" : "Site of Special Scientific Intrest No. 8",
                "type" : "multipolygon",
                "leisure" : "nature_reserve"
        },
        "geometry" : {
                "type" : "Polygon",
                "coordinates" : [
                        [
                                [
                                        -58.465,
                                        -62.2359
                                ],
                                [
                                        -58.4652,
                                        -62.2363
                                ],
                                [
                                        -58.4648,
                                        -62.2363
                                ],
                                [
                                        -58.4648,
                                        -62.2359
                                ],
                                [
                                        -58.465,
                                        -62.2359
                                ]
                        ]
                ]
        }
```

_Questions_

Q:
QGIS 3 on Mac OS - Where is the configuration directory?

A:
From the QGIS Settings Menu, User Profiles, select "Open active profile folder". You'll be taken straight there.

_Tips_

One tip (shot myself in the foot first): create the 2dsphere index upfront, then run the mongoimport on a (huge) json file. It will then all but the 'invalid' geojson. If you do it the other way around you get into a loop of index creation breaking on failing documents one by one which can be a pita with huge sets.

### 20211004

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

Is this is about speed and limiting polygon size versus accuracy, add `-simplify` and `COORDINATE_PRECISION` to the `ogr2ogr` command

`ogr2ogr -explodecollections -skipfailures -simplify .1 -makevalid -lco COORDINATE_PRECISION=4 -f GeoJSONSeq north-america-latest.osm.json north-america-latest.osm.pbf multipolygons`

# todo

* Compare input json file size and MongoDB size
* View in QIS

# other

```
brew install pigz
pigz -9 -k north-america-latest.osm.json
```

# links

How to convert `other_tags` info from OSM source format to geojson

https://www.compose.com/articles/how-to-transform-and-use-openstreetmap-data-into-geojson-using-gdal/

UHow to query OSM source for specific info during conversion

https://www.compose.com/articles/how-to-transform-and-use-openstreetmap-data-into-geojson-using-gdal/

Working with US Tiger data

https://github.com/jgoodall/us-maps
