
.. NOTE:: Staat de handeling die je wilt verrichten er niet bij? Heb je tips, suggesties of heb je een fout ontdekt? :ref:`Laat het ons weten! <doel-feedback>`

#############
Handleidingen
#############

Hoe zet je adressen om naar coördinaten? Hoe maak je Nederlandse geodata geschikt om te plotten in bijv. Leaflet of CartoDB? Hoe download je grote hoeveelheden data uit een WFS endpoint? 

**************************************
Geocoderen - van adres naar coördinaat
**************************************

Het omzetten van adressen naar geografische coördinaten heet `geocoderen <https://en.wikipedia.org/wiki/Geocoding>`_. Gebruik QGIS om eenmalig kleine datasets te geocoderen; gebruik een van de vele APIs als je grotere datasets hebt of regelmatig moet geocoderen.

Op de desktop: QGIS
===================

De PDOK BAG Geocoder plugin voor QGIS is zet adressen om naar coördinaten op de kaart. De plugin leest een CSV bestand in bijv. adressen.csv en geeft je een geogerefereerd Shapefile terug (adressen.shp). De plugin spreekt de `PDOK Geocoder <https://www.pdok.nl/nl/service/openls-bag-geocodeerservice>`_ aan.

.. image:: images/qgis-geocoder.png
    :align: center

Klein nadeeltje van de PDOK Geocoder is dat ie coördinaten in het Nederlandse coördinatenstelsel retourneert. CartoDB, Mapbox, Google Maps, etc. verwachten coördinaten in lat/lng. In :ref:`coord-trans` lees je hoe je coördinaten kan transformeren in o.a. QGIS.

Via een API
===========

Nederland telt een aantal Geocodeer APIs waarmee je (betaald) adressen kan geocoderen. 

- `PDOK Geocoder <https://www.pdok.nl/nl/service/openls-bag-geocodeerservice>`_
- `Postcode API <http://www.postcodeapi.nu/>`_ van `Apiwise <http://www.apiwise.nl/>`_
- `Overheid.io <https://overheid.io/documentatie/bag>`_ 

Op http://openaddresses.io/ vindt je geodata waarmee je je eigen geocodeer (API) kan bouwen. 

.. _coord-trans:

*************************
Coördinatentransformaties
*************************

Nederlandse geodata gebruiken het `Rijksdriehoekscoördinatenstelsel <https://nl.wikipedia.org/wiki/Rijksdriehoeksco%C3%B6rdinaten>`_, ook wel bekend als ``Amersfoort / RD New``. RD-coördinaten worden niet (out-of-the-box) door Google Maps, Mapbox, CartoDB, e.a. ondersteund. Deze diensten gebruiken de `WGS84 / Pseudo-Mercator <https://en.wikipedia.org/wiki/Web_Mercator>`_ projectie. Hoewel ``Pseudo-Mercator`` meter [m] als eenheid heeft, gebruiken Google Maps, Mapbox, CartoDB, e.a. *lengte- en breedtegraden* als coördinaten voor vector features. Deze lengte- en breedtegraden duiden een plek aan op de aarde zoals benaderd door de WGS84 ellipsoïde. Om Nederlandse vector data in bijv. Mapbox te visualiseren moet je RD-coördinaten daarom naar WGS84 transformeren i.p.v. Pseudo-Mercator. **Let op**: dit geldt niet voor rasters.

Het transformeren van coördinaten kan in de desktop met :ref:`QGIS <coord-trans-qgis>`, in de browser met :ref:`proj4js <coord-trans-proj4js>` en in de *command line* met :ref:`ogr2ogr <coord-trans-ogr2ogr>`. Om deze tools te gebruiken moet je de EPSG (European Petroleum Survey Group) codes van de coördinatenstelsels weten waartussen je wilt transformeren. Deze vindt je op `epsg.io <http://epsg.io/>`_:

- ``Amersfoort / RD New`` heeft `EPSG code 28992 <http://epsg.io/28992>`_
- ``Pseudo-Mercator`` heeft `EPSG code 3857 <http://epsg.io/3857>`_
- ``WGS84`` heeft `EPSG code 4326 <http://epsg.io/4326>`_

Op `mapschool.io <http://mapschool.io/>`_ lees je meer over coördinatenstelsels en bolcoördinaten.

