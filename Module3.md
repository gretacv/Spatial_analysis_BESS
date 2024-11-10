# Obtaining landcover profile of fieldtrip areas (Module 3)

[ChatGPT conversation](https://chatgpt.com/share/6730ea53-1270-8003-9ced-8ab1d4f0428f) covering the topics of projected coordinate systems. 

## Objective:
Obtaining a description of the landcover composition around the field trip stops to compare the data with what was seeing. 

### Geojson with the locations (vector data)
- **source**: `https://github.com/gretacv/Spatial_analysis_BESS/blob/main/fielwork_locations.geojson`, accessed 8th of November 2024
- **projection**: EPSG:4326
- **attribute table**: single field with the name of the location

## Step 1: Changing the projection of the vector data from a geographic coordinate system to a projected one. 
The projected coordinate system allows to use the metric system to use distances rather than decimal degrees. Decimal degrees correspond to different metric distances depending on the latitude (cf ChatGPT conversation above). 
### Reprojecting to projected coordinate system
We choose EPSG:25830, it is the coordinate system used by the data provided by the Castilla y León (CyL) data platform and the one use for Spanish national mapping. 
```
 processing.run("native:reprojectlayer", {'INPUT':'C:/Users/localuser/Documents/GIS data/fielwork_locations.geojson','TARGET_CRS':QgsCoordinateReferenceSystem('EPSG:25830'),'CONVERT_CURVED_GEOMETRIES':False,'OPERATION':'+proj=pipeline +step +proj=unitconvert +xy_in=deg +xy_out=rad +step +proj=utm +zone=30 +ellps=GRS80','OUTPUT':'ogr:dbname=\'C:/Users/localuser/Documents/GIS data/fieldwork_locations_25830.gpkg\' table="field_locations" (geom)'})
```
## Step 2: From points to polygons, buffering the points
We choose to use 3 km as buffer taking into account how much was walk during the fieldtrip. Previous to the final buffer, we test what the argument SEGMENTS controls. When only using one segment, instead of a circle we obtain a square.
### Buffer
```
 processing.run("native:buffer", {'INPUT':'C:/Users/localuser/Documents/GIS data/fieldwork_locations_25830.gpkg|layername=field_locations','DISTANCE':3000,'SEGMENTS':5,'END_CAP_STYLE':0,'JOIN_STYLE':0,'MITER_LIMIT':2,'DISSOLVE':False,'SEPARATE_DISJOINT':False,'OUTPUT':'ogr:dbname=\'C:/Users/localuser/Documents/GIS data/field_locations_buffer_3km.gpkg\' table="buffer_3km" (geom)'})
```

## Step 3: Obtaining the number of pixels of each type of landcover within the area of interest.
### Landcover data by ESRI derived from Sentinel data
- **source**: `https://livingatlas.arcgis.com/landcoverexplorer/#mapCenter=-3.28600%2C31.34000%2C3&mode=step&timeExtent=2017%2C2021&year=2022&downloadMode=true`, accessed on the 7th of November 2024
- **year**: 2023
- **resolution**: 10 meters
- **projection**: EPSG:32630
- **value**: type of landcover, there are 11 classes, each class refers to a type of landcover, see more in the [metadata page](https://www.arcgis.com/home/item.html?id=cfcb7609de5f478eb7666240902d4d3d].

### Zonal histogram
We are working with categorical data, so the tool Zonal Statistics does not provide usable results. For example obtaining the mean of the category within an area is not interpretable. Instead we use zonal histogram. For each category found in the polygons we obtain a field that contains the number of pixels.
```
processing.run("native:zonalhistogram", {'INPUT_RASTER':'C:/Users/localuser/Documents/GIS data/30T_20230101-20240101.tif','RASTER_BAND':1,'INPUT_VECTOR':'C:/Users/localuser/Documents/GIS data/field_locations_buffer_3km.gpkg|layername=buffer_3km','COLUMN_PREFIX':'LC_','OUTPUT':'ogr:dbname=\'C:/Users/localuser/Documents/GIS data/field_locations_buffer_3km_LC_hist.gpkg\' table="output" (geom)'})
```
## Step 4: Visualisation of the results in a chart
In this case a chart like a barchart is more effective to presents the results of the spatial analysis than a map. We copy paste the [contents of the attribute table](https://docs.google.com/spreadsheets/d/10b4sTBkO6vBV4SIt26eLPq2Xn9wt4IVJqEfDUbu1z0g/edit?pli=1&gid=0#gid=0) into a spreadsheet (Google sheets or Excel) and we visualize the results using those tools directly or other visualization tools like Datawrapper. 
> [View the interactive visualization on Datawrapper](https://datawrapper.dwcdn.net/LeRAy/1/)

