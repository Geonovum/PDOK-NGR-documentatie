##################
Geodata downloaden
##################

In :ref:`webapps` lees je hoe je de geo APIs in Leaflet en OpenStreetMap kan consumeren. Vaak is het ehcter noodzakelijk om een dataset in zijn geheel te downloaden zodat je het met andere gegevens kan combineren en/of een landsdekkende analyse kan uitvoeren.

*******************
Op de desktop: QGIS
*******************

WFS
===

QGIS stelt je in staat om de gegevens van een :ref:`WFS <wfs>` endpoint lokaal op te slaan.

Ga als volgt te werk om een deel van de BAG in QGIS te laden en lokaal in een ander coordinatenstelsel op te slaan.

1. Klik op ``Layer``-> ``Add Layer`` -> ``Add WFS Layer`` in het hoofdmenu van QGIS
2. Klik op ``New``
3. Vul een makkelijk te onthouden naam in het ``Name`` veld
4. Plak de BAG WFS URL (``https://geodata.nationaalgeoregister.nl/bag/wfs``) in het ``URL`` veld en klik op ``OK``
5. Klik op ``Connect``
6. Kies een van de lagen uit de lijst bijv. ``pand`` en klik op ``Add`` 
7. De gekozen laag verschijnt in de ``Layers`` pane (links van de kaart) als ``bag:panden``
8. Klik met de rechtermuisknop op ``bag:panden`` en selecteer ``Save As..``
9. Kies ESRI Shapefile, GeoJSON of KML uit het ``Format`` menu
10. Klik op de ``Browse`` knop en geef aan waar het bestand opgeslagen moet worden
11. Kies ``EPSG:4326 - WGS84`` uit het ``CRS`` menu om de coördinaten naar ``lat/lng`` te transformeren. Zie :ref:`coord_trans` voor meer informatie.
12. Klik op ``OK``

.. image:: images/qgis-vector-save.png
    :align: center

Het nieuwe bestand kun je in CartoDB of Mapbox Studio gebruiken. KML bestanden kun je rechtstreeks in Google Earth visualiseren.

PDOK & INSPIRE plugins
======================

De `PDOK Services Plugin <http://plugins.qgis.org/plugins/pdokservicesplugin/>`_ stelt je in staat om de datasets van `Publieke Dienstverlenign op de Kaart <https://www.pdok.nl/nl/producten/pdok-services/overzicht-urls>`_ te doorzoeken en in QGIS te laden.

 .. image:: images/qgis-pdok-plugin.png
  :align: center

De `INSPIRE Nederland <https://github.com/warrieka/inspireNL>`_ plugin stelt je instaat om door de `INSPIRE datasets <http://inspiredatagids.nl/over-inspire>`_ te zoeken en deze in QGIS te laden.

.. image:: images/qgis-inspire-plugin.png
  :align: center

Het is ook mogelijk om per INSPIRE thema, annex en/of organisatie te zoeken. 

QGIS plugins kun je via ``Plugins`` -> ``Manage and Install Plugins...`` menu installeren en beheren.

****************************
In de terminal: ogr2ogr (EN)
****************************

The :ref:`ogr2ogr <ogr2ogr tutorial>` utility allows for reading and writing of many different vector formats. The most basic use of *ogr2ogr* is constructed as::

    ogr2ogr -f output_format destination source layer

Getting the footprints of the first 15000 BAG buildings from the WFS endpoint as GeoJSON is achieved as::

    $ ogr2ogr -f GeoJSON footprints.geojson
    WFS:"http://geodata.nationaalgeoregister.nl/bagviewer/wfs"
    bagviewer:pand

You can use :ref:`ogrinfo <ogrinfo>` to discover which layers are contained in a WFS endpoint, see the :ref:`ogr2ogr tutorial <ogr2ogr tutorial>`_ for more information.

.. ****************************
.. ATOM
.. ****************************

