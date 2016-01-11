.. _webapps:

###################
Geodata in web apps
###################

`Leaflet <http://leafletjs.com/>`_ en `OpenLayers <http://openlayers.org/>`_ maken het mogelijk om gegevens uit de :ref:`geo services en APIs <services>` te visualiseren op een interactieve kaart.

*******
Leaflet
*******

*Leaflet is the leading open-source JavaScript library for mobile-friendly interactive maps.*

Leaflets minimalistische insteek heeft in korte tijd veel gebruikers en ontwikkelaars aangetrokken die voor een `groeiende lijst van plugins <http://leafletjs.com/plugins.html>`_ zorgen. Leaflet wordt door o.a. Mapbox en CartoDB gebruikt.

WMS
===

Leaflet kan out-of-the-box WMS endpoints lezen. Gebruik de ``L.tileLayer.wms()`` functie als volgt

.. code-block:: javascript

    var map = L.map('map').setView([52, 5.3], 7);

    var leefbaro = L.tileLayer.wms("https://services.geodan.nl/public/data/ows/MBIZ4280LEEF/org/wms/MBIZ4280LEEF/wms", {
        layers: 'schaalafhankelijke_leefbaarheidskaart',
        format:'image/png',
        transparent: true
    });

    leefbaro.addTo(map);  

.. NOTE:: Dit werkt alleen als de WMS endpoint in de Pseudo-Mercator (ESPG:3857) kaartprojectie beschikbaar is, zie :ref:`coord-trans` voor meer informatie. De :ref:`WMS Capabilities <wms-getcapabilities>` document vermeldt welke coördinatenstelsels ondersteund worden.

WFS
===

Leaflet heeft geen out-of-the-box ondersteuning voor WFS. Een eenvoudige :ref:`GetFeature request <wfs-getfeature>` kun je als volgt zelf opbouwen. Onderstaande broncode `staat ook op GitHub <https://github.com/Geonovum/PDOK-NGR-documentatie/blob/gh-pages/examples/quickstart-leaflet.html>`_.

.. code-block:: javascript

    var map;

    window.onload = function() {
        map = L.map('map');
        
        // load OpenStreetMap basemap
        var basemap = L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png');
        basemap.addTo(map);

        var url = 'http://geodata.nationaalgeoregister.nl/bag/wfs?';
        var params = 'request=GetFeature&';
        params += 'service=WFS&';
        params += 'typeName=bag:pand&';
        params += 'count=100&';
        params += 'outputFormat=json&';
        params += 'srsName=EPSG:4326';

        $.getJSON(url + params, function(data) {
            loadGeometry(data);
        });
    };

    function loadGeometry(data) {
        $.each(data.features, function(index, geometry) {
            L.geoJson(geometry).addTo(map);
        });

        var center = data.features[0].geometry.coordinates[0][0];

        // setView expects lat, lng whereas GeoJSON stores coordinates as lng, lat
        map.setView([center[1], center[0]], 17);
    }

Je kan ook een van de `WFS plugins <http://leafletjs.com/plugins.html>`_ gebruiken. De BAG met bijv. de `WFST <https://github.com/Flexberry/Leaflet-WFST>`_ plugin lezen gaat als volgt.

.. code-block:: javascript

    var wfst = new L.WFST({
        url: 'http://geodata.nationaalgeoregister.nl/bag/wfs',
        typeNS: 'bag',
        typeName: 'pand',
        crs: L.CRS.EPSG4326,
        style: {
            color: 'blue',
            weight: 2
        }
    })
    .addTo(map) 
    .once('load', function () {
        map.fitBounds(wfst);
    });

TMS
===

Leaflet kan *by default* enkel rasterdata visualiseren die in de Pseudo-Mercator projectie opgeslagen is. De Nederlandse TMS endpoints zijn enkel in de Rijksdriehoekstelsel beschikbaar. De `Proj4Leaflet <http://kartena.github.io/Proj4Leaflet/>`_ plugin stelt je in staat om coördinatenstelses te transformeren. Zie onderstaande voorbeeld van `@emacgillavry <https://github.com/emacgillavry/PDOK-Leaflet/>`_. In :ref:`coord-trans` lees je meer over coördinatentransformaties.

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

************
OpenLayers 3
************

