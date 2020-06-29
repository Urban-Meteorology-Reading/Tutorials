.. _DSMGenerator:

Spatial data - Generate a digital surface model
===============================================

Introduction
------------
One reason not exploiting tools such as UMEP is the lack of appropriate geodata.
In this tutorial you will generate a `DSM <http://umep-docs.readthedocs.io/en/latest/Abbreviations.html>`__ and `DEM <http://umep-docs.readthedocs.io/en/latest/Abbreviations.html>`__ from scratch using data sources available online. You will make use of built-in functionallity in QGIS and the `DSM Generator <https://umep-docs.readthedocs.io/en/latest/pre-processor/Spatial%20Data%20DSM%20Generator.html>`__ in UMEP. This tutorial also includes suggestion on how to create other basic datasets such as land cover as well as population density information.

Steps
~~~~~

#. Generate a DSM from open source data and refine the result
#. Generate your howetown DSM  
#. Learn about retrieving other datasets used in UMEP modelling

Initial Steps
-------------
UMEP is a Python plugin used in conjunction with
`QGIS <http://www.qgis.org>`__. To install the software and the UMEP
plugin see the `getting started <http://umep-docs.readthedocs.io/en/latest/Getting_Started.html>`__ section in the UMEP manual.

Generate a DSM and refine the result
------------------------------------
As a test you will initially make use of the Gothenburg dataset used in other UMEP tutorials.

- If not already downloaded, download and extract the dataset. 

  :download:`Datasets for Gothenburg, Sweden </data/Goteborg_SWEREF99_1200.zip>`

- Load the DEM (**DEM_KRbig.tif**) into a new QGIS project.
- Open *UMEP > Pre-Processor > Spatial Data > DSM Generator*. This is a tool that works as an assistant on creating DSMs from `Open Street Map <https://www.openstreetmap.org>`__ (OSM) building data. It is also capable of using any other vector based building footprint data that includes height information of building polygons. 
- Use the settings as shown in the figure below and click *OK*.

  .. figure:: /images/DSMGenerator1.jpg
   :alt:  none
   :align: center

   The Setting of your first DSM generation.

With the settings as above, the DSM Generator downloads the OSM building data for the specified area and extracts all buildings that have height information of any kind included. In our case the result is not very good since only 8 out of 47 polygons included useful height information. You can compare with the very high quality DSM, **DSM_KRbig.tif**, included in the datasets for this tutorial. 

Ususally height information in the OSM dataset is not very common but for some larger cities the information can be very close to full coverage. You can contribute to the open street map project by adding information on building heights e.g. for your home town. Visit `<www.openstreetmap.org>`__ for more information.

Even though the result is disappointing you now have a good starting point to producing a higher quality DSM. 

- If still open, Close the *DSMGenerator*.
- Open the shapefile **buildings_gothenburg.shp** that you saved from the settings in the figure above.

As you can see, many more buildings are present within the DEM extent than those that seen in **dsm_osm_v1**.

- Open the attribute table for **buildings_gothenburg** (Right-click on layer and choose *Open Attribute Table*

Here you see all the relevant attributes that have been retrieved from the OSM-data with regards to building form and function. Each line is a building polygon. Many of the attributes are empty and have no infromation (*NULL*). The column **bld_height** is the most important column here. As you can see, only a few objects have information here. Now we will add some basic height information for all buildings in the area. This area is the old part of Gothenburg and all builiding are generally of somewhat similar height (appr. 18 m agl).

- Select all objects (sometimes called *features*) with a building height higher than 0 ("bld_height > 0") by using the *Select by Expression* window (eppsilon on top of a yellow square).
- Click the *Invert Selection*- button (two buttons to the right).
- Open the *Field Calculator* (Abacus) and use the settings as shown below:

  .. figure:: /images/DSMGenerator2.jpg
   :alt:  none
   :align: center

   Settings for the Field Calculator.
   
- Click OK on the *Field Calculator*.

Click on the Pen in the attribute window, save your changes and close your attribute table.

- Now open the DSM Generator again and this time use **buildings_gothenburg**  and the *bldg_height* as the attribute to retrieve height info from. Call the output **dsm_osm_v2** and leave all other settings as before.

Now you see all buildings included in your new DSM. Of course, this can be refined much more by setting individual heights for each building footprint. Nevertheless, you now have a rough DSM ready to be used for further anaysis.

Assume that you only have a DEM covering a very large area and you only want to create a DSM for a fraction of the extent. In this scenario it is beneficial to have a background map service that can help you to locate yourself and zoom in appropriatly. There are a number of plugins available for such services. 

- If not already installed, go to the *Plugins > Manage and Install Plugins* and install *QuickMapServices*. When installed, you can add the standard OSM from *Web > QuickMapServices > OSM > OSM Standard*. Now you can also see which buildings were not included in your first DSM. 

Note that roof structures are not included in this technique (i.e. all roofs are assumed flat). To include tilted roofs and other roof structures, more sophisticated techniques should be used, for example using LiDAR point cloud data. To find out more, see this `tutorial <LidarProcessing>`.

Download DEM from available databases
-------------------------------------
Data on ground elevation at various spatial resolutions is found in various places throughout the internet. In general, the larger coverage area, the lower the spatial resolution. Spatial resolution is essential if your study area includes large variations in height at short distances. If your study area is located on a flat plain, even a coarse spatial resolution (e.g. 1 km) could be ok. 

Regarding global coverage, `gisgeography <https://gisgeography.com/free-global-dem-data-sources/>`__ provides a nice list of available data sources. Some of the links are outdated but you still get a good overview of available data. Also, `OpenDEM <https://opendem.info/>`__ is a good access point to get hold of elevation data. Usually, national datasets are availble at much higher spatial resolution e.g. `<https://digimap.edina.ac.uk/>`__ for the UK. These national databases are usually not fully open and are sometimes password protected. 

- (Optional) Now try to generate a DSM for your home neighboor hood where you grew up. Use the SRTM 1 arc resolution dataset found here: `<https://opendem.info/link_open.html>`__. Remember not to produce too large an area as downloading from OSM can be slow and restricted in size. This will take you either NASA or USGS for download. You need an account but it is free to sign up.

Other datasets for UMEP
-----------------------
Land cover infromation is one important infromation required for UMEP modelling and data can be found in various places. One European dataset that can be exploited is the `Urban altas <https://land.copernicus.eu/local/urban-atlas>`__ or the `Global land cover <https://lcviewer.vito.be/>`__-dataset. Actually, Open Street Map can also be used to create basic land cover map by exploiting the full information (not just buildings) included. There is a plugin called *QuickOSM* that can be used to download OSM-data directly into your QGIS project.

Regarding population, the `Gridded Population of the World (GPW4) <https://sedac.ciesin.columbia.edu/data/collection/gpw-v4>`__ can be used to derive population density. 

