***********************
Tile Map Service (TMS) 
***********************


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

Vervolgens kun je dan een tms laag definieren als:

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