.. _coord-trans-qgis:

Op de desktop: QGIS
===================

1. Klik met de rechtermuisknop op de laag die je wilt transformeren en selecteer ``Save As..``
2. Kies ESRI Shapefile, GeoJSON of KML uit het ``Format`` menu
3. Klik op de ``Browse`` knop en geef aan waar het bestand opgeslagen moet worden
4. Kies ``EPSG:4326 - WGS84`` uit het ``CRS`` menu om de coördinaten naar ``lat/lng`` te transformeren
5. Klik op ``OK``

.. image:: images/qgis-vector-save.png
    :align: center

.. _coord-trans-proj4js:

In de browser: proj4js
======================

De JavaScript bibliotheek `proj4js <http://proj4js.org/>`_ is gemaakt voor het transformeren van coördinaten.

.. code-block:: javascript

    // definitie van de Nederlandse coördinatenstelsel
    var RD = "+proj=sterea +lat_0=52.15616055555555 +lon_0=5.38763888888889 +k=0.9999079 +x_0=155000 +y_0=463000 +ellps=bessel +units=m +towgs84=565.2369,50.0087,465.658,-0.406857330322398,0.350732676542563,-1.8703473836068,4.0812 +no_defs";

    // World Geodetic System, in gebruik door Google Maps, Mapbox, CartoDB, e.a.
    var WGS84 = "WGS84";
    
    var transformed = proj4(RD,WGS84,[0,0]);
    // Array [ 3.3135577051498664, 47.974765849805124 ]
    
.. _coord-trans-ogr2ogr:

In de terminal: ogr2ogr
=======================

Het programma `ogr2ogr` is een command line utility die een groot aantal geodata formats kan lezen en schrijven. Omzetten van coördinaten in het Nederlandse coördinatenstelsel naar WGS84 gaat als volgt

::

    ogr2ogr -f GeoJSON target.geojson source_RD.geojson -s_srs EPSG:28992 -t_srs EPSG:4326


.. _wfs-pagination:

******************
WFS - pagination
******************
.. NOTE:: Dit is een `bijdrage <http://www.brentjensgeoict.nl/index.php?post=haal-meer-data-en-geojson-uit-een-pdok-wfs>`_ van Thijs Brentjens.

De Web Feature Services van PDOK bieden toegang tot allerlei (vector)data. De data van de BAG, natura2000, het NWB wegennet, bestuurlijke grenzen en wat dan ook kan je ophalen voor gebruik in eigen applicaties. Bijvoorbeeld in QGIS, OpenLayers of andere tooling. Voor de service URLs kan je kijken op de PDOK `services <https://www.pdok.nl/nl/producten/pdok-services>`_ pagina.

Conform de WFS specificatie gaat het ophalen met GetFeature requests. Bijvoorbeeld een HTTP Get Request om `alle provincie grenzen op te halen <http://geodata.nationaalgeoregister.nl/bestuurlijkegrenzen/wfs?service=WFS&version=2.0.0&request=GetFeature&typename=provincies>`_:

::

    http://geodata.nationaalgeoregister.nl/bestuurlijkegrenzen/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=provincies

De PDOK services kennen alleen een maximum van 1.000 objecten per request. Dat mag, bijvoorbeeld om de belasting op de servers te beperken en te voorkomen dat iemand niet (per ongeluk) alle data ophaalt in zijn browser. Voor datasets van enige omvang betekent dit alleen dat je die niet helemaal in één keer via de WFS kan ophalen. In sommige gevallen kan je je wenden tot de data dumps, te downloaden via ATOM feeds. Zie `<http://geodata.nationaalgeoregister.nl/atom/index.xml>`_

Maar niet altijd. En soms wil je juist de WFS bevragen, met een filter erbij bijvoorbeeld. Dus wat doe je dan als je meer dan die 1.000 objecten wil ophalen? Dan komt een van de handige WFS 2.0.0 functies van pas: ResponsePaging.

.. _wfs-response-paging:

WFS 2.0 ResponsePaging
======================

Vanaf versie 2.0.0 van WFS is ``ResponsePaging`` gespecificeerd. De PDOK WFSen ondersteunen dit. Gelukkig. Want met Response paging kan je een door jezelf opgegeven aantal objecten ophalen (bijvoorbeeld de eerste 100) en daarna de volgende set (van 100) en weer de volgende set, totdat je alles hebt. Vergelijkbaar met paginering van zoekresultaten van zoekmachines.

