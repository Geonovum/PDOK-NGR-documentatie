#############
Handleidingen
#############


*******************************************************
Download data via Web Feature Service (WFS) and ogr2ogr
*******************************************************

TODO: convert `this presentation <https://speakerdeck.com/ndkv/open-geo-data-in-the-netherlands-and-beyond>`_ to a tutorial. Aim for the style of e.g. `OpenLayers 3 documentation <http://ol3js.org/en/master/doc/tutorials/concepts.html>`_.

* Nationaalgeoregister is a registry, it does not contain datasets itself but links to other sources. 
* Natioaalgeoregister holds links to `services <OGC Services>`_.

TODO: Include image that depicts how NGR is connected to databases.

This tutorial shows how to get datasets from the Dutch national geoportal through WFS using the GDAL/OGR toolset.  

The GDAL/OGR library is the Swiss army knife for handling geospatial data. GDAL provides functions to read, write and transform raster files (e.g. GeoTIFF). OGR provides the same functionality for vector data.

On Linux you can grab the library through your package manager. Apt-getting it on Ubuntu is as easy as::

    $ sudo apt-get install libgdal

Easiest way to get it on Windows is through the `OSGeo4W <http://trac.osgeo.org/osgeo4w/>`_ installer. 

.. NOTE::

    This tutorial assumes you are familar with the Web Feature Service. Not sure what that is? Review it :ref:`here <wfs>`. 


BAG
===

In this tutorial we will work with the :ref:`Bassisregistratie Adressen en Gebouwen dataset <bag>`. It contains, amongst others, the footrpints of all the Dutch buildings. It's the basis for this `CitySDK <http://citysdk.waag.nl/buildings/>`_ visualisation. The BAG WFS endpoint is located at::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs

.. WARNING::

    This particular service is limited to serving a maximum of 15000 features per request. If you need more you'll have to obtain the whole dataset from the ATOM feed or through ExtractNL. 

.. NOTE::

    Although the focus of this tutorial is on the BAG, the demonstrated worklfow and commands can be used to query any WFS endpoint. See ... for more information on how to search specifically for WFS endpoints in the register.  

We'll first investigate the endpoint with the *ogrinfo* utility and retrieve the data with the *ogr2ogr* utility.  

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

    $ ogrinfo -so WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs"

which results in::

   INFO: Open of 'WFS:' 
         using driver 'WFS' successful.

   1. bagviewer:ligplaats (Polygon)
   2. bagviewer:pand (Polygon)
   3. bagviewer:standplaats (Polygon)
   4. bagviewer:verblijfpaats (Point)
   5. bagviewer:woonplaats (Multi Polygon)


where the enumerated items represent the available layers and their type. The building footprints are contained in the 2nd layer and are of type *Polygon*. We can use *ogrinfo* to investigate a specific layer by appending its name at the end of the previous command as::

    $ ogrinfo -so WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs" bagviewer:pand

The result is the number of features contained in the layer, a listing of their attributes, the coordinate reference system of the layer and a bounding box of the features.

.. NOTE::

    Observe the afore mentioned limit: the reported number of features is 15000. There are, of course, more than 15000 buildings in the Netherlands.  


Getting data with ogr2ogr
=========================

The* ogr2ogr* utility allows for reading and writing of many different vector formats. The most basic use of *ogr2ogr* is constructed as::

    ogr2ogr -f output_format destination source layer

Getting the footprints of the first 15000 buildings as GeoJSON is achieved as::

    $ ogr2ogr -f GeoJSON footprints.geojson
    WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs"
    bagviewer:pand


Transforming - coordinates and formats
======================================

ogr2ogr's primary function is to transform vector data into different formats and coordinate reference systems. We can do the same with the WFS source; transforming the data from the Dutch coordinate system to lat/lng is done as::

    $ ogr2ogr -f GeoJSON footprints.geojson WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs" -t_srs EPSG:4326 bagviewer:pand

ogr2ogr can also transform between file formats. Transforming e.g. a shapefile into a GeoJSON file is done as::

    $ ogr2ogr -f GeoJSON input.shp output.geojson

Filtering
---------

ogr2ogr supports filtering of datasources through a simple *-where* clause as well as sophisticated SQL queries. Retrieving a single column from the footprints dataset through SQL is done as::

    $ ogr2ogr -f GeoJSON gemeenten_2011_84.geojson 
    WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs" 
    -sql "SELECT CAST('bouwjaar') AS integer 
    FROM 'bagviewer:pand'" 
    -t_srs EPSG:4326

TODO: add bounding box query

Next steps: visualising and publishing data  
===========================================

You can now load the vector file in a popular GIS pacakge for furhter analysis, load it in a PostGIS databasem, publish it to Mapbox or CartoDB, etc. 

TODO examples

******************************************
WFS 2.0 pagination and GeoJSON output (NL)
******************************************
.. NOTE:: Dit is een `bijdrage <http://www.brentjensgeoict.nl/index.php?post=haal-meer-data-en-geojson-uit-een-pdok-wfs>`_ van Thijs Brentjens.

