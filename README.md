# tile-reduce

[![Build Status](https://travis-ci.org/mapbox/tile-reduce.svg)](https://travis-ci.org/mapbox/tile-reduce)

*Note: This module is under heavy development and is changing fast*

[MapReduce](http://en.wikipedia.org/wiki/MapReduce) geoprocessing across tiles

## install

```sh
npm install tile-reduce
```

## example

This example takes a selection of OpenStreetMap roads from Mapbox Streets, buffers them, and pipes the output to tippecanoe.

### run

```sh
node index.js | tippecanoe -o buffer.mbtiles
```

### index.js

```js
var TileReduce = new require('tile-reduce');

var bbox = [
    -80.13702392578125,
    32.72721987021932,
    -79.75799560546875,
    32.936081249036604
  ];

var opts = {
  zoom: 15,
  tileLayers: [
      {
        name: 'streets',
        url: 'https://b.tiles.mapbox.com/v4/mapbox.mapbox-streets-v6/{z}/{x}/{y}.vector.pbf',
        layers: ['road']
      }
    ],
  map: __dirname+'/buffer.js'
};

var tilereduce = TileReduce(bbox, opts);

tilereduce.on('reduce', function(result){
  console.log(JSON.stringify(result));
});

tilereduce.run();
```

### buffer.js

```js
var turf = require('turf');

module.exports = function (tileLayers, opts, done){
  var road = tileLayers.streets.road;
  var bufferedRoad = turf.buffer(road, 20, 'meters');
  done(null, bufferedRoad);
}
```

## test

```sh
npm test
```