Dit kan je heel simpel opgeven per request met de paramaters *count* (die maxFeatures vervangt uit WFS 1.0.0 en WFS 1.1.0) en de *startIndex*. Een voorbeeld voor de eerste 100 ligplaatsen uit de BAG, dus met *count=100* en *startIndex=0*:

::

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bag:ligplaats&
    count=100&
    startindex=0

En de volgende 100 en weer 100:

::

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bag:ligplaats&
    count=100&
    startindex=100

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bag:ligplaats&
    count=100&
    startindex=200

Totdat je alle data binnen hebt. Hoe je weet dat je alles hebt? Kijk eens naar het GML response als je *count* en *startIndex* hebt gebruikt. In de eerste tag staat een attribuut *numberMatched*, dat aangeeft hoeveel resultaten er totaal zijn. Erg handig om te gebruiken in je algoritme om alles op te halen.

Of slimmer nog, vraag voordat je daadwerkelijk data gaat ophalen met *resulttype=hits* hoeveel objecten je GetFeature request zou opleveren. Bijvoorbeeld (let op: zonder count parameter!)

::

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bag:ligplaats&
    resulttype=hits

In dit geval is het `antwoord <http://geodata.nationaalgeoregister.nl/bag/wfs?service=WFS&version=2.0.0&request=GetFeature&typename=bag:ligplaats&resulttype=hits>`_ 11757.

****************************
WFS - output formaat
****************************

GML is voor veel webontwikkelaars niet de eerste keus. JSON en GeoJSON voor geodata lijken de standaard te worden. Maar een WFS geeft standaard (keurig conform de specs) GML terug op een GetFeature request. Wederom niet getreurd. Ook het GeoJSON formaat is beschikbaar bij de WFSen die PDOK aanbiedt. Gebruik daarvoor de parameter `outputformat=json` bij een GetFeature request en je krijgt GeoJSON terug. Voorbeeld:

::

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bag:ligplaats&
    count=100&
    startindex=100&
    outputformat=json 

Tot slot: een PDOK WFS steunt nog meer formaten. Zie daarvoor het stukje XML over het outputFormat van het GetFeature-deel in uit de Capabilities van een WFS. Dit Capabilities document is op te vragen via bijvoorbeeld:

::

    http://geodata.nationaalgeoregister.nl/bag/wfs
    service=WFS&
    request=GetCapabilities

****************************
WFS - coördinaten in lat/lng
****************************

Tip van Edward MacGillavry (Webmapper): voeg ``srsName=EPSG:4326`` parameter aan je WFS request toe om de coördinaten naar lat/lng (WGS84) te transformeren.

.. _ogr2ogr tutorial:

**********************
ogr2ogr en de BAG (EN)
**********************

This tutorial shows how to get datasets from the Dutch national geoportal through WFS using the GDAL/OGR toolset.

The GDAL/OGR library is the Swiss army knife for handling geospatial data. GDAL provides functions to read, write and transform raster files (e.g. GeoTIFF). OGR provides the same functionality for vector data.


Installation
============

On **Linux** you can grab the library through your package manager. Apt-getting it on Ubuntu is as easy as::

    $ sudo apt-get install libgdal

Easiest way to get it on Windows is through the `OSGeo4W <http://trac.osgeo.org/osgeo4w/>`_ installer.


Basisregistratie Adressen en Gebouwen
=====================================
The *Basisregistratie Addressen en Gebouwen* is a Dutch law in which it is declared and regulated that all address and building information needs to be freely available to it's citizens.
Litterally translated it means "Base registration Addresses and Buildings" and is abbreviated as *BAG*.

.. NOTE::

    This tutorial assumes you are familar with the Web Feature Service. Not sure what that is? Review it :ref:`here <wfs>`. 

In this tutorial we will work with the `Basisregistratie Adressen en Gebouwen dataset (in Dutch) <https://www.kadaster.nl/wat-is-de-bag>`_. It contains, amongst others, the footprints of all the Dutch buildings. It's the base for the `CitySDK <http://citysdk.waag.nl/buildings/>`_ visualisation. The BAG WFS endpoint is located at::

    http://geodata.nationaalgeoregister.nl/bag/wfs

