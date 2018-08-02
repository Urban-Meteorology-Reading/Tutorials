.. _SUEWSWUDAPT:

Urban Energy Balance - SUEWS and WUDAPT
=======================================

Introduction
------------

.. caution:: This tutorial is not ready for use. Work in progress.

In this tutorial you will generate input data for the 
`SUEWS <http://suews-docs.readthedocs.io>`__ model and simulate spatial 
(and temporal) variations of energy exchanges within an area in New York City using local climate zones derived within the `WUDAPT <http://www.wudapt.org/>`__ project. The World Urban Database and Access Portal Tools project is a community-based project to gather a census of cities around the world.

.. note:: This tutorial is currently designed to work with QGIS 2.18. It is strongly recommended that you goo through the :ref:`SuewsSpatial` tutorial before you go through this tutrial. 


Objectives
----------

To prepare input data for the SUEWS model using a WUDAPT dataset and analyse energy exchanges within an area in New York City, US.


Initial Steps
-------------

UMEP is a python plugin used in conjunction with
`QGIS <http://www.qgis.org>`__. To install the software and the UMEP
plugin see the `getting started <http://umep-docs.readthedocs.io/en/latest/Getting_Started.html>`__ section in the UMEP manual.

As UMEP is under development, some documentation may be missing and/or
there may be instability. Please report any issues or suggestions to our
`repository <https://bitbucket.org/fredrik_ucg/umep/>`__.


Loading and analyzing the spatial data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: You can download the all the data from `here <https://github.com/Urban-Meteorology-Reading/Urban-Meteorology-Reading.github.io/blob/master/other%20files/SUEWSWUDAPT_NYC.zip>`__. Unzip and place in a folder where you have read and write access to. The LCZ data for various of cities are also available from the `WUDAPT <http://www.wudapt.org/>`__ portal.


- Start by loading the raster dataset (**NYC_LCZ.tif**) into an empty QGIS project. This dataset is referenced to the WGS84 CRS (ESPG:4326). 
- You can set the correct colors for your LCZ raster by opening the LCZ converter at *UMEP > Pre-Processer > Spatial data > LCZ converter*. In the upper right corner, choose the LCZ raster and press *Color Raster* and then close the *LCZ Converter*.


Vector grid generation
----------------------

A vector polygon grid is required for specifying the extent and resolution of the modelling.You will make use of a built-in tool in QGIS to generate such a grid.

#. First zoom in to Manhattan as shown in the figure below

.. figure:: /images/SUEWS_WUDAPT_NYC_ManhattanZoom.jpg
   :alt:  none
   :width: 100%

   Zoom in the Manhattan island.

#. As WGS84 (EPSG:4326) is in degree coordinates and maybe you want to specify your grid in meters, you need to change the CRS of your current QGIS-project. Click on the globe at the bottom right of your QGIS windoe and select *ESPG:26918* as your 'on the fly' CRS.
#. Open vector grid at *Vector > Research Tools > Vector grid*.
#. Select the extend of your canvas by clicking the ... next to *Grid
   extent (xmin, xmax, ymin, ymax)* and select *Use layer/canvas
   extent*. As you can see the units in now in meters and not in degrees.
#. Select *Use Canvas Extent*.
#. Specify the desired grid spacing to 5000 meters. This will save time later on. Of course you can set it a much smaller number if you have the time to wait when the model performs the calculations later on.
#. Make sure the output is in polygons, not lines.
#. Create as temporary layer.
#. Save your grid by right-click on the new layer in the *Layers Panel* and choose *Save as...*. Here it is very imporant that you save in the same CRS as you other layers (ESPG:4326). Save as a shape file.


Population density
------------------
Population density is required to estimate the anthropogenic heat release (Q\ :sub:`F`) in SUEWS. There is a possibility to make use of both night-time and daytime population densities to make the model more dynamic. In this tutorial you will only use a night-time dataset. This dataset can be aqcuired from the *Spatial Data Downloader* in UMEP.

#. Open de spatial downloader at *UMEP > Pre-Processer > Spatial data >
   Spatial Data Downloader*.
