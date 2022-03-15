# Detrender Tool - Python Solution to Detrend a river Digital Elevation Model (DEM)


## Welcome
The Detrended DEM is a valuable information for fluvial geomorphologic analyses. *Riodetrend* found its roots on the need for an open source, freely distributable algorithm for performing river DEM detrending. The algorithm removes the overall valley slope (thalweg slope) for obtaining non-slanted pixel elevations by fitting a piecewise regression surface on Thalweg points (required as input) and removing it from the original river DEM.


### Data Flow Diagram
<img src="https://media.github.tik.uni-stuttgart.de/user/2363/files/537af300-751d-11eb-87ba-455bd7ca31ec" width = "800" />

### Code Structure (UML)
<img src="https://media.github.tik.uni-stuttgart.de/user/2363/files/877f1280-7608-11eb-9680-db2907106bc6" width = "800" />

### Clone the repository
    $ git clone https://github.com/ricardovobarros/riodetrend.git
    
### Requirements
Python 3.0\
- For generating Thalweg: QGIS 

Libraries:
- `matplotlib.pyplot`
- `numpy`   
- `pandas`
- `geopandas`
- `gdal`

In-built Python libraries:
- `os`
- `glob`
- `sys`
- `time`
- `logging`

### Input Instructions 
To run *riodetrend*, you have to provide on terminal the local address of two folders
which contains respectively raster file(s) and shape file(s) (in this order). Alternatively, you can substitute the files in ```geodata_example``` in the repository and run `detrender.py`.

_Important:_ The number of files has to be the same for both folders so that each raster file corresponds to a Thalweg shapefile. 

To ensure the compatibility between shapefiles and rasters, it is recommended that the files' names end with number, example: "raster_1" and "thalweg_points_1", "raster_2" and "thalweg_points_2".


#### Input Raster(s)
While preparing DEM rasters, make sure:
 - DEM of the river should not have tributaries. It must have only on main path of the river.
 - Clip your DEM in a **optimized** way. That means, this tool won't be able to detrend the DEM
 if the clipped raster files are curved. Please see examples in the folder `geodata_example`.
 
#### Input Shape 
Follow the step-by-step below to create a desirable Thalweg shapefile input to run *riodetrend*.

__The shapefiles do not contain the information of the elevation (z).__ 
Thus, it is necessary to obtain this data from the DEM and relate them to the corresponding coordinates (X,Y)
from the shapefile. This process can be done using the **Field Calculator** in QGIS, as shown in following steps:

1. Open each DEM in (QGIS).
1. Create a shapeline with the same projection of the DEM.
1. Use the tool from SAGA `Gradient Vector from Surface` to create a gradient vector field on the DEM.
1. Use the  provided `DEM_style` to paint the DEM. In raster file go to: _properties>>style>>load style_ and choose `DEM_style`
1. Still in properties, inside Band Rendering, change the max and min values to better fit the style on the DEM. Press ok. 
1. With the help of the style and the gradient vector filed draw a Shape Line representing the Thalweg of the DEM.
1. Use the Tool `Points Along Geometry` and choose the previous created line as Input Layer. Define the distance as the larger pixel size of the DEM
1. To save the elevation of the pixels immediately below the Shape points do the following: 
    1. Select the created Shape Points  and open ![Captura de Tela 2021-02-23 às 19 15 04](https://media.github.tik.uni-stuttgart.de/user/2363/files/afbc4080-760b-11eb-8d1b-e16be50c6df0) **Field Caculator** (Upper part, on the right of "ToolBox" button)
    1. Inside **Field Caculator** create a new field, as shown in the Figure 1, with the following properties:
        1. Choose the function _"Expression" >> "Rasters" >> "raster_value"_  
            1. raster_value(DEM name, DEM band number, XY value of Shape Points)  
        1. Output field name = "z" (it must be letter 'z' in lower case)
        1. Output field type = Decimal number (real)




![Captura de Tela 2021-02-23 às 19 35 24](https://media.github.tik.uni-stuttgart.de/user/2363/files/68837f00-760e-11eb-8b39-7c53e029cc8d) \
**Figure 1.** Creating Field named 'z' which saves \
the elevation values of DEM's pixels located at  
the exact Shape Point


----
### Execution
#### From the Terminal



`python detrender.py [(rasters'_file),
                        (shapes'_file),
                        (print_boolean = 1)
                        (band = 1),
                        (detended_limit=10**(-3))]`
+ *rasters' file*: (STRING) path-like of the folder in the system that contains all 
raster files (.tif) to be detrended
+ *shapes' file*: (STRING) path-like string of the folder in the system that contains all 
shape files (.shp)
+ *print_boolean*: (Optional INT, default=1) choose 1 to plot all figures related to the de
trend process. Choose 0 to not plot it.
+ *band*: (Optional INT, default=1) band of the raster, in which is locates the elations of the pixels.
+ *detrended_limit*: (Optional FLOAT, default=10**-3) limit used to validate a detrended raster is flat enough 
to be considered detrended.
  
One folder named *detrend_rasters* will be automatic created at the save location of the 
folder containing the raster files.\
If `print_boolan = 1`, one folder named *detrender_plotter* will be automatic created at the save location fo the 
folder containing the raster files.\
The path to the folders should not have space on it.

  
#### Run the Example ```geodata_example```
To run the example contained inside the folder "geodata_example", one can just run `detrender.py`. The setup 
for running the example is defined in `config.py`. The output will be safed inside the folders "detrended_rasters"
and "plots". 
