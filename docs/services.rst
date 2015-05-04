############
OGC Services
############

De Open Geospatial Consortium (OGC) is een internationale organisatie die de leiding heeft in de ontwikkeling van interoperabele standaarden voor georuimtelijke en plaatsgebonden diensten. De belangrijkste OGC standaarden in gebruik in Nederland zijn de 

- de **Web Map Service** (WMS) - de zogenaamde *view* service genereert een statische kaartuitsnedes van geo-informatie in een raster formaat zoals PNG, GIF of JPEG. 
- de **Web Feature Service** (WFS) - de zogenaamde *download* service is een protocol voor het opvragen van geografische vector data, al dan niet via een ruimtelijk filter.

De W*S diensten worden via HTTP GET en POST requests bevraagd. De mogelijkheden van elk service worden in een *Capabilities* document beschreven

::

    http://geo.data.nl/ogc-service?
    service=WMS/WFS/WMTS/WCS
    request=GetCapabilities

Hierin staat in welke coordinatenstelsels de gegevens beschikbaar zijn, welke lagen beschikbaar zijn, welke data formats ondersteunt worden, etc.

In de praktijk worden de diensten zelden op deze manier bevraagd. Het is gebruikelijker om ze via OpenLayers, Leaflet, QGIS, ogr2ogr, `Python <https://pypi.python.org/pypi/OWSLib>`_, etc te benaderen.

.. _wfs:

*************************
Web Feature Service (WFS)
*************************

Web Feature Service is een webservice voor het opvragen van geografische vector data en de bijbehorende administratieve gegevens. Belangrijke requests zijn:

- **GetCapabilities**: voor het bekijken van de mogelijkheden van de service
- **DescribeFeatureType**: haalt de beschrijving op van een of meerdere objecten
- **GetFeature**: haalt een of meerdere geometrieeen en de bijbehorende attributen op. Deze operatie kan gecombineerd worden met ruimtelijke of attribuut filters en een output-formaat keuze (afhankelijk van implementatie: bijv. GML, SHP, JSON, KML, CSV)

Zie de `specificatie <http://www.opengeospatial.org/standards/wfs>`_ voor een volledige beschrijving van WFS.

GetCapabilities
===============

De functionaliteit van een WFS endpoint wordt beschreven in een *Capabilities* document die middels een GetCapabilities request opgehaald kan worden:

::

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    request=GetCapabilities

Het resultaat is een XML document waarin o.a. de opgeslagen data types, lagen beschreven worden, ondersteunde coordinatenstelsels, etc.

.. code-block:: xml

    <wfs:WFS_Capabilities version="2.0.0" xsi:schemaLocation="http://www.opengis.net/wfs/2.0 http://geodata.nationaalgeoregister.nl/schemas/wfs/2.0/wfs.xsd http://inspire.ec.europa.eu/schemas/common/1.0 http://inspire.ec.europa.eu/schemas/common/1.0/common.xsd http://inspire.ec.europa.eu/schemas/inspire_dls/1.0 http://inspire.ec.europa.eu/schemas/inspire_dls/1.0/inspire_dls.xsd" updateSequence="1943">
        <ows:ServiceIdentification>...</ows:ServiceIdentification>
        <ows:ServiceProvider>...</ows:ServiceProvider>
        <ows:OperationsMetadata>...</ows:OperationsMetadata>
        <FeatureTypeList>
            <FeatureType></FeatureType>
            <FeatureType>
                <Name>bag:pand</Name>
                <Title>pand</Title>
                <Abstract>pand</Abstract>
                <ows:Keywords>
                    <ows:Keyword>pand</ows:Keyword>
                    <ows:Keyword>features</ows:Keyword>
                </ows:Keywords>
                <DefaultCRS>urn:ogc:def:crs:EPSG::28992</DefaultCRS>
                <ows:WGS84BoundingBox>
                    <ows:LowerCorner>3.2800546964714012 50.748745396375774</ows:LowerCorner>
                    <ows:UpperCorner>7.224161199744223 53.48515806526503</ows:UpperCorner>
                </ows:WGS84BoundingBox>
                <MetadataURL xlink:href="http://www.nationaalgeoregister.nl/geonetwork/srv/dut/xml.metadata.get?uuid=aa3b5e6e-7baa-40c0-8972-3353e927ec2f"/>
            </FeatureType>
            <FeatureType>...</FeatureType>
            <FeatureType>...</FeatureType>
            <FeatureType>...</FeatureType>
        </FeatureTypeList>
        <fes:Filter_Capabilities>...</fes:Filter_Capabilities>
    </wfs:WFS_Capabilities>