#. Select *population density* and select the *GPWv4: UN-Adjusted
   Population Density* closest to the year you intend to model (2010). The values will be in (pp / square kilometer).
#. Make sure your canvas is zoomed out to the entire LCZ map and click
   *Use canvas extent*
#. Now click *Get data*.
#. Save as a geoTiff (*.tif) with the name **GPWv4_2010**.
#. Now you need to calculate population density per grid in units *pp/hectare*. First open the QGIS built-in tool *Zonal statistics* (*Raster > Zonal Statistics*). If the toool is absent you need to activte it by going to *Plugins > Manage and Install Plugins* and add *Zonal statistics plugin*. Open the tool and make the settings as shown below. This will calulate mean population density per grid.

   .. figure:: /images/SUEWS_WUDAPT_NYC_Zonalstat.jpg
      :alt:  none
   
      Settings for the Zonal statistics plugin.

#. Open the attribute table for your **Grid_5000m**-layer (right-click on layer and choose (*Open attribute Table*). 
#. Click the abacus shaped symbol this is the *Field calculator*.
#. Under *Output field name* write "pp_ha, the *Output field type* should be “Decimal number (real)”, and the *Output Precision* can be set to 2.
#. In the expression dialog box write gpw_mean/100, here gpw_mean is the name of your population density field and the 100 is to convert the data from km\ :sup:`2` to ha.
#. Click *OK* and you should have a new field called “pp_ha”.


LCZ converter
-------------

Now you will make use of the *LCZ Converter*-plugin to generate input data for the SUEWS model.

#. Open the LCZ converter at *UMEP > Pre-Processer > Spatial data > LCZ
   converter*.
#. Select the LCZ raster layer at '' LCZ raster''.
#. Select the vector grid you have just created in step 3 at *Vector
   grid* and select the ID field of the polygon grid at *ID field*.
#. By clicking *Adjust default parameters* you can edit the table. This
   table specifies the pervious, trees, grass, etc. fractions for each
   of the LCZ classes. For more information about each of the classes
   see `LCZConverter <http://umep-docs.readthedocs.io/en/latest/Tutorials/SUEWSWUDAPT_Beijing.html>`__.
   If you choose to edit the table, make sure all fractions add up to
   1.0.
#. If you are unsure about the exact fractions for each of the LCZ click
   the tab *Pervious distribution*. Select *Same for all LCZ's*
#. Now you can select your best estimate about the distribution of the
   pervious surface fractions for urban and the tree distribution for
   rural. In addition, also specify the expected height of the trees.
#. Once you are satisfied click *Update Table*.
#. Select add results to polygon.
#. Add a file prefix if desired.
#. Finally select an output folder where you would like to receive the
   text files and click *Run*.

This should generate 3 text files, one with the land cover fractions,
one with morphometric parameters for buildings and one for trees for
each grid cell of the polygon grid.


SUEWS
-----

Before running SUEWS, you will need to
prepare some of the data required to run it.

#. Open SUEWS prepare at: *UMEP > Pre-Processer > SUEWS prepare*.
#. Under *vector polygon grid* specify your created vector grid and the *ID field*.
#. Select the location of the *Meteorological file* that was included in the input data, the *Building morphology*, *Tree morphology* and *Land cover fractions* from the step above and the population density (pp_ha) in the dropdown list.
#. Enter the start and end of day light savings time and the UTC offset.
#. Specify the *Leaf cycle* = winter when initialising in January.
   Unless the user has better information initialise the *Soil moisture
   state* at 100 %.
#. Select an output folder where the initial data to run SUEWS should be
   saved and press *Generate*.
#. Open SUEWS at *UMEP > Processer > Urban Energy Balance > Urban Energy
   Balance (SUEWS/BLUEWS, advanced).*
#. Specify the *Temporal resolution of forcing data (minutes)* to be 60 minutes.
#. Specify the *Temporal resolution of output (minutes)* to be 60 minutes.
#. Select the *Input folder* specified in SUEWS prepare and select an output folder for the SUEWS output to be saved in. Finally, click *Run*.


Analysing model reults
----------------------

Here I am.
