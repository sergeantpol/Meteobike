# Analyzing your Meteobike Data in QGIS

[QGIS](https://qgis.org) is a free and open-source Geographic Information System (GIS) that allows us to perform advanced geographical analysis of the data obtained from one or multiple Meteobike systems. It runs on Linux, Mac OSX, Windows and other systems. 

## Installing QGIS

Download [QGIS](https://qgis.org) and follow installation instructions, incluing the correct Python version.

## Importing and map Meteobike data in QGIS

In a first step we would like to import and map your Meteobike dataset.

### Set-up OSM as background map in QGIS

Open [QGIS](https://qgis.org) and create a new project using `Project` > `New` >. Save the new project under `Project` > `Save As...`. 

In the 'Browser' to the left, click on `XYZ Tiles` and double click `OpenStreetMap`. Open Street Map tiles will be shown.  At first, you will see the entire Earth. Use the cursor and zoom fuction to navigate to the city of your data.

![Images/QGIS_OSMFull.png](Images/QGIS_OSMFull.png)

### Importing Meteobike data to QGIS

You can import any .csv file into QGIS. This means, you can directly input the raw datafiles from the Meteobike system. Alternatively, you can also data that has been filtered and merged into a single file from multiple systems. Use Menu `Layer` > `Add Layer` > `Add Delimited Text Layer...` to add data from one or multiple meteobike systems.

![Images/QGIS_CSVImport.png](Images/QGIS_CSVImport.png)

Select the file of your interest (by clicking on the `...`-button in the upper right). 

Under 'File Format' select `CSV (comma separated values)`, under 'Record and Field Options' select `First record has field names` and `Detect field types`. Under 'Geometry Definition' it should automatically select 'Longitude' as `x field` and 'Latitude' as `y field`. 'Sample Data' displays your measurement dataset. Check if everything looks OK and then click `Add`.

Your measurements will be displayed as single points. Right click on the new layer and select `Properties...` to customize the display.

You can display the measured values as numeric labels next to the points. To do this, select 'Labels' and choose for example the 'Temperature' field to show numeric values of temperatures measured at that point:

![Images/QGIS_LayerOptions.png](Images/QGIS_LayerOptions.png)

Each point is displayed along with the measured temperature:

![Images/QGIS_Labels.png](Images/QGIS_Labels.png)

You can further color-code the points based on measured temperatures. Right click again on the new layer and select `Properties...`. Here you can select `Symbology` and choose `Graduated` as the model:

![Images/QGIS_ColorByValue.png](Images/QGIS_ColorByValue.png)

Under 'Column' select the field from the Meteobike dataset you would like to colorize (e.g. temperature differences). Unter 'Method' choose `Color`. Under 'Color ramp' you can choose from a variety of color bars (and also invert them, as done in the example). Below is the list that assigns colors to values. The example uses Quantile mapping with 13 classes. Click `Classify` to update the color ramp, then `OK` to apply it to the map:

![Images/QGIC_SampleGraduated.png](Images/QGIS_SampleGraduated.png)

## Create statistics of temperatures in a specific area.

Next assume, you would like to calculate average temperatures measured in different parks and contrast them. To d this, you need to select points based on geographic location - in some areas in complex, irregular shapes. One option to achieve this is through a 'spatial join' - a basic geographic operation selecting elements that are within another one. 

First you have to define the geographic areas in which you would like to sample measurements. In our example we will select temperatures in different parks, however, this approach can be applied to any other geographic dataset - including imported shape files, 'Local Climate Zones' (LCZ) or raster elements (see below for raster examples).

### Create polygons of the areas of interest

Before you draw the areas, you must create a new layer. Select Menu `Layer` > `Create Layer` > `New Shapefile Layer...`. Under 'File name' select an appropriate name (here, we will call it `parks.shp`) and use the `...`-button to store the shape file locally. As 'Geometry type' choose `Polygon`.

You can later add properties to each polygon such as a name of the park (or LCZ, size of the park etc.). As an example, we will now simply create a field `Name` to enter the known local name of each park. Enter "Name" and and click `Add to Fields List`. Then click `OK` to create the shape file.

Under `Layers` you will now see a new layer called "Parks". Right-click on the 'Parks'-layer and select `Toggle Editing`. Then click the polygon-drawing icon (![Images/QGIS_Polygon.png](Images/QGIS_Polygon.png)) to draw a first park area:

![Images/QGIS_DrawPolygon.png](Images/QGIS_DrawPolygon.png)

Close the polygon by right-clicking. A dialog will appear to enter ID and Name (and any other properties you have previously defined). Enter and click `OK`:

![Images/QGIS_PolygonDialog.png](Images/QGIS_PolygonDialog.png)

You can finish here, or add additional polygons for additional parks. In the end, after drawing one or multiple polygons, click again on `Toggle Edit` and save the shape file layer.

### Calculate statistics from points within polygon

In a next step you would like to select all measurements inside the selected polygon and calculate statistics based on only those points taht fall within the area. This is a more complex task, so you need the "Toolbox" for this. Select menu `Processing` > `Toolbox`. The toolbox appears on the right hand side of the map:

In the toolbox choose select `Vector general` > `Join attributes by location (summary)`:

![Images/QGIS_JoinAttributesLoc.png](Images/QGIS_JoinAttributesLoc.png)

As 'Input Layer' choose your polygon-layer (i.e. 'parks.shp'). As join layer choose your points with temperatures from the meteobike dataset. Under 'Geometric predictate' you should choose `contains` and under 'Fields to summarize' you select the field from the meteobike dataset to create statistics from (i.e. Temperature). Then click on `run`. This will first select all points that fall within the polygon and then calculate statistics from those points and write them to the polygon as attributes. A new layer `Joined layer` will be created. You can rename the layer.

Right-click on the new `Joined layer` and select `Open Attribute Table`. You will now see a list of all parks with the statistics of the points (i.e. temperatures) displayed:

![Images/QGIS_Table.png](Images/QGIS_Table.png)

### Create a gridded map of the heat island

Essentially the same procedure can also be used to aggregate the measured datapoints in a gridded map. QGIS has an option to create different grids as polygon layers. To create a grid, select menu `Vector` > `Research Tools` > `Create grid...`:

![Images/QGIS_CreateGrid.png](Images/QGIS_CreateGrid.png)

Because it is trendy, you can choose as grid type `Hexagon (polygon)`. To define the area that should be gridded, you can `Select canvas extent` by clicking on the `...` button. Choose an appropriate grid size, e.g. 250 m and click `Run`. A new grid layer will be generated.

![Images/QGIS_HexagonGrid.png](Images/QGIS_HexagonGrid.png)

In the next step you can repeat what has been described above for the parks, but replace the parks with the grid polygons - Go to the `Toolbox` and select again `Vector general` > `Join attributes by location (summary)`. As 'Input Layer' choose the newly created grid layer )(the hexagons). As join layer choose again your points with temperatures from the meteobike dataset. It is recommended to select a limited number fields for startstics (only the ones you need) otherwise the operation will take a long time to be computed.

A new grid layer will be generated which is showing only cells where measurements are located within. You can right-click the new layer and choose `Properties...` attribute a color coding to the grid cells under `Symbology`. Choose `Graduated` as the model.

![Images/QGIS_GridColoring.png](Images/QGIS_GridColoring.png)

This way you finally get fancy heat map:

![Images/QGIS_HexagonHeatMap.png](Images/QGIS_HexagonHeatMap.png)

## Combining the Meteobike data with raster data

Some data sources such as Digital Elevation Models (DEM) or vegetation indices are provided in raster format, rather than vector format. In some application we would like to attribute values from rasters to our Meteobike measurements, or perform more detailed releif analysis.

### Importing a DEM and displaying the countour lines

As an example, we can import a DEM and combine it with the measurements from our Meteobikes. A free source for elevation data  ASTER Global DEM V2  is the [Global Data Explorer](https://gdex.cr.usgs.gov/gdex/), which can be obtained from a server at USGS and NASA. Use the navigation tools on the website to zoom to the area of your interest. With the rectangular selection tool you can select an area to be downloaded:

![Images/QGIS_DataExplorer.png](Images/QGIS_DataExplorer.png)

If you create a free user account, you can download the ASTER Global DEM V2 in GeoTIFF format:

![Images/QGIS_DataExplorerDownload.png](Images/QGIS_DataExplorerDownload.png)

Save the '.tif' file locally on your machine. Then import the ASTER DEM into QGIS using the menu `Layer` > `Add Layer` > `Àdd Raster Layer`. Choose the previous '.tif' file with the ASTER Global DEM V2 as source:

![Images/QGIS_ImportRaster.png](Images/QGIS_ImportRaster.png)

Right-click to change the appearance of the DEM to have different elevations displayed differently. To add countour lines go to the menu `Raster` > `Extraction` > `Contour...`.

![Images/QGIS_Contour.png](Images/QGIS_Contour.png)

You can now display your Meteobike data along the digital elevation model:

![Images/QGIS_DEMSample.png](Images/QGIS_DEMSample.png)

### Merge data from the DEM with the Meteobike data

To attribute data from a raster dataset to the point-measurements, go to the menu `Processing` > `Toolbox`. As an example, we will now attribute the elevation from the DEM to each measurement location of the Meteobike. In the Toolbox choose `SAGA` > `Vector <-> raster` > `Add raster values to points`. SAGA (System for Automated Geoscientific Analyses) is a free, hybrid, cross-platform GIS software that is included in QGIS as a plug-in. It contains many more specific scientific tools to analyze data.

![Images/QGIS_RasterToPoints.png](Images/QGIS_RasterToPoints.png)

Choose the Meteobike Dataset as the 'Points' and the DEM as the 'Grid'. As points do not fall directly in teh center of pixels, you may ant to interpolate the data between pixels (e.g. here 'Interpolation' `[1] Bilinear interpolation`). Click `Run` to create a new point layer that has the original Meteobike data combined with the data from the DEM (elevation). You find the elevation from the DEM in the very last column of the 'Attribute Table'. You can now display the data with the elevation from the DEM. 

![Images/QGIS_SampleDEMPoints.png](Images/QGIS_SampleDEMPoints.png)

Note that the meteobikes already measured elevation (GPS Altitude) that should actually match the elevation data from the DEM - so there is currently no added value of doing this. Nevetheless, you can apply this procedure to any raster dataset (slope, catchment area, land cover data). There is a whole range of useful terrain analysis tools available in QGIS / SAGA for further analysis.
