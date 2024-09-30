# Exclusion of solar panel areas in Segovia
1. exporting the selected feature of sgovia as geopackage
2. Clipping solar exclusion with Segovia province
   ```python
    processing.run("native:clip",
   {'INPUT':'C:/Users/profesorieu/Downloads/enre_cyl_excl_foto.gpkg|layername=enre_cyl_excl_foto',
   'OVERLAY':'C:\\Users\\profesorieu\\Downloads\\segovia_province.gpkg|layername=prov_cyl_recintos',
   'OUTPUT':'ogr:dbname=\'C:/Users/profesorieu/Downloads/solar_exclusion_segovia.gpkg\' table="solar panels" (geom)'})
   ```
3. Dissolve the solar exclusion in segovia