De Web Feature Services van PDOK bieden toegang tot allerlei (vector)data. De data van de BAG, natura2000, het NWB wegennet, bestuurlijke grenzen en wat dan ook kan je ophalen voor gebruik in eigen applicaties. Bijvoorbeeld in QGIS, OpenLayers of andere tooling. Voor de service URLs kan je kijken op de PDOK `services <https://www.pdok.nl/nl/producten/pdok-services>`_ pagina.

Conform de WFS specificatie gaat het ophalen met GetFeature requests. Bijvoorbeeld een HTTP Get Request om `alle provincie grenzen op te halen <http://geodata.nationaalgeoregister.nl/bestuurlijkegrenzen/wfs?service=WFS&version=2.0.0&request=GetFeature&typename=provincies>`_:

::

    http://geodata.nationaalgeoregister.nl/bestuurlijkegrenzen/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=provincies

De PDOK services kennen alleen een maximum van 15.000 objecten per request. Dat mag, bijvoorbeeld om de belasting op de servers te beperken en te voorkomen dat iemand niet (per ongeluk) alle data ophaalt in zijn browser. Voor datasets van enige omvang betekent dit alleen dat je die niet helemaal in één keer via de WFS kan ophalen. In sommige gevallen kan je je wenden tot de data dumps, te downloaden via ATOM feeds. Zie `<http://geodata.nationaalgeoregister.nl/atom/index.xml>`_

Maar niet altijd. En soms wil je juist de WFS bevragen, met een filter erbij bijvoorbeeld. Dus wat doe je dan als je meer dan die 15.000 objecten wil ophalen? Dan komt een van de handige WFS 2.0.0 functies van pas: ResponsePaging.

WFS 2.0 ResponsePaging
======================

Vanaf versie 2.0.0 van WFS is ResponsePaging gespecificeerd. De PDOK WFSen ondersteunen dit. Gelukkig. Want met Response paging kan je een door jezelf opgegeven aantal objecten ophalen (bijvoorbeeld de eerste 100) en daarna de volgende set (van 100) en weer de volgende set, totdat je alles hebt. Vergelijkbaar met paginering van zoekresultaten van zoekmachines.

Dit kan je heel simpel opgeven per request met de paramaters *count* (die maxFeatures vervangt uit WFS 1.0.0 en WFS 1.1.0) en de *startIndex*. Een voorbeeld voor de eerste 100 ligplaatsen uit de BAG, dus met *count=100* en *startIndex=0*:

::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bagviewer:ligplaats&
    count=100&
    startindex=0

En de volgende 100 en weer 100:

::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bagviewer:ligplaats&
    count=100&
    startindex=100

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bagviewer:ligplaats&
    count=100&
    startindex=200

Totdat je alle data binnen hebt. Hoe je weet dat je alles hebt? Kijk eens naar het GML response als je *count* en *startIndex* hebt gebruikt. In de eerste tag staat een attribuut *numberMatched*, dat aangeeft hoeveel resultaten er totaal zijn. Erg handig om te gebruiken in je algoritme om alles op te halen.

Of slimmer nog, vraag voordat je daadwerkelijk data gaat ophalen met *resulttype=hits* hoeveel objecten je GetFeature request zou opleveren. Bijvoorbeeld (let op: zonder count parameter!)

::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bagviewer:ligplaats&
    resulttype=hits

In dit geval is het `antwoord <http://geodata.nationaalgeoregister.nl/bagviewer/wfs?service=WFS&version=2.0.0&request=GetFeature&typename=bagviewer:ligplaats&resulttype=hits>`_ 11757.

(Geo)JSON en andere formaten
============================

GML is voor veel webontwikkelaars niet de eerste keus. JSON en GeoJSON voor geodata lijken de standaard te worden. Maar een WFS geeft standaard (keurig conform de specs) GML terug op een GetFeature reques. Wederom niet getreurd. Ook het GeoJSON formaat is beschikbaar bij de WFSen die PDOK aanbiedt. Gebruik daarvoor de parameter *outputformat=json* bij een GetFeature request en je krijgt GeoJSON terug. Voorbeeld:

::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs?
    service=WFS&
    version=2.0.0&
    request=GetFeature&
    typename=bagviewer:ligplaats&
    count=100&
    startindex=100&
    outputformat=json 
    
    
.. NOTE::

    Let op, zie ook de tip over lat/long-coordinaten hieronder.

Handig in webapplicaties als OpenLayers en Leaflet. Of ook deskop pakketten.

Tot slot: een PDOK WFS steunt nog meer formaten. Zie daarvoor het stukje XML over het outputFormat van het GetFeature-deel in uit de Capabilities van een WFS. Dit Capabilities document is op te vragen via bijvoorbeeld:

::

    http://geodata.nationaalgeoregister.nl/bagviewer/wfs
    service=WFS&
    request=GetCapabilities

Gebruik EPSG:4326 voor lat/long-coordinaten
===========================================
Update, met een tip van Edward MacGillavry (Webmapper): Voeg voor GeoJSON ook toe dat je data wilt in WGS84, met de volgende parameter: *srsName=EPSG:4326*. De meeste software gaat namelijk uit van lat/long coordinaten in WGS84 in GeoJSON.