.. WARNING::

    This particular service is limited to serving a maximum of 1.000 features per request. If you need more you'll have to obtain the whole dataset from the ATOM feed or through ExtractNL.

.. NOTE::

    Although the focus of this tutorial is on the BAG, the demonstrated worklfow and commands can be used to query any WFS endpoint. See `NGR WFS <http://www.nationaalgeoregister.nl/geonetwork/srv/dut/catalog.search#/search?facet.q=protocol%2FOGC%253AWFS&isChild=%27false%27&resultType=details&fast=index&_content_type=json&from=1&to=20&sortBy=relevance>`_ for all WFS endpoints in the register.

We'll first investigate the endpoint with the *ogrinfo* utility and retrieve the data with the *ogr2ogr* utility.

.. _ogrinfo:

Investigating the data source with ogrinfo 
==========================================
The *ogrinfo* utility retrieves the metadata of a service. It tells us which layers are available in the service, how many features they contin, in which coordinate reference system is the data stored, etc.

::

    $ ogrinfo -so WFS:"<url>"

where 

* -so retrieves a summary of the statistics
* <url> points to a WFS endpoint

*ogrinfo* is executed from the command line. On Linux you need to find Terminal/Console app. On Windows your best bet is the OSGeo4W Shell that is installed through the OSGeo4W installer. Open either one and enter the following command to query the BAG endpoint

::

    $ ogrinfo -so WFS:"http://geodata.nationaalgeoregister.nl/bag/wfs"

which results in::

   INFO: Open of 'WFS:' 
         using driver 'WFS' successful.

   1. bag:ligplaats (Polygon)
   2. bag:pand (Polygon)
   3. bag:standplaats (Polygon)
   4. bag:verblijfpaats (Point)
   5. bag:woonplaats (Multi Polygon)


where the enumerated items represent the available layers and their type. The building footprints are contained in the 2nd layer and are of type *Polygon*. We can use *ogrinfo* to investigate a specific layer by appending its name at the end of the previous command as::

    $ ogrinfo -so WFS:"http://geodata.nationaalgeoregister.nl/bag/wfs" bag:pand

The result is the number of features contained in the layer, a listing of their attributes, the coordinate reference system of the layer and a bounding box of the features.

.. NOTE::

    Observe the afore mentioned limit: the reported number of features is 1.000. There are, of course, more than 1.000 buildings in the Netherlands.


Getting data with ogr2ogr
=========================

The* ogr2ogr* utility allows for reading and writing of many different vector formats. The most basic use of *ogr2ogr* is constructed as::

    ogr2ogr -f output_format destination source layer

Getting the footprints of the first 1.000 buildings as GeoJSON is achieved as::

    $ ogr2ogr -f GeoJSON footprints.geojson
    WFS:"http://geodata.nationaalgeoregister.nl/bag/wfs"
    bag:pand


Transforming - coordinates and formats
======================================

ogr2ogr's primary function is to transform vector data into different formats and coordinate reference systems. We can do the same with the WFS source; transforming the data from the Dutch coordinate system to lat/lng is done as::

    $ ogr2ogr -f GeoJSON footprints.geojson WFS:"http://geodata.nationaalgeoregister.nl/bag/wfs" -t_srs EPSG:4326 bag:pand

ogr2ogr can also transform between file formats. Transforming e.g. a shapefile into a GeoJSON file is done as::

    $ ogr2ogr -f GeoJSON input.shp output.geojson

Filtering
---------

ogr2ogr supports filtering of datasources through a simple *-where* clause as well as sophisticated SQL queries. Retrieving a single column from the footprints dataset through SQL is done as::

    $ ogr2ogr -f GeoJSON gemeenten_2011_84.geojson 
    WFS:"http://geodata.nationaalgeoregister.nl/bag/wfs" 
    -sql "SELECT CAST('bouwjaar') AS integer 
    FROM 'bag:pand'" 
    -t_srs EPSG:4326

TODO: add bounding box query

Next steps: visualising and publishing data
===========================================

You can now load the vector file in a popular GIS pacakge for furhter analysis, load it in a PostGIS database, publish it to Mapbox or CartoDB, etc.

TODO examples