GetFeature
==========

Met de GetFeature request is het mogelijk om geometrieen en attributen op te halen. De volgende reuest haalt 100 gebouwen op uit de Basisregistratie Adressen en Gebouwen

:: 

    http://geodata.nationaalgeoregister.nl/bag/wfs?
    service=WFS&
    request=GetFeature&
    typeName=bag:pand&
    count=100&
    outputFormat=json

`Het resultaat <http://geodata.nationaalgeoregister.nl/bag/wfs?service=WFS&request=GetFeature&typeName=bag:pand&count=10&outputFormat=json>`_ is een GeoJSON document met daarin de polygonen van de voetafdruk en attributen van elk gebouw.

.. code-block:: javascript

    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "id": "pand.7871844",
                "geometry_name": "geometrie",
                "properties": {
                    "identificatie": 856100000350209,
                    "bouwjaar": 1941,
                    "status": "Pand in gebruik",
                    "gebruiksdoel": "woonfunctie",
                    "oppervlakte_min": 147,
                    "oppervlakte_max": 147,
                    "aantal_verblijfsobjecten": 1,
                    "actualiteitsdatum": null
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                179753.466,
                                405278.319
                            ],
                            [
                                179750.226,
                                405277.631
                            ],

                            ...

                            [
                                179753.466,
                                405278.319
                            ]
                        ]
                    ]
                }
            },
            {...}
        ]
    }

.. _wms:

*********************
Web Map Service (WMS)
*********************

De Web Map Service is een webservice voor het ophalen van kaartbeelden in een raster formaat zoals PNG, JPEG en GIF. Het is te vergelijken met de *static maps* APIs van `Mapbox <https://www.mapbox.com/developers/api/static/>`_ en `Google Maps <https://developers.google.com/maps/documentation/staticmaps/#quick_example>`_ 

.. image:: images/brt.png
    :height: 315
    :width: 680
    :align: center


WMS kent minimaal 3 operaties: 

- **GetCapabilities**: retourneert een lijst van beschikbare kaartlagen, projecties, formaten enz)
- **GetMap**: retourneert een statische kaart afbeelding 
- **GetFeatureInfo**: geeft attribuutgegevens van een object op een bepaalde locatie

Zie de `specificatie <http://www.opengeospatial.org/standards/wms>`_ voor een volledige beschrijving van WMS.

GetCapabilities
===============

TODO

GetMap
======

De *GetMap* request haalt een statisch kaartbeeld op.

::

  http://geodata.nationaalgeoregister.nl/ahn2/wms?
  service=WMS&
  request=GetMap&
  layers=ahn2_5m&
  bbox=13014,306243,286599,623492&
  width=400&
  height=500&
  format=image/png&
  srs=EPSG:28992


Dit `resulteert <http://geodata.nationaalgeoregister.nl/ahn2/wms?service=wms&request=getmap&layers=ahn2_5m&bbox=13014,306243,286599,623492&width=400&height=500&format=image/png&srs=EPSG:28992>`_ in een PNG afbeelding.

.. image:: images/ahn2.png
    :height: 300
    :width: 400
    :align: center

GetFeatureInfo
==============

TODO

::

  http://geodata.nationaalgeoregister.nl/ahn2/wms?
  service=wms&
  request=getfeatureinfo&
  layers=ahn2_5m&
  bbox=13014,306243,286599,623492&
  width=400&
  height=500&
  format=image/png&
  srs=EPSG:28992&
  query_layers=ahn2_5m&
  info_format=application/json&
  x=353&
  y=145

