#######################
Databronnen en datasets
#######################

Nederland kent een rijk aanbod aan georegisters- en dataportalen. Op deze pagina lees je welke data zijn, waar ze staan en hoe je evt. de beschikbare vector data kan aanspreken.

================================================================== ========================================================
Naam                                                               URL
================================================================== ========================================================
:ref:`Nationaal GeoRegister <data-ngr>`                            http://nationaalgeoregister.nl/geonetwork/srv/dut/search
:ref:`Publieke Dienstverlening op de Kaart <data-pdok>`            https://www.pdok.nl
:ref:`data.overheid.nl <data-overheid.nl>`                         https://data.overheid.nl/
:ref:`Provinciaal GeoRegister <data-pgr>`                          http://www.provinciaalgeoregister.nl/georegister/
:ref:`Atlas Leefomgeving <data-atlas-leefomgeving>`                https://www.atlasleefomgeving.nl/
:ref:`Atlas Natuurlijk Kapitaal <data-atlas-nat-kap>`              http://www.atlasnatuurlijkkapitaal.nl/
================================================================== ========================================================

.. _data-ngr:

*********************
Nationaal GeoRegister
*********************

*Het Nationaal Georegister (NGR) is dé centrale voorziening voor het beschrijven en ontsluiten van geo-informatie van Nederland. Het NGR bevat het overzicht van beschikbare datasets en services. Deze datasets kunt u in veel gevallen direct downloaden en de services kunt u bekijken of in uw eigen toepassing gebruiken.*

**URL:** http://nationaalgeoregister.nl/geonetwork/srv/dut/search

**Toegang tot vector data**: filtreer Brontype op: ``dataset`` of ``service``. Filtreer Online Bronnen op: ``OGC:WFS``, ``ATOM``.

.. _data-pdok:

************************************
Publieke Dienstverlening op de Kaart
************************************

*Publieke Dienstverlening Op de Kaart (PDOK) is een centrale voorziening voor het ontsluiten van geodatasets van nationaal belang. Dit zijn actuele en betrouwbare gegevens voor zowel de publieke als private sector. PDOK stelt digitale geo-informatie als dataservices en bestanden beschikbaar. De meeste PDOK diensten zijn gebaseerd op open data en daarom voor iedereen vrij beschikbaar.*

**URL:** https://www.pdok.nl

**Toegang tot data:** https://www.pdok.nl/nl/producten/pdok-services/overzicht-urls geeft een overzicht van alle PDOK datasets en :ref:`webservices <services>`. Je kan ook gebruik maken van de :ref:`PDOK plugin voor QGIS <qgis-pdok-inspire-plugins>`.

.. _data-overheid.nl:

****************
data.overheid.nl
****************

**URL:** https://data.overheid.nl/

**Toegang tot vector data:** filtreer op ``download``, ``ogc:wfs``, ``ogc:kml``, ``ogc:gml``, ``inspire atom``, ``SHAPE``

.. _data-pgr:

***********************
Provinciaal GeoRegister
***********************

*Het Provinciaal GeoRegister (PGR) is een digitaal register, waarin provincies hun provinciale geo-informatie op een duidelijke en gestructureerde wijze vindbaar maken.*

**URL:** http://www.provinciaalgeoregister.nl/georegister/

**Toegang tot vector data:** Het PGR biedt enkel downloadbare ``GML`` en ``SHAPE`` bestanden die per email gestuurd worden. Deze bestanden kun je m.b.v. :ref:`QGIS <downloaden-qgis>` transformeren naar bijv. GeoJSON.

.. _data-atlas-leefomgeving:

******************
Atlas Leefomgeving
******************
 
*In de Atlas Leefomgeving staat veel basisinformatie over uw leefomgeving, het overheidsbeleid en de invloed van factoren zoals lucht of natuur op de gezondheid.*

**URL:** https://www.atlasleefomgeving.nl/

**Toegang tot vector data:** 

1. ga naar http://www.atlasleefomgeving.nl/kijken
2. open een kaart bijv. ``Cultureel Erfgoed`` -> ``Rijksmonumenten``
3. open de ``Bijsluiter``
4. klik op ``Over de Kaart``
5. klik op ``Distributie``
6. copy/paste de URL onder ``Locatie dataset`` en vervang ``wms`` door ``wfs``. Eindigt de URL op ``ows``? Je kan het direct als een :ref:`WFS <wfs>` endpoint aanspreken.

.. _data-atlas-nat-kap:

*************************
Atlas Natuurlijk Kapitaal
*************************

*ANK is het platform voor informatie over ons natuurlijk kapitaal. Denk daarbij aan alle diensten die de natuur ons levert. U treft op ANK informatie over de staat van het natuurlijk kapitaal in Nederland voor ons dagelijks leven en over de wijze waarop het natuurlijk kapitaal beter en duurzamer benut kan worden. Het doel: dat we duurzaam gebruik kunnen maken van de waarden die onze omgeving ons biedt.*

**URL**: http://www.atlasnatuurlijkkapitaal.nl/

**Toegang tot vector data:**

1. ga naar http://www.atlasnatuurlijkkapitaal.nl/kaarten
2. open een kaart bijv. ``Voedsel`` -> ``Mosselzaadinvanginstallaties``
3. open de Bijsluiter
4. klik op ``Over de Kaart``
5. klik op ``Distributie``
6. copy/paste de URL onder ``Locatie dataset`` en vervang ``wms`` door ``wfs`` indien aanwezig. Eindigt de URL op ``ows``? Je kan het direct als een :ref:`WFS <wfs>` endpoint aanspreken.

.. *********************
.. Ruimtelijkeplannen.nl
.. *********************

.. TODO

.. .. _bag:

.. *******************************************
.. Bassisregistratie Adressen en Gebouwen (BAG)
.. *******************************************

.. TODO: schrijf een algemene beschrijving van dataset: waar te halen, wat zit erin, etc. 

.. TODO: link naar apps, analyses, visualisaties, etc. die de BAG als basis gebruiken. 

.. *******************************
.. Actueel Hoogtebestand Nederland
.. *******************************

.. TODO: schrijf een algemene beschrijving van dataset: waar te halen, wat zit erin, etc. 

.. TODO: link naar apps, analyses, visualisaties, etc. die de BAG als basis gebruiken. 
