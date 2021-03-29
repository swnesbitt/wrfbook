# Running WPS

## Steps in running a WRF real data simulation or forecast

The figure below describes the typical steps when running a basic WRF simulation.  In this section, we will describe the external data sources and WPS steps.

<img src="https://www2.mmm.ucar.edu/wrf/OnLineTutorial/images/flow.png">

## External Data Sources

There are two external data sources for each WRF run, the 

- static geographic data 
- gridded meteorological/land surface data.

The static geographic data is available for download from the WRF users page.  It needs to reside somewhere on your computer system and is used by `geogrid.exe` to create fields necessary for running WPS.

On `expanse`, the files are available in `/expanse/lustre/projects/uic406/snesbitt/WPS_GEOG` so there is no need to download these files.