Dit `resulteert <http://geodata.nationaalgeoregister.nl/ahn2/wms?service=wms&request=getfeatureinfo&layers=ahn2_5m&bbox=13014,306243,286599,623492&width=400&height=500&format=image/png&srs=EPSG:28992&query_layers=ahn2_5m&info_format=application/json&x=353&y=145>`_ in een JSON document. 

.. code-block:: javascript

    {
        "type": "FeatureCollection",
        "totalFeatures": "unknown",
        "features": [
            {
                "type": "Feature",
                "id": "",
                "geometry": null,
                "properties": {
                    "GRAY_INDEX": 17.51810073852539
                }
            }
        ],
        "crs": null
    }


****************************
Web Map Tile Services (WMTS)
****************************

Web Map Tile Services zijn vergelijkbaar met WMS, echter in dit geval is het kaartbeeld opgeknipt in tegels volgens een gedefinieerd grid. De tegels worden al dan niet gecached aan serverzijde voor hergebruik. De WMTS operaties zijn vergelijkbaar met de overige OGC diensten, echter het *Capabilities* document is uitgebreid met het gebruikte grid (per projectie). 




WMTS wordt ondersteund door het overgrote deel van de hierboven genoemde WMS clients. Voor Esri is er een plug-in beschikbaar voor wmts arcbrutile(http://arcbrutile.codeplex.com/). Geonovum heeft ten behoeve van interoperabiliteit binnen Nederland een tiling richtlijn voor RD_new (epsg:28992) vastgesteld (http://www.geonovum.nl/sites/default/files/Nederlandse_richtlijn_tiling_-_versie_1.0.pdf).

Zie de `speficitatie <http://www.opengeospatial.org/standards/wmts.>`_ voor meer informatie. 

***********************
Tile Map Service (TMS) 
***********************

TODO

The TMS resolutions are defined on page 7 of the `PDOK Manual (PDF) <https://www.pdok.nl/sites/default/files/bibliotheek/handleiding_pdok_gebruik_10_dec_2012_v1_1.pdf>`_.

OpenLayers
==========
Allereerst dien je op LAYER niveau (dus niet perse op de map options) onderstaande "randvoorwaarden" te definieren. Deze zullen worden gebruikt door ALLE pdok tms lagen

.. code-block:: javascript

  var matrixIds = [];
  for(var i=0; i<15; ++i) { 
     matrixIds[i]='EPSG:28992:'+i;
  }
  
  var resolutions = [3440.64, 1720.32, 860.16, 430.08, 215.04, 107.52, 53.76, 26.88, 13.44, 
    6.72, 3.36, 1.68, 0.84, 0.42, 0.21];

Vervolgens kun je dan een TMS laag definieren als:

.. code-block:: javascript
  
  var mijnlaag = new OpenLayers.Layer.TMS(
    'Topografische kaart 1:10.000',
    'http://geodata.nationaalgeoregister.nl/tms/',
     {
       layername: 'top10nl', 
       isBaseLayer: true
       displayInLayerSwitcher: true,
       type: 'png8',
       matrixSet: 'EPSG:28992',
       matrixIds: matrixIds,
       tileOrigin: new OpenLayers.LonLat(-285401.92,22598.08),
       serverResolutions: resolutions,
       tileFulExtent: new OpenLayers.Bounds (-285401.92, 22598.08, 595401.9199999999, 903401.9199999999)
     }
  );

En deze laag dan uiteraard toevoegen aan je OpenLayers.Map

Flamingo 4
==========
Configuration parameters for the geo content management solution `Flamingo 4 <http://flamingo.b3p.nl/trac/>`_.

.. image:: https://f.cloud.github.com/assets/1814164/350385/7707eab6-a01a-11e2-9d07-0c27a27ec11a.png
    :width: 800px

***********************************
Catalogue Service for the Web (CSW)
***********************************

De developer pagina van het Nationaal Georegister (NGR) bevat een aantal tips om met data en services uit het register aan de slag te gaan. Deze pagina bevat meer informatie over de CSW interface van het NGR.

Het register is een catalogus met informatie over vooral ruimtelijke datasets en services die door voornamelijk overheden ter beschikking gesteld worden. OGC:CSW is de catalogus standaard van OGC. De CSW requests kunnen via HTTP GET of XMl over HTTP POST verstuurd worden.

De CSW specificatie is vrij groot en bescrhijft veel functionaliteit. Deze pagina beschrijft hoe je de meest voorkomende operaties kan uitvoeren. Meer info over CSW vind je op (http://www.opengeospatial.org/standards/cat). In veel gevallen ben je echter sneller af als je een bestaande bibliotheek gebruikt om CSW te ontsluiten: OpenLayers(http://dev.openlayers.org/docs/files/OpenLayers/Protocol/CSW/v2_0_2-js.html) of GXP(http://gxp.opengeo.org/master/examples/catalogue.html) of Geonetwork Widgets(http://nationaalgeoregister.nl/geonetwork/apps/js/GeoNetwork/examples).
Ook Esri(https://github.com/Esri/geoportal-server/tree/master/components/desktop/CswClient/trunk) en QGIS(http://hub.qgis.org/projects/cswclient) kennen CSW-ondersteuning via het laden van een plug-in, beide plug-ins zijn beschikbaar als opensource.

De belangrijkste operaties om metadata op te vragen zijn:

1. `GetCapabilities`_: voor het bekijken van de mogelijkheden van de service. Deze operatie kan handig zijn om te zien welke filters ondersteund worden bijvorrbeeld.
2. `GetRecords`_: om meerdere metadata documenten (records) op te vragen, bijvoorbeeld met een zoekfilter.
3. `GetRecordById`_: om 1 metadata document op te vragen, via het metadata ID.

De CSW requests kunnen via HTTP GET of XML over HTTP POST verstuurd worden. Hieronder volgen praktische voorbeelden voor het NGR.

Op de website van GeoNetwork is meer informatie te vinden over CSW: 

http://www.geonetwork-opensource.org/manuals/2.8.0/eng/developer/xml_services/csw_services.html

GetCapabilities
===============
Middels een GetCapabilities request kan opgevraagd worden wat de server kan. Door het volgende HTTP GET request te versturen, kan je zien wat de server aan functionaliteit biedt:

::

    http://nationaalgeoregister.nl/geonetwork/srv/dut/csw?
    request=GetCapabilities&
    service=CSW

Het `antwoord <http://nationaalgeoregister.nl/geonetwork/srv/dut/csw?request=GetCapabilities&service=CSW>`_ is een XML document, conform de CSW 2.0.2 specificatie. Het somt de operaties en filters op die de CSW ondersteunt en de URLs voor het versturen van de requests. Daarnaast bevat het document de beschikbare formaten om metadata op te vragen. Het voert te ver de gehele structuur van het document uit te leggen.

GetRecords
==========
Doorzoeken en opvragen van metadata records. Om metadata op te vragen met GetRecords, kan je bijvoorbeeld dit HTTP GET request gebruiken:

::

    http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?
    service=CSW&
    version=2.0.2&
    request=GetRecords&
    namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&
    resultType=results&
    outputSchema=http://www.opengis.net/cat/csw/2.0.2&outputFormat=application/xml&
    maxRecords=10&
    typeNames=csw:Record&
    elementSetName=full&
    constraintLanguage=CQL_TEXT&
    constraint_language_version=1.1.0&
    constraint=AnyText+LIKE+%27%25water%25%27

Dit geeft `metadata terug <http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?service=CSW&version=2.0.2&request=GetRecords&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.opengis.net/cat/csw/2.0.2&outputFormat=application/xml&maxRecords=10&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=AnyText+LIKE+%27%25water%25%27>`_ die over "water" gaat. In een standaard CSW formaat, dat Dublin Core elementen bevat.

Het request bevat veel parameters. De CSW specificatie beschrijft al deze parameters. Hieronder volgt een behandeling van de belangrijkste parameters als je zelf via HTTP requests metadata wilt doorzoeken / opvragen:

**Parameters**
De volgende parameters kunnen handig zijn om aan te passen, voor andere zoekopdrachten:

* outputSchema=http://www.opengis.net/cat/csw/2.0.2 --> gebruik outputSchema=http://www.isotc211.org/2005/gmd voor ISO metadata
* typeNames=csw:Record --> typenames=gmd:MD_Metadata is nodig voor het verkrijgen van ISO metadata
* maxRecords=10 --> het maximum aantal records als zoekresultaten
* elementSetName=full (of: summary)
* constraintLanguage=CQL_TEXT --> er is ook de mogelijkheid XML Filters conform de OGC Filter encoding te gebruiken. CQL_TEXT is vaak handiger in GET-requests, omdat de notatie korter is.
* constraint=AnyText+LIKE+%27%25water%25%27 --> dit is de zoekterm. Hier wordt een filter gebruikt dat door alle tekst van de metadata zoekt (AnyText), op de term "water". De waarde van het veld constraint moet URL encoded zijn. "AnyText+LIKE+%27%25water%25%27" is  URL gecodeerd van "AnyText LIKE '%water%'". De percentage-tekens zijn wildcards. Het online tooltje: http://meyerweb.com/eric/tools/dencoder/ kan helpen bij het coderen/decoderen van de waardes.

**Optioneel**
* resultType=results --> "results" levert de zoekresultaten zelf (metadata records) op, "hits" levert alleen de aantallen zoekresultaten op.
* startPosition=<integer> --> in combinatie met maxrecords te gebruiken, om door grotere hoeveelheden records te lopen. Zie hieronder bij **Paginering van zoekresultaten**.

Parameters die niet aangepast hoeven te worden:

* outputFormat=application/xml --> Hoe de metadata gecodeerd wordt. Dit is voor NGR altijd application/xml (zie de Capabilities bij het stukje over GetRecords).
* request=GetRecords
* service=CSW
* version=2.0.2


**Paginering van zoekresultaten: maxRecords en startPosition**
Het NGR bevat veel metadata records. Door de maxRecords en startPosition parameters te gebruiken kan je in delen metadata records opvragen. Dus bijvoorbeeld eerst de eerste 10 (maxRecords=10&startposition=1), dan de volgende 10 (maxRecords=10&startposition=11) en weer de volgende 10 (maxRecords=10&startposition=21).


Zoeken via filters
------------------
Het NGR staat allerlei zoekopdrachten toe, ook via de CSW. De meestgebruikte is alle tekst doorzoeken.

Zie de bespreking van de parameter "constraint" voor een eenvoudig voorbeeld.

Naast AnyText ondersteunt het NGR vele andere zoekparameters, die heel gericht op een bepaald metadata element zoeken. De Capabilities sommen deze op in de lijsten met Queryables (SupportedISOQueryables en AdditionalQueryables). Bijvoorbeeld: ServiceType om te zoeken op het type services, zoals op een zogenaamde "view" service:
constraint=ServiceType='view'
Gecodeerd:constraint=ServiceType%3D%27view%27

Of de titel moet een term bevatten:
constraint=Title LIKE '%dijken%'
Gecodeerd:constraint=Title LIKE '%25dijken%25'

Request: http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?request=GetRecords&service=CSW&version=2.0.2&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.opengis.net/cat/csw/2.0.2&outputFormat=application/xml&maxRecords=10&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=Title%20LIKE%20%27%25dijken%25%27&nextRecord=20

Het voert te ver hier alle zoekparameters te bespreken. Zie daarvoor de CSW specificatie.


GetRecordById
=============
Het request GetRecordById kan handig zijn om naar 1 specifiek metadata record te verwijzen. Het request is korter dan GetRecords. Een voorbeeld is:

http://nationaalgeoregister.nl/geonetwork/srv/dut/csw?SERVICE=CSW&version=2.0.2&REQUEST=GetRecordById&elementSetName=full&OutputSchema=http://www.isotc211.org/2005/gmd&ID=85fdc4ee-05fa-455d-bf11-eb0b927e6f77

Dit request vraagt in ISO formaat de metadata op van het record met ID=85fdc4ee-05fa-455d-bf11-eb0b927e6f77. Dit ID is te vinden door de resultaten van GetRecords te bestuderen.

Voorbeelden
===========

* `Eerste 10 records (1 t/m 10) <http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?service=CSW&version=2.0.2&request=GetRecords&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.opengis.net/cat/csw/2.0.2&outputFormat=application/xml&maxRecords=10&startposition=1&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=AnyText+LIKE+%27%25water%25%27>`_

::

    http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?
    service=CSW&
    version=2.0.2&
    request=GetRecords&
    namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&
    resultType=results&
    outputSchema=http://www.opengis.net/cat/csw/2.0.2&
    outputFormat=application/xml&
    maxRecords=10&
    startposition=1&
    typeNames=csw:Record&
    elementSetName=full&
    constraintLanguage=CQL_TEXT&
    constraint_language_version=1.1.0&
    constraint=AnyText+LIKE+%27%25water%25%27

* 10 volgende records (11 t/m 20):

  http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?service=CSW&version=2.0.2&request=GetRecords&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.opengis.net/cat/csw/2.0.2&outputFormat=application/xml&maxRecords=10&startposition=11&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=AnyText+LIKE+%27%25water%25%27


* Vraag om Dublin Core metadata (beperkte set).
  Opvragen van Dublin Core metadata gaat met de parameters::

    &outputSchema=http://www.opengis.net/cat/csw/2.0.2&typeNames=csw:Record

  Dus bijvoorbeeld een geheel request:

  http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?service=CSW&version=2.0.2&request=GetRecords&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputFormat=application/xml&maxRecords=10&outputSchema=http://www.opengis.net/cat/csw/2.0.2&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=AnyText+LIKE+%27%25water%25%27

* Vraag om ISO-metadata 
  Opvragen van ISO metadata (meer gegevens dan Dublin Core) gaat met de parameters::

    &outputSchema=http://www.isotc211.org/2005/gmd&typeNames=gmd:MD_Metadata

  Dus bijvoorbeeld een geheel request:

  http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire?service=CSW&version=2.0.2&request=GetRecords&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputFormat=application/xml&maxRecords=10&outputSchema=http://www.isotc211.org/2005/gmd&typeNames=gmd:MD_Metadata&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&constraint=AnyText+LIKE+%27%25water%25%27

INSPIRE metadata
----------------
Op zoek naar alleen INSPIRE metadata (en niet alle Nederlandse metadata)? Gebruik dan in plaats van de URL:

http://nationaalgeoregister.nl/geonetwork/srv/dut/csw


De URL van het INSPIRE endpoint:

http://nationaalgeoregister.nl/geonetwork/srv/dut/inspire

Deze laatste URL is ook van een gewone CSW, maar de inhoud betreft alleen de metadata voor INSPIRE.

Tooling met CSW ondersteuning 
=============================

In veel gevallen ben je sneller af als je een bestaande bibliotheek gebruikt om CSW te ontsluiten:

* `OpenLayers <http://dev.openlayers.org/docs/files/OpenLayers/Protocol/CSW/v2_0_2-js.html>`_
* `GXP <http://gxp.opengeo.org/master/examples/catalogue.html>`_
* `Geonetwork Widgets <http://nationaalgeoregister.nl/geonetwork/apps/js/GeoNetwork/examples>`_

Veel GIS pakketten bieden ondersteuning voor CSW via open source plug-ins. 

* `esri <https://github.com/Esri/geoportal-server/tree/master/components/desktop/CswClient/trunk>`_
* `QGIS <http://hub.qgis.org/projects/cswclient>`_