*A high-performance, feature-packed library for all your mapping needs.*

OpenLayers is een "`batteries included <http://openlayers.org/en/v3.12.1/examples/>`_" bibliotheek. Het heeft bijv. out-of-the-box ondersteuning voor WebGL, *touch input*, coordinatenstelsels e.a. OL wordt door o.a. `PDOK Kaart <http://kaart.pdok.nl/>`_ en de `Zwitserse geodata portaal <https://map.geo.admin.ch>`_ gebruikt. 

WMS
===

OpenLayers ondersteunt WMS out-of-the-box. De leefbaarheidskaart van de `Leefbarometer <http://www.leefbaarometer.nl/home.php>`_ bevraag je als volgt. 

.. code-block:: javascript

    var leefbarheid = new ol.layer.Tile({
        title: "Leefbaarheidskaart",
        visible: false,
        opacity: 0.7,
        source: new ol.source.TileWMS({
            url: "https://services.geodan.nl/public/data/ows/MBIZ4280LEEF/org/wms/MBIZ4280LEEF/wms",
            params: {
                "LAYERS": "schaalafhankelijke_leefbaarheidskaart",
                "SRS": "EPSG:3857"
            }
        })
    });

OpenLayers ondersteunt ook het ophalen van de achterliggende gegevens via de :ref:`WMS GetFeatureInfo <getfeatureinfo>` request, zie de `OpenLayers 3 voorbeeldenpagina <http://openlayers.org/en/v3.12.1/examples/>`_ voor meer informatie (zoek op WMS). 

WFS
===

OpenLayers heeft volwassen ondersteuning voor het lezen van WFS. Onderstaand voorbeeld haalt de BAG panden op die in de huidige extent vallen (`volledige broncode <http://openlayers.org/en/v3.12.1/examples/vector-wfs.html>`_ ).

.. code-block:: javascript

    var vector = new ol.layer.Vector({
        source: new ol.source.Vector({
            format: new ol.format.GeoJSON(),
            url: function(extent, resolution, projection) {
                return 'https://geodata.nationaalgeoregister.nl/bag/wfs?service=WFS&' +
                'version=1.1.0&request=GetFeature&typename=bag:pand&' +
                'outputFormat=application/json&srsname=EPSG:3857&' +
                'bbox=' + extent.join(',') + ',EPSG:3857';
            },
            strategy: ol.loadingstrategy.tile(ol.tilegrid.createXYZ({
                maxZoom: 20
            }))
        })
    });

    var map = new ol.Map({
        layers: [vector],
        target: document.getElementById('map'),
        view: new ol.View({
            center: [5, 50],
            center: ol.proj.transform([4.470, 51.9334], 'EPSG:4326', 'EPSG:3857'),
            zoom: 18
        })
    });

TMS
===

OpenLayers heeft geen *native* functionaliteit om TMS te visualiseren, je dient zelf de `TileImage` source te configureren, zie onderstaande `voorbeeld <https://github.com/bartvde/PDOK-OpenLayers3>`_ van `@6artvde <https://twitter.com/6artvde>`_.

.. code-block:: javascript
    :linenos:

    var extent = [-285401.92,22598.08,595401.9199999999,903401.9199999999];
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

De OpenLayers documentatie geeft een volledige `beschrijving <http://openlayers.org/en/v3.0.0/apidoc/ol.source.TileImage.html>`_ van de `TileImage` bron.


*******
CartoDB
*******

WMS
===

CartoDB ondersteunt sinds kort het inlezen van WMS endpoints. Deze hoeven niet in Pseudo-Mercator beschikbaard te zijn, CartoDB converteert ze indien nodig. Hierbij maken zij gebruik van MapProxy. 

Ga als volgt te werk om een Nederlandse WMS/WMTS kaartlaag toe te voegen

1. Open een bestaande kaart of maak een nieuwe
2. Klik op ``Change basemap`` -> ``Yours +``
3. Kies ``WMS/WMTS`` en plak een geldige WMS/WMTS URL in het tekstveld bijv. die van de PDOK Luchtforo: ``https://geodata1.nationaalgeoregister.nl/luchtfoto/wms``
4. Klik op ``Get Layers``, vindt de kaartlaag die je wilt toevoegen en klik op ``Add This``
