############
Visualiseren
############


*******
Leaflet
*******

WMS
===

TODO

WFS
===

TODO

TMS
===

.. code-block:: javascript
	:linenos:

	// Resoluties (pixels per meter) van de zoomniveaus:
	var res = [3440.640, 1720.320, 860.160, 430.080, 215.040, 107.520, 53.760, 26.880, 13.440, 6.720, 3.360, 1.680, 0.840, 0.420];

	// Juiste projectieparameters voor Rijksdriehoekstelsel (EPSG:28992):
	var RD = L.CRS.proj4js('EPSG:28992', '+proj=sterea +lat_0=52.15616055555555 +lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000 +ellps=bessel +units=m +towgs84=565.2369,50.0087,465.658,-0.406857330322398,0.350732676542563,-1.8703473836068,4.0812 +no_defs', new L.Transformation(1, 285401.920, -1, 903401.920));
	RD.scale = function(zoom) {
	    return 1 / res[zoom];
	};
	var map = new L.Map('map', {
	  continuousWorld: true,
	  crs: RD,
	  layers: [
	    new L.TileLayer('http://geodata.nationaalgeoregister.nl/tms/1.0.0/brtachtergrondkaart/{z}/{x}/{y}.png', {
	        tms: true,
	        minZoom: 3,
	        maxZoom: 13,
	        attribution: 'Kaartgegevens: © <a href="http://www.cbs.nl">CBS</a>, <a href="http://www.kadaster.nl">Kadaster</a>, <a href="http://openstreetmap.org">OpenStreetMap</a><span class="printhide">-auteurs (<a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>).</span>',
	        continuousWorld: true
	    })
	  ],
	  center: new L.LatLng(53.219231,6.57537),
	  zoom: 7
	});

Met dank aan `@emacgillavry <https://github.com/emacgillavry/PDOK-Leaflet/>`_.

**********
OpenLayers
**********

WMS
===

TODO

WFS
===

TODO

TMS
===

.. code-block:: javascript
	:linenos:

	va extent = [-285401.92,22598.08,595401.9199999999,903401.9199999999];
	var resolutions = [3440.640, 1720.320, 860.160, 430.080, 215.040, 107.520, 53.760, 26.880, 13.440, 6.720, 3.360, 1.680, 0.840, 0.420];
	var projection = new ol.proj.Projection({code:'EPSG:28992', units:'m', extent: extent});

	var url = 'http://geodata.nationaalgeoregister.nl/tms/1.0.0/brtachtergrondkaart/';

	var tileUrlFunction = function(tileCoord, pixelRatio, projection) {
	  var zxy = tileCoord;
	  if (zxy[1] < 0 || zxy[2] < 0) {
	    return "";
	  }
	  return url +
	    zxy[0].toString()+'/'+ zxy[1].toString() +'/'+
	    zxy[2].toString() +'.png';
	};

	var map = new ol.Map({
	  target: 'map',
	  layers:  [
	    new ol.layer.Tile({
	      source: new ol.source.TileImage({
	        attributions: [
	          new ol.Attribution({
	            html: 'Kaartgegevens: © <a href="http://www.cbs.nl">CBS</a>, <a href="http://www.kadaster.nl">Kadaster</a>, <a href="http://openstreetmap.org">OpenStreetMap</a><span class="printhide">-auteurs (<a href="http://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>).</span>'
	          })
	        ],
	        projection: projection,
	        tileGrid: new ol.tilegrid.TileGrid({
	          origin: [-285401.92,22598.08],
	          resolutions: resolutions
	        }),
	        tileUrlFunction: tileUrlFunction
	      })
	    })
	  ],
	  view: new ol.View({
	    minZoom: 3,
	    maxZoom: 13,
	    projection: projection,
	    center: [150000, 450000],
	    zoom: 3
	  })
	});

Met dank aan `@6artvde <https://github.com/bartvde/PDOK-OpenLayers3>`_.