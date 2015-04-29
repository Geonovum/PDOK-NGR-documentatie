######################################
Nationaal GeoRegister Developer pagina
######################################

De developer pagina van het nationaal georegister bevat een aantal tips om met data en services uit het register aan de slag te gaan.

Het register is een catalogus met informatie over vooral ruimtelijke datasets en services die door voornamelijk overheden ter beschikking gesteld worden.

In het zoekresultaat wordt middels iconen aangegeven welke API's er beschikbaar zijn voor een bepaalde dataset.

*********************
API's in het Register
*********************

Het register maakt gebruik van open standaarden (api-beschrijvingen) die door dan wel W3C(http://www.w3c.org), OGC(http://www.opengeospatial.org), ISO, JRC (http://ec.europa.eu/dgs/jrc) of geonovum (http://geonovum.nl) beheerd worden. 
Hieronder een lijst van standaarden met korte omschrijving en links om verder te lezen.

W3C:RDF
=======

De catalogus kan geindexeerd worden middels Linked Data RDF, ten behoeve van semantic web applicaties. Het register biedt momenteel (nog) geen SPARQL end-point. Het RDF endpoint is http://www.nationaalgeoregister.nl/geonetwork/srv/dut/rdf.search. Voorbeeld: zoek alle datasets met 'pand':

.. code::

        http://www.nationaalgeoregister.nl/geonetwork/srv/dut/rdf.search?any=pand

**Opmerking:** Uiteindelijk moeten de URIs die in het NGR gebruikt worden geharmoniseerd zijn met bijvoorbeeld PDOK en de stelselcatalogus zodat het mogelijk wordt eenvoudig tussen metadata definities en objecten heen en weer te navagiren. Deze harmonisatie moet nog plaats vinden.

W3C:RSS
=======

De catalogus kan ontsloten worden middels RSS. Diverse RSS endpoints zijn gepubliceerd in het open search description document op http://www.nationaalgeoregister.nl/geonetwork/srv/dut/portal.opensearch. Inspire heeft een aantal additionele vereisten opgesteld ten behoeve van interoperabiliteit rond file-downloads via Atom.

iso:19139
=========

19139 (http://www.iso.org/iso/catalogue_detail.htm?csnumber=32557) is een schema wat de inhoud van metadata documenten beschrijft die middels CSW uitgewisseld worden. Een alternatief voor iso19139 is Dublic Core. Bij het uitlezen van een CSW antwoord dient nagegaan te worden welk schema gebruikt wordt (of in de request dent een target schema meegegeven te worden). Middels het gebruikte schema is het CSW antwoord om te zetten naar bruikbare concepten als titel, auteur, datum. Iso19139 wordt gebruikt om metadata van een dataset (iso19115) of metadata van een service (iso19119) vast te leggen. Geonovum heeft een aantal Nederlandse profielen voor metadata vastgesteld om interoperabiliteit in het Nederlandse domein te faciliteteren (http://www.geonovum.nl/geostandaarden/metadata/destandaarden).

*****************************
Ruimtelijke Download formaten
*****************************

Ruimtelijke datasets kennen afwijkende formaten dan wat je normaal tegen komt. Er zijn diverse tools beschikbaar om data om te zetten naar het gewenste formaat: OGR/GDAL, GeoKettle, Qgis (SPIT). Downloads zijn over het algemeen in de volgende formaten beschikbaar:

GML
===

Geography Markup Language is de meest gebruikte OGC standaard voor vastleggen van ruimtelijke data. Openen in Qgis, Esri, OpenLayers. Met GML kan zowel data uit complexe modellen (Inspire, basis registraties) als eenvoudige tabel-data vastgelegd worden. Over het algemeen kan alleen de laatste categorie direct gevisiualiseerd worden in de huidige GIS paketten.  

KML
===

Origineel ontwikkeld door Keyhole en Google, maar tegenwoordig een OGC standaard. Kan geopend worden in Google Earth, Qgis, OpenLayers en vele anderen.

GeoPackage
==========

Een nieuwe standaard van OGC voor het uitwisselen van ruimtelijke data. Vooral in gebruik op mobiele telefoons vanwege het onderligende SQLite formaat. Kan naast vector en raster data ook tilesets en metadata bevatten. De geometrie wordt als Well Known Binary (WKB) formaat opgeslagen in een geo-kolom. 

GeoJson/TopoJson
================

Formaat dat met name gebruikt wordt bij uitwisseling tussen web-applicaties. TopoJson is een optimalisatie van geojson, waarbij een grens tussen twee vlakken slechts door 1 lijn beschreven wordt.

Shapefile/GDB/e00
=================

Diverse formaten ontwikkeld door Esri Inc. Met name de shapefile wordt ook veel buiten het esri domein gebruikt en door vele ruimtelijke software ondersteund. Naast esri ondersteund bijvoorbeeld ook OGR/GDAL de e00 en GDB formaten.

CSV
===

(punt) komma gescheiden text document waarbij kolommen voor x en y coordinaat opgenomen zijn.

GeoTiff/MrSid/ECW
=================

Diverse formaten voor het vastleggen van raster data. MrSID en ECW kennen een optimalisatie ten behoeve van performance waarbij, ze aggregaties van de data vastleggen op diverse schaal niveau's

DXF
===

Veel gebruikt formaat in de cad wereld. Het omzetten van dxf naar geo is een aardige uitdaging, doordat attribuut data en geometrie in afzonderlijke objecten opgeslagen worden. OGR/GDAL, Esri en FME kunnen DXF converteren.

**********
Projecties
**********

De uitdagingen rond het plat slaan van de aarde tot een platte kaart hebben erin geresulteerd dat er op diverse plekken op de aarde afwijkende projecties gebruikt worden. Er zijn wel enkele wereldwijde projecties (spherical mercator (epsg:900913/epsg:3758), zoals gebruikt door google/bing/OSM en wgs84 (epsg:4326), zoals gebruikt in GPS) echter deze geven in Nederland een te afwijkende kaartwerkelijkheid. In Nederland werken wij bij voorkeur in RD_new (epsg:28992), deze projectie kan echter niet gebruikt worden voor het stuk Noordzee voor onze kust. Voor projectie info updates kun je de website http://georeference.org bekijken. Voor het herprojecteren van geometrien zijn voor de meeste omgevingen omgevingen bibliotheken beschikbaar. 

proj
====

Een C implementatie die zich eenvoudig laat includeren in bijvoorbeeld python

proj4js
=======

Een javascript implementatie van proj

geotools
========

Een Geo toolset voor java, met onder andere herprojecties

dotspatial
==========

Een Geo toolset voor .Net, met onder andere herprojecties

PostGIS/GeoPackage/Oracle
=========================

Je kunt de herprojectie ook aan een database overlaten, de meeste ruimtelijke databases ondersteunen herprojecties

