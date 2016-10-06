2 Spatial discretisation
2.1 Model domain
If the model is to be run on a regular grid, the programs stationMask and preDew may be
used for defining the model domain and the characteristics of the landscape elements used
as computational elements in the model. This requires that data defining watercourses,
catchments and land surface characteristics are available as ascii/grid import/export
format files used by most geographical information systems (GIS). These two programs
generate a set of files which are necessary for running program dew with the spatially
distributed DEW model.
If the model is to be run with irregularly shaped computational elements or several water
balance algorithms are to be used, the files defining the model domain and the
characteristics of landscape elements must be produced with a text editor.
2.2 Meteorological input data
If the model is run on a regular grid, meteorological input data may also be defined on a
regular grid. In this case the meteorological data are read from binary files, one file per
time step for precipitation and temperature, respectively. Regardless of the spatial
discretisation of the model domain, time series of meteorological input data from station
points may be used for driving the model. In this case, the meteorological data are read
from a text file.
2.3 Hierarchy of landscape and watercourse elements
The model can describe flow of water through the hierarchy of landscape elements,
subcatchments and the river/lake network in two ways. The implicit approach assumes
that runoff is sendt from all landscape elements within a subcatchment directly to the
outlet for every timestep. Water is then routed through a simplified river/lake network
where each subcatchment corresponds to one branch in the watercourse network.
The second approach describes flow of water between landscape elements and within the
river/lake network explicitly. This requires that flow directions within the hierarchy of
landscape elements, between landscape elements and watercourse elements, and within
the hierarchy of watercourse elements are supplied to the model.
The requirements for using the simple, or implicit, approach for flow of water through the
hierarchy of landscape elements are presented in Chapter 5. Chapter 6 describes
additional requirements for using an explicit hierarchy of landscape and watercourse
elements.
2.4 Hydrological model structure of computational elements
The Distributed Element Water balance model system (DEW model system) is not a
hydrological model per se, but a model interface that can be used to combine algorithms
from different hydrological model structures. Currently, the HBV (Bergström, 1995;
Lindström et al., 1997) and the KiWa (Beldring et al., 2000; Beldring, 2002) model
structures are implemented in the DEW model. The DEW model allows each landscape
element to use either one of the two water balance algorithms. The elements can be
combined according to the characteristics of the model domain. The automatic approach
for defining the model domain described in chapters 3 and 4 will only allow one type of
water balance algorithm to be used for all computational elements, which must also be
defined on a regular grid. When the model domain is not a regular grid or different water
balance algorithms are to be used for different computational elements, input files must
be produced by a text editor. The figure below presents one possible combination of
computational elements for hillslopes draining to a stream network.

3 Program stationMask
Program stationMask defines the model domain.
3.1 Input
Program stationMask requires two input files.
n Watercourse/subcatchment hierarchy description
n Landscape elements located within each watercourse/subcatchment
3.2 Watercourse/subcatchment hierarchy
description
The discretisation of watercourses/subcatchments use a unique number for identifying the
different elements. In the example below, these numbers are 80001, 80002 and 80003.
The watercourses/subcatchments 80001 and 80002 discharge into
watercourse/subcatchment 80003. The outlet from the model domain is located in
watercourse/subcatchment 80003. Several outlets are possible.
80001
80003
80002
15
The watercourse/subcatchment hierarchy is defined using a file named watercourse.dta in
this example:
# Number of watercourses: 3
0 : 80001 1.0
1 : 80002 1.0
2 : 80003 1.0
# Number of watercourse outlets: 1
2
# Hierarchy of watercourses
2 2 : 0 1
# Number of watercourses: <no. of watercourses/subcatchments>
<index> : <watercourse/subcatchment id.> <scale factor>
<index> : <watercourse/subcatchment id.> <scale factor>
...
# Number of watercourse outlets: <no. of outlets>
<index of outlet watercourse/subcatchment>
<index of outlet watercourse/subcatchment>
...
# Hierarchy of watercourses
<index of downstream> <no. of upstreams> : <indices of upstreams>
<index of downstream> <no. of upstreams> : <indices of upstreams>
...
#Number of watercourses
Number of watercourses/subcatchments.
The watercourses/subcatchments indices start from 0. For each
watercourse/subcatchment:
Watercourse/subcatchment index
Watercourse/subcatchment identification
Scale factor for modelled watercourse/subcatchment discharge
#Number of watercourse outlets
The watercourse/subcatchment index of each outlet must be provided.
#Hierarchy of watercourses/subcatchments
Watercourse/subcatchment index
Number of upstream watercourses/subcatchments
Upstream watercourses/subcatchments indices
3.3 Landscape elements within each watercourse/subcatchment
The landscape elements within the regular grid are identified by an ascii/grid
import/export format file. This file has grid cell identifiers that connect landscape
elements to watercourse/subcatchments identifiers as shown in the file named
watercourse_id.asc in the example below. Grid cells with nodata value (-9999) will not
be included in the model domain. The file watercourse_id.asc may include grid cells with
watercourse/subcatchments identifiers which are not included in the model domain.
16
ncols 5
nrows 8
xllcorner 0
yllcorner 0
cellsize 500
NODATA_value -9999
80001 80001 -9999 -9999 -9999
80001 80001 80001 -9999 -9999
80001 80001 80001 -9999 -9999
80001 80001 80001 80003 -9999
80002 80002 80003 80003 -9999
80002 80002 80003 80003 80003
80003 80003 80003 80003 80003
-9999 80003 -9999 -9999 -9999
3.4 Output
Program stationMask produces one output file in ascii/grid import/export format with
information about the model domain. The name of the output-file must be provided when
running the model. In the example below this file is called stations.asc. Program
stationMask writes the data in one column.
ncols 5
nrows 8 ...
xllcorner 0 80002
yllcorner 0 80002
cellsize 500 80003
NODATA_value -9999 80003
80001 -9999
80001 80002
-9999 80002
-9999 80003
-9999 80003
80001 80003
80001 80003
80001 80003
-9999 80003
-9999 80003
80001 80003
80001 -9999
80001 80003
-9999 -9999
-9999 -9999
80001 -9999
80001
80001
80003
-9999
...
17
3.5 Running stationMask
When stationMask is run on a Linux system or using a Windows console interface it is
possible to read the information necessary for running the program from a text file. In the
example below, the text file is called control_mask.txt. The model is started from the
command prompt with the command:
stationMask < control_mask.txt
If the executable file stationMask is not located in a directory in the search path of the
computer session, the full or relative path to stationMask must be provided.
File control_mask.txt contains the information to be supplied to the user interface of
program stationMask.
watershed.dta Watercourse/subcatchment hierarchy
watercourse_id.asc Landscape elements
stations.asc Output file with model domain
18
4 Program predew
Program predew determines the characteristics of each landscape element based on
ascii/grid import/export format files with information about land surface characteristics,
e.g. elevation, topographical characteristics, land use, lakes, glaciers. The program also
connects landscape elements to watercourse/subcatchment elements.
4.1 Input
Program predew requires 14 input-files.
n File with meteorological stations information
n Parameter file common for all land cover and soil/bedrock classes
n Output file from program stationMask with information about the model domain.
n Elevation of grid cells
n Slope length of grid cells
n Slope angle of grid cells
n Aspect of grid cells
n Percentage of grid cells areas covered by lakes
n Percentage of grid cells areas covered by forest
n Percentage of grid cells areas covered by bogs
n Percentage of grid cells areas covered by glaciers
n Potential tree line of grid cells
n Watercourse/subcatchment hierarchy description (used by program stationMask)
n Landscape elements located within each watercourse/subcatchment (used by
program stationMask)
4.2 Meteorological stations information
Program predew determines the meteorological stations to be used for modelling
precipitation and temperature in each grid cell. A file with information about the type and
location of each meteorological station must be supplied. This file is called
met_stations.dta in the example below.
Number of precipitation stations : 4
Number of temperature stations : 4
P 5608 0.0 0.0 205.0 SAETERNBEKKEN
P 5608 2500.0 0.0 205.0 SAETERNBEKKEN
P 5608 0.0 4000.0 205.0 SAETERNBEKKEN
P 5608 2500.0 4000.0 205.0 SAETERNBEKKEN
T 5608 0.0 0.0 205.0 SAETERNBEKKEN
T 5608 2500.0 0.0 205.0 SAETERNBEKKEN
T 5608 0.0 4000.0 205.0 SAETERNBEKKEN
T 5608 2500.0 4000.0 205.0 SAETERNBEKKEN
Number of precipitation stations : <no. of precipitation stations>
Number of temperature stations : <no. of temperature stations>
<station type> <station id.> <east coord.> <north coord.>
<elevation> <station name>
<station type> <station id.> <east coord.> <north coord.>
<elevation> <station name>
...
19
This file must be provided even in the case that meteorological data are to be read from
binary grid files. In this case, the information is only to be considered as dummy
information and the file met_stations.dta should contain the following data:
Number of precipitation stations : 1
Number of temperature stations : 1
P -9999 -9999 -9999 -9999
T -9999 -9999 -9999 -9999
4.3 Parameter file common for all land cover and
soil/bedrock classes
The file dew_common_parameters.dta in the example below provides parameter values
and other characteristics common to all land cover and soil/bedrock classes which are
necessary for running the model. Model parameters are defined in Chapter 7.
Number of seconds per time step SECONDS_TIMESTEP : 3600
Number of precipitation series NUM_PREC_SERIES : 1
Number of temperature series NUM_TEMP_SERIES : 1
Prec. grad. low per 100 m PREC_GRAD_LOW : 1.05
Prec. grad. high per 100 m PREC_GRAD_HIGH : 1.0
Altitude for 50 % reduction GRAD_CHANGE_ALT : 0
Prec. correction for rain PREC_CORR_RAIN : 1.0
Additional prec. corr. for snow PREC_CORR_SNOW : 1.0
Temp. lapse rate dry time steps LAPSE_DRY : -.21
Temp. lapse rate wet time stes LAPSE_WET : -.21
Lake temperature memory (days) DAY_TEMP_MEMORY : 30.0
Lake evaporation constant LAKE_EPOT_PAR : 1.0E-4
Rating curve constant KLAKE : .0025
Rating curve saddle point DELTA_LEVEL : 0.0
Rating curve exponent NLAKE : 1.0
Initial soil moisture INITIAL_SOIL_MOISTURE : 0.1
Initial upper zone INITIAL_UPPER_ZONE : 0.0
Initial lower zone INITIAL_LOWER_ZONE : .05
Initial saturated fraction one INTITIAL_SATURATED_ONE : 0.0
Initial saturated fraction two INTITIAL_SATURATED_TWO : 0.2
Initial lake temperature INITIAL_LAKE_TEMP : 0.0
Initial lake level INITIAL_LAKE_LEVEL : 0.0
Initial snow storage INITIAL_SNOW_STORAGE : 0.0
Initial total reservoir INITIAL_TOTAL_RESERVOIR : 0.0
Day no. for zero snow storage DAY_SNOW_ZERO : 0
n Number of seconds per time step is the temporal resolution of model simulations
n Number of precipitation series and number of temperature series defines the
number of for stations to be used for determination of meteorological input data
for each grid cell. Program predew determines weights for inverse distance
interpolation of meteorological data to each model grid cell using the nearest
precipitation and temperature stations.
n Gradients for precipitation increase per 100 m elevation change above and below
altitude GRAD_CHANGE_ALT must be supplied. A value of 1.0 means no change.
PREC_GRAD_HIGH will not be used if GRAD_CHANGE_ALT = 0.
n Precipitation correction for rain adjusts data for gauge losses. An additional
precipitation correction for snow may also be provided.
n Temperature lapse rates for dry and wet time steps (no rain or rain).
n Lake temperature memory and lake evaporation constant provide information
used in a simple method for modelling lake temperature and lake evaporation.
n Rating curve parameters are used for modelling lake outflow.
20
n Initial value for soil moisture content in HBV elements.
n Initial values for upper and lower saturated zone water content in HBV elements.
n Initial values for saturated zone water content in KiWa elements.
n Initial values for lake temperature and lake water level.
n Initial value for total volume of water stored in lakes.
n Snow storage may be reduced to zero at a specified day number each year. If
DAY_SNOW_ZERO = 0 the snow storage is not altered.
4.4 Land surface characteristics of grid cells
Program predew reads input files with land surface characteristics of the grid cells within
the model domain. The information is supplied as ascii/grid import/export format files
with information about elevation (metres above sea level), slope length (metres), slope
angle (degrees), aspect (degrees), percentage of grid cells areas covered by lakes,
percentage of grid cells areas covered by forest, percentage of grid cells areas covered by
bogs, percentage of grid cells areas covered by glaciers and finally, potential tree line of
grid cells (metres above sea level). The potential tree line defines the upper margin of the
subalpine forest where trees become dwarfed or are absent. All these files have the same
format. An example is provided for grid cell elevations in file altitude.asc.
ncols 5
nrows 8
xllcorner 0
yllcorner 0
cellsize 500
NODATA_value -9999
410 400 -9999 -9999 -9999
400 360 380 -9999 -9999
300 280 300 -9999 -9999
240 220 200 230 -9999
280 220 200 210 -9999
270 240 200 170 160
280 250 210 160 120
-9999 240 -9999 -9999 -9999
4.5 Output
Program predew produces three output files.
n A file with control information used during model development. The name of this
file must be supplied when running the model. In the example below this file is
called pre_out.txt.
n File with information about grid cell characteristics
n File with information about landscape elements within each
watercourse/subcatchment
4.6 File with information about grid cell
characteristics
The file with information about characteristics for each grid cell produced by program
predew describes the coordinates of each grid cell and the model structure/algorithm used
for modelling hydrological processes. Two model structures are possible: HBV model
21
and Kinematic Wave (KiWa) model structure. These two model structures differ in the
algorithms used for describing subsurface processes.
Furthermore, elevation, slope properties, lake and glacier percentage, land cover type, soil
type and other land surface characteristics are described. Information about the
meteorological stations and the weights to be used for interpolation of meteorological
data are also provided. The file dew_landscape.dta shown below presents the first lines of
an output file with information about grid cell characteristics.
ncols 5
nrows 8
xllcorner 0
yllcorner 0
cellsize 500
NODATA_value -9999
# Number of landscape elements : 27
0 0 1 250000. 410. 300.0 3.4 180. 0.0 0.0 2 2 100.0 0 0 0.0
2 1.0 2 1.0
The first six lines provide information necessary for describing the coordinates of a
regular grid.
Line 7 gives information about the number of landscape elements.
Starting from line 8, there is one line for each landscape element (grid cell) in the model
domain with the following information.
Element index
Coordinate index
Model structure (0 = HBV, 1 = KiWa)
Area (m2)
Elevation (m)
Slope length (m)
Slope angle (degrees)
Aspect (degrees)
Lake percentage (%)
Glacier percentage (%)
Land surface class 1; land cover class
Land surface class 1; soil type
Percentage of area covered by land surface class 1 (%)
Land surface class 2; land cover class
Land surface class 2; soil type
Percentage of area covered by land surface class 2 (%)
For all precipitation series:
Precipitation station number
Precipitation station weight
For all temperature series:
Temperature station number
Temperature station weight
22
4.7 File with information about landscape elements
within each watercourse/subcatchment
The landscape elements discharging to each watercourse or located within each
subcatchment are listed in file dew_waterland.dta presented below.
# 80001 # 11
0 0
1 1
2 5
3 6
4 7
5 10
6 11
7 12
8 15
9 16
10 17
# 80002 # 4
12 20
13 21
16 25
17 26
# 80003 # 12
11 18
14 22
15 23
18 27
19 28
20 29
21 30
22 31
23 32
24 33
25 34
26 36
The information provided for each watercourse/subcatchment is:
# <watercourse/subcathment index> # <no. of landscape elements>
<element index of element 1> <coordinate index of element 1>
<element index of element 2> <coordinate index of element 2>
...
Element indices start from 0, coordinate indices are starting from 0 in the upper left
corner of the regular grid. The example below shows element indices and coordinate
indices for the regular grid used for landscape elements in catchment Sæternbekken.
23
4.8 Running predew
When predew is run on a Linux system or using a Windows console interface it is
possible to read the information necessary for running the program from a text file. In the
example below, the text file is called control_pre.txt. The model is started from the
command prompt with the command:
predew < control_pre.txt
If the executable file predew is not located in a directory in the search path of the
computer session, the full or relative path to predew must be provided.
File control_pre.txt contains the information to be supplied to the user interface of
program predew.
0 / 1 Model structure: 0 = HBV, 1 = KiWa
C Watercourse hierarchy: Subcatchments (C)
pre_out.txt Program development output file
met_stations.dta Meteorological stations
dew_common_parameters.dta Parameters for all classes
stations.asc Model domain file
altitude.asc Elevation
length.asc Slope length
slope.asc Slope angle
aspect.asc Aspect
lake_per.asc Lake percentage
forest_per.asc Forest percentage
bog_per.asc Bog percentage
glacier_per.asc Glacier percentage
tree_level.asc Potential tree line
watershed.dta Watercourse/subcatchment hierarchy
watercourse_id.asc Landscape elements
24
5 Program dew
The hydrological modelling is performed by program dew. Program dew can be run in
calibration mode or simulation mode. When the program is being run in calibration mode,
a file with observed streamflow data for the calibration period is required. Program dew
will not perform optimization of model parameters; the only difference between the two
modes is that the sum all model simulated streamflow values is written to the end of the
model results files dew_<watercourse/subcatchment>.var (Chapter 6) in calibration
mode. Only model results for time steps where observed data are available will be
included in this sum.
5.1 Input
Input files to program dew may be generated by programs stationMask and predew or be
produced using a text editor. When the model domain is not a regular grid or several
water balance algorithms are to be used for different computational elements, input files
must be produced by a text editor. In addition to the files that may be generated by
programs stationMask and predew or be produced using a text editor, program dew
requires files with information about parameter values of land cover/vegetation classes
and soil/bedrock classes, watercourse elements and meteorological input data. The
structure of the input files is described below, or in Chaper 3. Model parameters are
defined in Chapter 7.
Program dew requires 12 or 13 input-files.
n File with meteorological stations information
n Parameter file common for all land cover and soil/bedrock classes
n Land cover classes parameters
n Soil/bedrock classes parameters HBV model
n Soil/bedrock classes parameters KiWa model
n Landscape elements selected for time series output
n Landscape elements characteristics (may be generated by program predew)
n Watercourse/subcatchment hierarchy description (used by programs stationMask
and predew)
n Landscape elements located within each watercourse/subcatchment (used by
programs stationMask and predew)
n Flow directions of landscape elements (in case of explicit hierarchy of landscape
elements).
n Correction of meteorological data
n Meteorological time series (only for input data in time series format)
n File with observed streamflow data named obs_streamflow.var. This file is
required in calibration mode, but not in simulation mode. If it does not exist,
model results will not be compared to observations. An example is provided in
file obs_streamflow.var below.
#dew_00080003.var
19960920/0000 0.011248
19960920/0100 0.011248
19960920/0200 0.011248
25
19960920/0300 0.011248
19960920/0400 0.011248
19960920/0500 0.011248
19960920/0600 0.011248
...
The first line gives the name of the model output file which observed data are to be
compared to. The next lines contain observed streamflow data. This information can be
repeated for each model output file which is to be compared to observed data.
5.2 When the model domain is not a regular grid
If the model domain is not a regular grid, the information in the first six lines of the file
dew_landscape.dta is to be considered as dummy information. It must be provided, but it
will not be used by the program dew.
The landscape elements discharging to each watercourse or located within each
subcatchment are read from file dew_waterland.dta. In the case that the model domain is
a regular grid, this file may be generated by program predew. If irregularly shaped
landscape elements are used, this file must be produced using a text editor. The
coordinate indices may then no longer be used for finding the location of landscape
elements, and should be assigned the value of the element indices. The landscape
elements discharging to each watercourse or located within each subcatchment should
then be listed as in the example below. The watercourse/subcatchment element 80003
receives water from 10 landscape elements with indices from 0 to 9.
# 80003 # 10
0 0
1 1
2 2
3 3
4 4
5 5
6 6
7 7
8 8
9 9
5.3 Land cover classes parameters
The following land cover classes are used by program dew.
Open land: Non-forested areas below the tree line (agricultural fields, meadows
etc.)
Bog: Bogs and wetland areas
Forest: Lowland areas with coniferous or deciduous forests
Alpine: Areas below the tree line with subalpine forests
Heather: Areas above the tree line with grass, heather, shrubs or dwarfed trees
Bedrock: Areas above the tree line with extremely sparse vegetation
Glacier: Glaciated areas covered by snow and ice
26
The tree line is the upper margin of the subalpine forest where trees become dwarfed or
are absent.
Land cover classes parameters are read from file dew_landsurface_parameters.dta below.
The file has one line per land cover class, but has been divided into three parts in this
example.
Type no. INTER_MAX EPOT_PAR WET_PER_CORR
OPEN 0 1.0E-4 9.2E-5 0.8
BOG 1 1.0E-4 0.000020 0.8
FOREST 2 1.0E-4 0.000020 0.8
ALPINE 3 0.0 0.0 0.0
HEATHER 4 1.0E-4 9.2E-5 0.8
BEDROCK 5 1.0E-4 9.2E-5 0.8
GLACIER 6 1.0E-4 9.2E-5 0.8
ACC_TEMP MELT_TEMP SNOW_MELT_RATE ICE_MELT_RATE
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
0.0 -0.03 0.01 1.12
FREEZE_EFF MAX_REL ALBEDO CV_SNOW
0.01 0.08 0.90 0.0
0.01 0.08 0.90 0.0
0.01 0.08 0.90 0.0
0.01 0.08 0.90 0.3
0.01 0.08 0.90 0.5
0.01 0.08 0.90 0.75
0.01 0.08 0.90 1.0
5.4 Soil/bedrock classes parameters
There is one soil/bedrock class corresponding to each land cover class. There is one set of
soil/bedrock classes parameters for the HBV model structure, and one set of soil/bedrock
classes parameters for the KiWa model structure.
Soil/bedrock classes parameters for the HBV model structure are read from file
hbv_soil_parameters.dta below. The file has one line per soil/bedrock class, but has been
divided into two parts in this example.
27
Type no. FC FCDEL BETA INFMAX
OPEN 0 0.38 1.0 2.0 50.0
BOG 1 0.38 1.0 2.0 50.0
FOREST 2 0.38 1.0 2.0 50.0
ALPINE 3 0.38 1.0 2.0 50.0
HEATHER 4 0.38 1.0 2.0 50.0
BEDROCK 5 0.38 2.0 1.0 50.0
GLACIER 6 0.38 2.0 1.0 50.0
KUZ ALFA PERC KLZ DRAW
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
1.0 1.87 1.0E-4 0.013 0.0
Soil/bedrock classes parameters for the KiWa model structure are read from file
kiwa_soil_parameters.dta below. The file has one line per soil/bedrock class, but has
been divided into three parts in this example.
Type no. SOIL_DEPTH OV_PAR_1 OV_PAR_2
OPEN 0 0.8 45.2 1.24
BOG 1 0.8 45.2 1.24
FOREST 2 0.8 45.2 1.24
ALPINE 3 0.8 45.2 1.24
HEATHER 4 0.8 45.2 1.24
BEDROCK 5 0.8 45.2 1.24
GLACIER 6 0.8 45.2 1.24
TSAT_0 EFF_POR KSAT_0 A DELTA
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
0.7 0.074 16.6 -8.20 -15.51
LAMBDA_KW ROOT_DEPTH WILT_POINT EACT_PAR
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
-1.415 0.40 0.06 0.9
28
5.5 Landscape elements selected for time series
output
State variables and fluxes for selected landscape/model elements may be written to files.
File hbv_elements.dta below shows an example for HBV model elements.
# Number of landscape elements selected for HBV time series
output: 3
*no.* *groundwater reference level* *groundwater eff. por.*
0 -1.5 0.1
12 -1.5 0.15
25 -2.0 0.1
The numbers of the selected landscape elements must be specified. File
kiwa_elements.dta below shows an example for KiWa model elements.
# Number of landscape elements selected for KiWa time series
output: 3
*no.* * hillslope length fraction one* * hillslope length
fraction two*
0 0.5 1
12 0.5 1
25 0.0 1
5.6 Correction of meteorological data
Meteorological time series are corrected for gauge losses and elevation gradients using
information in the file with parameter values and other characteristics common to all land
cover and soil/bedrock classes. However, these corrections will not be applied when
gridded meteorological input data are used. Instead, it is possible to apply corrections to
all grid cells within a subcatchment. File catchment_correction.dta below shows an
example.
Catchment id. Precipitation correction Temperature correction
80001 1.0 0.0
80002 1.0 0.0
80003 1.0 0.0
These corrections apply to all landscape elements discharging to a watercourse or
contained within a subcatchment. All precipitation values read from the grid file are
multiplied by the precipitation correction, whereas the temperature correction is added to
all temperature values read from the grid file. These corrections can also be applied in the
case that meteorological time series are used. In this case, the precipitation and
temperature corrections will be applied to the input data for all computation element of
the model located within a subcatchment.
29
5.7 Meteorological time series input data
Meteorological time series data are supplied in a file with one column per time series.
File input_data.dta. below is an example.
*Time* *Precip* *Precip* *Precip* *Precip* *Temp* *Temp* *Temp* *Temp*
Saeternbekken
19960820/0000 0 0 0 0 13.8 13.8 13.8 13.8
19960820/0100 0 0 0 0 13.45 13.45 13.45 13.45
19960820/0200 0 0 0 0 13.2 13.2 13.2 13.2
19960820/0300 0 0 0 0 13.65 13.65 13.65 13.65
19960820/0400 0 0 0 0 13.45 13.45 13.45 13.45
19960820/0500 0 0 0 0 13.55 13.55 13.55 13.55
19960820/0600 0.25 0.25 0.25 0.25 13.65 13.65 13.65 13.65
19960820/0700 0 0 0 0 14.1 14.1 14.1 14.1
19960820/0800 0 0 0 0 14.7 14.7 14.7 14.7
19960820/0900 0.1 0.1 0.1 0.1 17.4 17.4 17.4 17.4
19960820/1000 0 0 0 0 20.55 20.55 20.55 20.55
19960820/1100 0 0 0 0 22.6 22.6 22.6 22.6
19960820/1200 0 0 0 0 23.85 23.85 23.85 23.85
19960820/1300 0 0 0 0 24.2 24.2 24.2 24.2
19960820/1400 0 0 0 0 24.15 24.15 24.15 24.15
19960820/1500 0 0 0 0 23.65 23.65 23.65 23.65
19960820/1600 0 0 0 0 23.15 23.15 23.15 23.15
19960820/1700 0 0 0 0 21.05 21.05 21.05 21.05
19960820/1800 0 0 0 0 19.5 19.5 19.5 19.5
19960820/1900 0 0 0 0 17.7 17.7 17.7 17.7
19960820/2000 0 0 0 0 16.4 16.4 16.4 16.4
19960820/2100 0 0 0 0 15.8 15.8 15.8 15.8
19960820/2200 0 0 0 0 15.3 15.3 15.3 15.3
19960820/2300 0 0 0 0 14.55 14.55 14.55 14.55
Each precipitation and temperature station in file met_stations.dta corresponds to a
column in file input_data.dta. Precipitation date have unit mm/time step and temperature
data have unit ° C. The two first lines on the file are used for comments. Meteorological
time series are corrected for gauge losses and elevation gradients using information in the
file with parameter values and other characteristics common to all land cover and
soil/bedrock classes.
30
5.8 Running dew
When dew is run on a Linux system or using a Windows console interface it is possible to
read the information necessary for running the program from a text file. In the example
below, the text file is called control_dew.txt. The model is started from the command
prompt with the command:
dew < control_dew.txt
If the executable file dew is not located in a directory in the search path of the computer
session, the full or relative path to dew must be provided.
File control_dew.txt contains the information to be supplied to the user interface of
program dew.
S Type of model run: simulation(S) or calibration(C)
C Watercourse hierarchy: nested catchments (C)
T / G Meteorological input data format: Time series or Grid
dew_out.txt Program development output file
20 8 1996 0 0 Start model spin-up dd mm yyyy hh mm
20 9 1996 0 0 Start simulation dd mm yyyy hh mm
17 11 1996 23 0 End simulation dd mm yyyy hh mm
met_stations.dta Meteorological stations
dew_common_parameters.dta Parameters for all classes
dew_landsurface_parameters.dta Land cover classes parameters
hbv_soil_parameters.dta Soil/bedrock classes parameters HBV
kiwa_soil_parameters.dta Soil/bedrock classes parameters KiWa
hbv_elements.dta Landscape elements with time series output HBV
kiwa_elements.dta Landscape elements with time series output KiWa
dew_landscape.dta Landscape elements characteristics
watershed.dta Watercourse/subcatchment hierarchy
dew_waterland.dta Landscape elements within each
watercourse/subcatchment
catchment_correction.dta Correction of meteorological data
input_data.dta Meteorological input data (only for
input data in time series format)
The watercourse hierarchy in the examples above assumes that an explicit hierarchy of
landscape elements is not used.
31
6 Explicit hierarchy of landscape
elements
If an explicit hierarchy of landscape elements is to be used, the landscape elements must
also be assigned flow directions. These flow directions must be supplied by a file which
is read by program predew. In the example below the file watercourse_id.asc contains
information about landscape elements located along the river network. In this case, all
landscape elements located along the river network receives water from upstream
elements and discharges into the stream network.
6.1 Watercourse description within a regular grid
The watercourse hierarchy of the river/lake network is defined using the file
watercourse.dta as in the previous example:
# Number of watercourses: 3
0 : 80001 1.0
1 : 80002 1.0
2 : 80003 1.0
# Number of watercourse outlets: 1
2
# Hierarchy of watercourses
2 2 : 0 1
6.2 Landscape elements within a regular grid
The landscape elements located along the river/lake network hierarchy are described
using the file watercourse_id.asc in the example below. Note that this file may not be
used for defining the model domain with program station_mask. This must be done using
another file where grid cells with landscape elements that are to be included in the model
domain have values not equal to nodata (-9999).
ncols 5
nrows 8
xllcorner 0
yllcorner 0
cellsize 500
NODATA_value -9999
-9999 -9999 -9999 -9999 -9999
-9999 -9999 -9999 -9999 -9999
-9999 -9999 -9999 -9999 -9999
-9999 -9999 80001 -9999 -9999
-9999 80002 80003 -9999 -9999
-9999 -9999 -9999 80003 -9999
-9999 -9999 -9999 -9999 80003
-9999 -9999 -9999 -9999 -9999
32
The file dew_waterland.dta with information about landscape elements discharging to
each element of the river/lake network is presented in the example below.
# 80001 # 1
10 17
# 80002 # 1
13 21
# 80003 # 3
14 22
19 28
25 34
6.3 Flow directions of landscape elements
Program predew requires an additional input file with flow directions of landscape
elements/grid cells within the model domain. In case of a regular grid, water from each
grid cell may discharge to one of eight downstream grid cells. The information is supplied
by an ascii/grid import/export format file with information about the flow direction for
each grid cell. An example is provided in file land_flow.asc below.
ncols 5
nrows 8
xllcorner 0
yllcorner 0
cellsize 500
NODATA_value -9999
2 4 -9999 -9999 -9999
2 4 4 -9999 -9999
2 2 4 -9999 -9999
1 1 4 4 -9999
1 1 2 2 -9999
1 64 1 2 4
1 4 1 1 4
-9999 128 -9999 -9999 -9999
The flow directions of the hierarchy of landscape elements and river/lake network in this
example is presented in the figure below:
33
Program predew writes the flow directions of all landscape elements within the model
domain to file dew_landupflow.dta presented in the example below. Each landscape
element may receive water from upstream landscape elements. Finally, water is
discharged into the river/lake network from the landscape elements described in file
watercourse_id.asc in the example above.
3 2 : 0 1
6 2 : 2 3
7 1 : 4
9 2 : 8 5
10 3 : 9 6 7
13 2 : 17 12
15 1 : 11
17 1 : 16
19 1 : 18
20 1 : 15
22 1 : 21
23 1 : 26
24 1 : 23
25 2 : 24 20
26 1 : 22
The information provided for each landscape element is:
<element index> <no. of upstream elements> : <element indices
of upstream landscape elements> ...
<element index> <no. of upstream elements> : <element indices
of upstream landscape elements> ...
<element index> <no. of upstream elements> : <element indices
of upstream landscape elements> ...
...
34
6.4 Running predew in case of an explicit hierarchy
of landscape elements
In the example below, the information necessary for running program predew is read
from the file control_pre.txt. An additional input file is required as compared to the case
with an implicit hierarchy of landscape and watercourse elements. The model is started
from the command prompt with the command:
predew < control_pre.txt
0 / 1 Model structure: 0 = HBV, 1 = KiWa
N Watercourse hierarchy: river/lake network (N)
pre_out.txt Program development output file
met_stations.dta Meteorological stations
dew_common_parameters.dta Parameters for all classes
stations.asc Model domain file
altitude.asc Elevation
length.asc Slope length
slope.asc Slope angle
aspect.asc Aspect
lake_per.asc Lake percentage
forest_per.asc Forest percentage
bog_per.asc Bog percentage
glacier_per.asc Glacier percentage
tree_level.asc Potential tree line
watershed.dta Watercourse/subcatchment hierarchy
land_flow.asc Flow direction of landscape elements
watercourse_id.asc Landscape elements
6.5 Running dew in case of an explicit hierarchy of
landscape elements
In the example below, the information necessary for running program dew is read from
the file control_dew.txt. An additional input file is required as compared to the case with
an implicit hierarchy of landscape and watercourse elements. The model is started from
the command prompt with the command:
dew < control_dew.txt
S Type of model run: simulation(S) or calibration(C)
N Watercourse hierarchy: river/lake network (N)
T / G Meteorological input data format: Time series or Grid
dew_out.txt Program development output file
20 8 1996 0 0 Start model spin-up dd mm yyyy hh mm
20 9 1996 0 0 Start simulation dd mm yyyy hh mm
17 11 1996 23 0 End simulation dd mm yyyy hh mm
met_stations.dta Meteorological stations
dew_common_parameters.dta Parameters for all classes
dew_landsurface_parameters.dta Land cover classes parameters
hbv_soil_parameters.dta Soil/bedrock classes parameters HBV
kiwa_soil_parameters.dta Soil/bedrock classes parameters KiWa
hbv_elements.dta Landscape elements with time series output HBV
kiwa_elements.dta Landscape elements with time series output KiWa
dew_landscape.dta Landscape elements characteristics
watershed.dta Watercourse/subcatchment hierarchy
dew_waterland.dta Landscape elements within each
watercourse/subcatchment
dew_landupflow.dta Upstream landscape elements
catchment_correction.dta Correction of meteorological data
input_data.dta Meteorological input data (only for
input data in time series format)
35
7 Model results
Program dew calculated input data, state variables and fluxes for each
watercourse/subcatchment. The discharge from each outlet in the model domain is
determined after routing water through the hierarchies of watercourses and
subcatchments.
7.1 Model results files
Program dew produces the following output files.
n A file with control information used during model development. The name of this
file must be supplied when running the model. In the example above this file is
called dew_out.txt.
For each watercourse/subcatchment.
pre_<watercourse/subcatchment>.var precipitation (mm/time step)
tem_<watercourse/subcatchment>.var temperature (° C)
swe_<watercourse/subcatchment>.var snow water equivalent (mm)
eva_<watercourse/subcatchment>.var evaporation (mm/time step)
hpe_<watercourse/subcatchment>.var percolation from soil moisture zone to
upper zone (mm/time step)
hsm_<watercourse/subcatchment>.var HBV soil moisture deficit (mm)
huz_<watercourse/subcatchment>.var HBV upper zone (mm)
hlz_<watercourse/subcatchment>.var HBV lower zone (mm)
hgw_<watercourse/subcatchment>.var HBV upp. and low. zone (mm)
run_<watercourse/subcatchment>.var runoff (mm/time step)
dew_<watercourse/subcatchment>.var discharge (m3/s)
For each model element selected for time series output.
n HBV_groundwater_<element>.var groundwater table depth (m)
For all elements within the model domain.
n res_totalvol.var total volume of water stored in lakes (m3)
36
8 Model parameters
The parameters of model hbv are used for modifying input data and calculating state
variables and fluxes for all computational elements within the model domain, both
landscape elements and watercourse elements.
8.1 Parameters common for all land cover and
soil/bedrock classes
n PREC_GRAD_LOW, PREC_GRAD_HIGH: Gradients for precipitation increase per
100 m elevation change below and above elevation GRAD_CHANGE_ALT. A value
of 1.0 means no change. A value of 1.1 means 10 % increase per 100 m elevation
change. PREC_GRAD_HIGH will not be used if GRAD_CHANGE_ALT = 0.
n PREC_CORR_RAIN: Precipitation correction for rain.
n PREC_CORR_SNOW: Additional precipitation correction for snow.
n LAPSE_DRY, LAPSE_WET: Temperature lapse rates for dry and wet time steps
(no rain or rain).
n DAY_TEMP_MEMORY: Temperature memory for lakes used in a simple method for
modelling lake temperature.
n LAKE_EPOT_PAR: Controls lake evaporation rate.
n KLAKE: The constant of the rating curve of lakes.
n DELTA_LEVEL: The zero point of the rating curve of the lakes.
n NLAKE: Exponent of the rating curve of the lakes.
n INITIAL_SOIL_MOISTURE: Initial water content in soil moisture zone in HBV
model elements (m).
n INITIAL_UPPER_ZONE: Initial water content in upper zone in HBV model
elements (m).
INITIAL_LOWER_ZONE: Initial water content in lower zone in HBV model
elements (m).
INITIAL_SATURATED_ONE: Initial fraction of soil profile covered by saturated
zone in upper end of hillslope in KiWa model elements (m).
INITIAL_SATURATED_TWO: Initial fraction of soil profile covered by saturated
zone in lower end of hillslope in KiWa model elements (m).
INITIAL_LAKE_TEMP: Initial temperature of lake elements (°C).
INITIAL_LAKE_LEVEL: Initial water level of lake elements (m).
INITIAL_SNOW_STORAGE: Initial snow storage (m).
INITIAL_TOTAL_RESERVOIR: Initial volume of water stored in lakes (m3).
8.2 Land cover parameters
The parameters for land cover are unique for each class.
INTER_MAX: Maximum interception storage (m).
EPOT_PAR: Controls potential evaporation rate (m/(TimeStep·°C)).
WET_PER_CORR: Controls reduction of ground evapotranspiration when
intercepted water is stored on vegetation..
ACC_TEMP: Threshold temperature for snow accumulation (°C).
MELT_TEMP: Threshold temperature for snow melt (°C).
n SNOW_MELT_RATE: Controls snow melt rate (m/(TimeStep·°C)).
n ICE_MELT_RATE: Controls ice melt rate for glaciers by multiplication with
SNOW_MELT_RATE.
n FREEZE_EFF: Controls refreeze rate of liquid meltwater in snow by
multiplication with SNOW_MELT_RATE.
n MAX_REL: Meltwater is retained in the snow until the amount of liquid water
reaches the relative fraction of snowpack water equivalent given by MAX_REL.
n ALBEDO: Snow surface albedo.
n CV_SNOW: Coefficient of variation for lognormal distribution of snowfall.
8.3 HBV soil/bedrock parameters
The parameters for soil/bedrock are unique for each class.
n FC: Field capacity (m).
n BETA: Controls distribution function of soil moisture.
n FCDEL: Fraction of field capacity where reduction of evapotranspiration below
potential level starts.
n INFMAX: Maximum infiltration rate (m/TimeStep).
n KUZ: Upper zone response coefficient.
n ALFA: Controls increase of upper zone response with increasing water content.
n PERC: Percolation from upper to lower zone (m/TimeStep).
n KLZ: Lower zone response coefficient.
n DRAW: Rate of draw up from lower zone to soil moisture zone (m/TimeStep).
8.4 KiWa soil/bedrock parameters
The parameters for soil/bedrock are unique for each class.
n SOIL_DEPTH: Depth of soil profile (m).
n OV_PAR_1: Overland flow kinematic wave friction parameter (m/TimeStep).
n OV_PAR_2: Overland flow kinematic wave exponent.
n TSAT_0: Saturation volumetric water content at soil surface.
n EFF_POR: Storage coefficient of saturated zone.
n KSAT_0: Saturated hydraulic conductivity at soil surface (m/TimeStep).
n A: Determines the rate of decrease of saturated hydraulic conductivity with depth
below the soil surface.
n DELTA: Controls partitioning of ground evapotranspiration between saturated
and unsaturated zone.
n LAMBDA_KW: Controls relationship between equilibrium soil moisture content at
the soil surface and depth to groundwater table.
n ROOT_DEPTH: Depth of root zone (m).
n WILT_POINT: Volumetric water content at the wilting point of vegetation.
n EACT_PAR: Controls actual evapotranspiration from the ground.
38
9 DEW model algorithms
The algorithms of program hbv are based on the Nordic HBV model (Sælthun, 1996),
with some exceptions. One important difference is that the response function of the upper
groundwater reservoir is based on the principle described by Lindström et al. (1997)
where no threshold is applied. Runoff from the upper groundwater zone is given by:
ALFA
QU KUZ UZ ; ALFA > 1.0
9.1 Vegetation
The vegetation cover is described as a lumped reservoir. Intercepted water stored on
vegetation evaporates at the potential rate. As long as intercepted water is present, the
fraction of the time step when actual evapotranspiration from the ground takes place is
reduced according to:
DryPeriod TimeStep −WetPeriod WET _ PER _CORR
n DryPeriod is the fraction of TimeStep when evapotranspiration from the ground
occurs.
n TimeStep is the time resolution of the model run.
n WetPeriod is the fraction of TimeStep when evaporation of intercepted water
occurs at the potential rate.
n 0 _ WET_PER_CORR _ 1

1 Run Model 
Program <./…/program_contrl.txt
Such as stationMask <contrl_mask.txt
See example: /hdata/fou/DistModel/Dew/Saetern_grid_N
Note: nodata is -9999.
1.1 Step 1: 
Run /hdata/fou/DistModel/Dew/Bin/stationMask	
Control file: control_mask.txt

/hdata/fou/personlig/holi/Dew/Data/LosnaWatershed	//input, id of the watercourse (rivers)
/hdata/fou/DistModel/Dew/GEO_DATA/ig_stafelt1.asc	//input, id of the sub-catchment
stations.asc	//output, different format of the watershed_area.asc
 
1.2 Step 2: 
Run /hdata/fou/DistModel/Dew/Bin/riverFlow
Control file: control_flow.txt

flow_out.txt   //output, the hiarechy of rivers and landscape elements with river cell
stations.asc	//input, the output file of the step 1: stationMask
/hdata/fou/personlig/holi/LosnaOutlet	// input, the definination of the outlet (in format of row, column in the squared study area)
/hdata/fou/personlig/holi/Dew/Data/LosnaWaterID.asc	//input, the river system in grid, actually same as the ‘watershed_area.asc’ 
/hdata/fou/personlig/holi/Dew/Data/LosnaLakeNo.asc	//input, the defined lake No in study area.
/hdata/fou/personlig/holi/Dew/Data/LosnaFlowD.asc	// input, flow direction of the cell in study area
/hdata/fou/personlig/holi/Dew/Data/Losna/WatercourseHierachy
/hdata/fou/personlig/holi/Dew/Data/Losna/WatercourseLandScape

1.3 Step 3: 
Run /hdata/fou/DistModel/Dew/Bin/predew
Control file: control_pre.txt

1	//input, model structure, 0 is HBV, 1 is KiWa
N	//input, watercourse hierarchy, N is network, C is subcatchments
pre_out.txt	//output, for model and run information
/hdata/fou/personlig/holi/Dew/Data/LosnaMetStations	//input, information about precipitation and temperature stations.
dew_common_parameters.dta	//input, parameters for all classes.
stations.asc	// input, the output file of the step 1: stationMask

altitude.asc	//input, elevation of the cells, landscape and river cells
length.asc	//input, slope length

/hdata/fou/personlig/Dew/Data/LosnaSlope.asc	//input, slope angle, in format of angle (maximum 360)

aspect.asc	//input, aspect, in format of angle (maximum 180)
lake_per.asc	//input, lake percentage, in format of %.
forest_per.asc	//input, forest percentage, in format of %.
bog_per.asc	//i nput, bog percentage, in format of %.
glacier_per.asc	//i nput, glacier percentage, in format of %.
tree_level.asc	//i nput, tree line elevation, different climates

dew_watercourse_hierarchy.dta	//input, id of the rivers, outlet and watercourse hierarchy.
/hdata/fou/personlig/Dew/Data/LosnaFlowD.asc	//input, flow direction of cells.

dew_watercourse_landscape.dta	//from   riverFlow

1.4 Step 4: 
Run /hdata/fou/DistModel/Dew/Bin/dew
1.4.1 Control file: control_dew.txt
For time series input
 S	//input, type of the model run, S is simulation, C is calibration.
N	//input, watercourse hierarchy, N is network, C is sub-catchments.
T	//input, meteorological input, T is time series, G is grid.
S	//input, routing method, S: sourceTOsink, M: Muskingum-cunge; 0: no routing:
20 8 1996 0 0 	//input, start spin-up  model time, dd mm yyyy  hh mm
20 9 1996 0 0	//input, start model time, dd mm yyyy  hh mm
17 11 1996 23 0	//input, end model time, dd mm yyyy  hh mm
/hdata/fou/personlig/holi/Dew/Data/LosnaMetStations	//input, information about precipitation and temperature stations
 dew_common_parameters.par	//input, parameters for all classes.
dew_landsurface_parameters.par	//input, land cover parameters.
hbv_soil_parameters.par	//input, soil parameters of HBV model.
kiwa_soil_parameters.par	//input, soil parameters of KiWa model.
hbv_elements.dta	//input, landscape elements with time series ouput by HBV.
kiwa_elements.dta	//input, landscape elements with time series ouput by KiWa.
/hdata/fou/personlig/holi/Dew/Data/LosnaSubCatchment	//input sub-catchments with time series ouput by HBV. 
dew_landscape.dta	//input, landscape cell characteristics (output of step 3: predew).
dew_watercourse_hierarchy.dta	//input, id of the rivers, outlet and watercourse hierarchy.
dew_waterland.dta	//input, landscape cell numbers of sub-catchments  (output of step 3: predew).
/hdata/fou/personlig/holi/Dew/Data/LosnaCatchmentCorrection	//input, correction of meteorological stations data.
/hdata/fou/personlig/holi/Dew/Data/NRFvelocity.par
/hdata/fou/personlig/holi/Dew/Data/ManinngRoughness.par
/hdata/fou/personlig/holi/Dew/Data/RiverElementData
input_data.dta	//output, input meteorological data in format of time series. For check purpose.

For grid data input
dew_out.txt	//output, for model information.
Input_out.txt	//output, the input file of every cell.
Dew_008001.var //output, the discharge of sub-catchment outlet. 008001 is the id of subcatments. Such file for every sub-catchment.
eva_008001.var //output, the evaporation of sub-catchment. 008001 is the id of sub-catchments. Such file for every sub-catchment.
HBV_groundwater_0.var //output by HBV model, the groundwater of the cell in time series. 0 is the index of cell in study area. Such file for every defined cell.
Hgw_0080001.var // output, HBV groundwater volume of sub-catchment 0080001.
Hlz_0080001.var // output, HBV lower zone groundwater level of sub-catchment 0080001.
huz_0080001.var // output, HBV up zone groundwater level of sub-catchment 0080001.
Hpe_0080001.var // output, HBV infiltration from satutred zone to groundwater.
hsm_0080001.var // output. HBV soil moisture deficit. 
Kiwa_groundwater_0_one.var	//output by Kiwa model, the groundwater of the cell in time series. 0 is the index of cell in study area. One is the layer. Such file for every defined cell.
Pre_0080001.var	// output, calibrated precipitation (the precipitation used in model input).
Res_totalvol.var	//output, water storage of lakes.
Run_00080001.var	//output, runoff of sub catchment 00080001.
Swe_00080001.var	//output, snow water equivalent..
tem_00080001.var	//output, temperature.
test_waterland.dat	//output, the landscape cells of every sub-catchments, for test purpose.
2 Files Variables
Some parameters are defined in classDew.h, for example number of land use types (7).
2.1 dew_common_parameters.par
Number of seconds per time step    SECONDS_TIMESTEP    : 86400
Number of precipitation series     NUM_PREC_SERIES     : 1
Number of temperature series       NUM_TEMP_SERIES     : 1
Prec. grad. low  per 100 m         PREC_GRAD_LOW       : 1.0
Prec. grad. high per 100 m         PREC_GRAD_HIGH      : 1.0
Altitude for 50 % reduction        GRAD_CHANGE_ALT     : 0
Prec. correction for rain          PREC_CORR_RAIN      : 1.0
Additional prec. corr. for snow    PREC_CORR_SNOW      : 1.0
Temp. lapse rate dry days          LAPSE_DRY           : 0.0
Temp. lapse rate precip. days      LAPSE_WET           : 0.0
Lake temperature memory (days)     DAY_TEMP_MEMORY     : 30.0
Lake evaporation constant          LAKE_EPOT_PAR       : 2.48292E-6
Rating curve constant              KLAKE               : .024632638
Rating curve saddle point (m)      DELTA_LEVEL         : 0.0
Rating curve exponent              NLAKE               : 1.0
Initial soil moisture        INITIAL_SOIL_MOISTURE     : .20
Initial upper zone           INITIAL_UPPER_ZONE        : 0.0
Initial lower zone           INITIAL_LOWER_ZONE        : .05
Initial saturated fraction one  INTITIAL_SATURATED_ONE : 0.0
Initial saturated fraction two  INTITIAL_SATURATED_TWO : 0.2
Initial lake temperature     INITIAL_LAKE_TEMP         : 0.0
Initial lake level           INITIAL_LAKE_LEVEL        : 0.0
Initial snow storage         INITIAL_SNOW_STORAGE      : 0.0
Initial total Reservoir      INITIAL_TOTAL_RESERVOIR   : 0.0
Day no. for zero snow storage      DAY_SNOW_ZERO       : 0
2.2 dew_Landscape_parameters.par
Type no.: type no of land class. The last is lake. The last second is glacier.
INTER_MAX: maximum interception by land cover.
EPOT_PAR: potential evaporation parameter.
WET_PER_CORR: wet period proportion, for correction of transpiration, affect soil evaporation.
ACC_TEMP: snowfall temperature, for snow accumulation in snow.h.
MELT_TEMP: melting temperatire of snow and ice, in snow.h.
SNOW_MELT_RATE: snow melt rate.
ICE_MELT_RATE: ice melt rate.
FREEZE_EFF: freezing rate of liquid water in snowpack, as an proportation of snow melt rate (SNOW_MELT_RATE).
MAX_REL: maximum retention of liquid water in snowpack.
ALBEDO: abedo of snow, not used in current version.
CV_SNOW: non-uniform distribution of snow.
MannR: manning roughness of land cover.
2.3 dew_SubSurface_parameters.par
Type no.: type no of land class. The last is glacier.
FC: filed capacity of soil.
FCDEL: filed evaporation parameters, maximum is 1.
BETA:
         INFMAX       KUZ          ALFA          PERC         KLZ          DRAW	MAXUZ
FOREST   0   2.000000    .6304750     0.0032297    1.703620   1.000000     2.00067         0.0006868    0.0025403    0	        0
Land     1   0.0003039   .5304750     0.0040252    1.703620   1.000000     0.835085        0.0083124    0.0220704    0	        0
GLACIER  2   0.6249300   .3047500     0.0040252    1.703620   0.2823610    1.42107         0.0026779    0.0136338    0   	0
2.4 Glacier related output
gam_********.out glacier annual mass balance for glacier covered area (mm)
gar_********.out glacier area (km^2)
gim_********.out glacier ice melt for glacier covered area (mm)
giv_********.out glacier ice volume (m^3), at a yearly timestep
gmc_********.out glacier mass balance average for watercourse outlet area (mm)
gmb_********.out glacier mass balance for glacier covered area (mm)

3 Modify Model 
Go to OBS_DATA to check the station observed discharge.
The needed data series is written in to series_calibration and run ../../Bin/calibration_data series_calibration 1999 (start year) 2002 (end year).  The calibrated file needed by pest will be generated and also the observed data. Copy obs_stremflow.dta to above directory. The head of obs_stremflow.dta need to be modified to the id of the sub-catchment outlet. ( #dew_00020011.var ->#dew_0*******.var).
Check the control_*** file if is right then run.
Pest-observed file.
If no calibrated parameters, just need to change the file and data. One step is to insert the pestfile_observed.txt in CAL_DATA (generated by calibration_data) to observation series in the dew.pst.
Check the necessary file. After calibration, need to copy the calibrated ***.dta (the end of dew.pst file) to the simulated directory.
Use make to compile file, such as make dew, make predew.

4 Small program
4.1 Dew_results
 (Dew_results_eng): dew_results (< input file) obs_streamflow.dta (> filename). 
4.2 Moments
the statistic of discharge file.
4.3 Calibration_data
 generate the pest required file (not all). calibration_data series_calibration.
4.4 parrep
5 Code Illustration
5.1 Output grid runoff
Sourcedew.cpp Line852
The input file of dew, hbv_element.data define the grid index of the grid runoff.
5.2 Data path
Set_env: /hdata/grid/medata/  is the file path of the grid precipitation data. (actually all the data)
/hdata/grid/medata/rr is precipitation.
In bash should set the environment variable first, open .bashrc file, to set.
Export VariableName=/hdata/fou/personli……
Or set some thing, like noclobber to stop file being over-written.
6 Note in NVE
6.1 Computer
Usename: holi       
Gate card code: 8503.
Wifi: wlan4339 wzk3Du1t8a
Glitra is to run model;
Vigga is to compile model. g++ -o output sourcecode.cpp
Personal file:  /hdata/fou/personlig/holi
Username is holi and the password is eyk149.
6.1.1 Log at home:
http://webb.nve.no/ts/
Start-> search programs and files->check point endpoint connect-> 
Password is eyk149  
Server adress: gatekeeper.nve.no
6.2 C++ Note
** the array is a pointer.
. when the object is not a pointer.
->: when the object is a pointer.
6.3 Linux command
Df: display free disk space
Du: display  the file used space
Diff: difference between files
main.exe &: run in background.
Export: change the environnement variable, such as export DISPLAY=*****.
Env : see the environment variable.
Sh: start to write sbatch script.
Dos2unix: examine the newline in window or unix format.
Xterm: to open a new window based on the current directory.
! : force to act.
6.4 Matlab note
Compile to a c++ libray.  mcc -W cpplib:myLib -T link:lib myfun(titan)

6.5 Emacs note
Ctrl+c: in the middle of the page.
Ctrl+X, i: insert a file to the current file.
Ctrl+r: search in front dirction.
Escape, >: goto the end of the file.
Escape, <: goto the head of the file.
Esc+Ctrl+Space: set the mark.
M-% (alt+shift+5): 命令用来执行查询替换操作。它首先搜索到与模式匹配的字符 串，然后在必要时替换该字符串。要执行查询替换命令，首先键人Esc %命令，然后键入要被替换的模式，并回车，再键入要替换的字符串并回车。完成上述操作之后，将搜索到与被替换的模式匹配的第一个字符串，同时出现几个选 项，每个选项都有与之对应的按键。例如，如果键入y，搜索到的字符串将被替换字符串所替换，同时，光标将位于已被替换掉的字符串上；如果键入n，将取消替 换操作，同时，光标将位于搜索到的字符串上。
Dos and unix file format:
M-x set-buffer-file-coding-system Unix

其操作步骤如下：
M-% (然后会出现Query replace的字样)
Query replace:<被替换字符串>
Query replace <被替换字符串> with:<替换字符串>
Query replacing <被替换字符串> with <替换字符串>:（? for help）
查询替换命令的选项如下：
y 或者 空格 键 替换搜索到的字符串
n 或者 Del 键 取消替换搜索到的字符串
^ 回到前一个搜索到的字符串
! 替换所有没有替换的与模式匹配的字符串
ESC 键 退出本次查询搜索 

与其他查找命令一样，查询搜索命令不允许在输入的查找字符串中使用特殊字符。但如果想使用特殊字符，可以使用 query-replace-regexp 命令，要运行该命令，必须首先键入 M-x 命令进入编辑器的小缓冲区中。

批量字符串替换
case：在文件夹/dirA下替换str1为str2
operation:  M+x ---> find-grep-dired ---> directory (if right, enter) str1 ----> t ----> Q ----> str1 ---> str2 ---> 逐个确认是否替换； 
                    替换完成后保存替换： Ctrl + x --> s
Rectangular operation: Ctrl + x r ..

6.6 Data Datasets







Databasearkiver: 	Sanntid, Hytran, Hykval, Hydag_t, Hydag
Kontrollprogrammer: 	Prikon, Kondag
Nederst: 		Valgene i programmet ”Finut” for å få sett forskjellige data.

/hdata/grid/medata/rr is precipitation.
stored in the hydrologic year, start from August, September (the first month of snow).
6.6.1 Discharge data: Start&


DAGUT and then click Start program.

Click Hent.

Type the id of time series and then click <=Søk vha. Serie-id. If there is no problem, then click Bruk serir. If the id of time serie has some problem, Bruk Serie will be grey and cannot be clicked.

The searched results came out and click Alle år på, meaning choose all the year. (Alle år av, none of them; Alle år på mellon, choose several continue years). Then click Direkte til arbeidstabell, to save the results.

This show the results of last step, no change was necessary (maybe check of the results). Click Lagre på fil, meansing save to file.

Choose the preferred format, usually Vardat II. And the click Send til Skriver.

Find the destination file, usually the home directory (where you start Start).

The message of success. Good luck!
Tips about Norwegian
Søk vha. Seirie-id: search via id.
Lukk vindu: close the window.
vis kommentarfeltet I eget vindu: show the comments in a new window.
Stasjonstype: Forvaltningsstasjon, Stasjonsstatus: Aktiv
Stasjonskommentar: 
Isoppstuede perioder hver vinter, og perioder med heverteffekt over fossenakken: Drive Type: Management Station, Station Status: Active
Station Comments:
Isoppstuede periods each winter, and periods of siphoning effect of the waterfall neck.
Kurvekvalitet: curve quality.

6.6.2 Catchment data
Start->Hysopp. The password is ‘pwh0l1’, in /home/holi/.sypassword in Glitra.

Click ‘Stasjon’.

Click ‘Stasjonsopplysninger’.

Write the search item, ‘Vassdragsnr’: watercourse no. or anyone. Then click ‘Utfør søk’: perform search.
Then the result come out. If click ‘Søkemodus’: search mode, the result will disappear and start a new search.

Click ‘Nedbøfelt fra GIS’: drainage basin from the GIS data will appear.

Information about topography, elevation and vegetation will come out. 
6.6.3 GIS data
****_S: cover the catchment drained to Sweden;
****_F: cover the catchment drained to Finnland;
****_SF: cover the catchment drained to Finnland or Sweden;

There is all data necessary. But how to find and use it.
Feature Dataset:	VANN.Hydrologi_INNSYN
Feature Class:	VANN.HYDRA_FeltTotalMstaF: the whole catchment
Choose one layer and right click.
 
Click ‘Properties ...’.

Make querry. 

If the item is not shown, check ‘Field’ and click the attribute. Do not forget ‘OK’.
6.6.4 Project Atlas Data
http://atlas.nve.no/ge/Viewer.aspx?Site=NVEAtlas#




Find the location of your .gepd file.
6.6.5 Reservoir data
G:\Rapporter\Hydra2\Crystal\Magasinserier.exe
6.6.6 River profile data
"C:\Program Files (x86)\HEC\HEC-RAS\4.1.0\ras.exe"
The file is in the model ****.g*** file.




7 PEST
Template file, instruction file, control file
Tempchek:
Pestchek: check pest required file and format, pestcheck dew.pst.
Inschek:
Pestgen
Template file can be modified from model input file. 	//ptf
Introduction file 	//pif
Control file	//pcf, ***.pst

if you name the PEST control file calib.pst, PEST will generate files calib.rec (the run record file), calib.par (best parameter values achieved), calib.rst (restart information stored at the beginning of each optimisation iteration), calib.jac (the Jacobian matrix for a possible restart), calib.jst (the same file from the previous optimisation iteration), calib.prf (special Parallel PEST restart file), calib.jco (the Jacobian matrix pertaining to best parameters for access by the JACWRIT utility), calib.sen (parameter sensitivities), calib.seo (observation sensitivities), calib.res (tabulated observation residuals), calib.rei (interim observation residuals), calib.mtt (interim covariance matrix and related matrices) and some other files with the same basename, depending on its current mode of operation. User

/hdata/fou/personlig/holi/Dew/Calibration/Gaulfoss_grid_6/
Note: the modell input or output file is not included.
The Calibrartin/bin is very useful and its code is in Source/Utilities.
7.1 File
7.1.1 Necessary file
Observed data: pest_obs.txt	//the observed data of the model simulate variable value.
Instruction data:122.9.0.1001.1_ins	//instruction how to read data (I think it is the introductions of pest_obs.txt).
Dew_parameters.tpl	// the parameters input to pest, used by model
Hbv_soil_parameters.tpl	// the parameters input to pest, used by model
Pest_template_2_range.txt	// the initial value and range of parameters.
Control_dew_sim.txt	//the control file of simulation model.
Control_dew_cal.txt	//the control file of calculation model.
Orginal		// the initial value of pest
	


Start_dew.sh	// the script to start the model.
Start_pest	// the script to PEST.
Series_calibration	// the observed series in the OBS_DATA.
Stations_nve.dta	// the file about discharge data in the NVE system, their named rule.
Hbv_soil_parameters.pest	// ( the results of the soil parameters)
Start_tempcheck.sh	// the script to run tempcheck.
Dew.pst	// the main file of pest.

7.1.2 Ouput file
kiwa_soil_parameters.dta	// the results kiwa of the soil parameters about soil.
Dew_common_parameters.dta	//(model output)
Hbv_soil_parameter.dta	//
Start_time	// the start time of calibration, since it is very long for calibration.
Dew.par	// the calibrated value of PEST, same as the result.
Log_pest	// the printed information when calibrating using PEST.
Dew_landscape_0.dta	// the output by run 0.
Jacob.runs	// run information of pest.
Pest_template_3.txt	// like dew.pest, but the third time of run.
Plotfil	// the last simulation results by PEST.
modelPerformance.txt	// the summary of model performance.
Run.sh
Dew.mtt	//
Dew.rei
Dew.res
Dew.seo
Tempcheck_1.log	// printed information tempcheck the 1 time.
sortPerformance	// the sorted results of calibrated model performance of pest times
pest.rec		// printed information of pest
rand_log	// the random number

Dew_week_mean.res	// the weekly mean of lake storage.
Dew_mth_mean.res	// the monthly mean of lake storage.
Dew.sen
Dew.cnd
Dew_log.txt
Tempcheck_3.log
Stations_nve.txt

PARAMETERS/…
Test_waterland.dta
Pestfile_observed.txt
Pest.hlt
Pest.tmp
Dew.var
Obs_streamflow 
Dew.mse: 
Obs_dew
7.2 Code
The executable file in the ../Dew/Calibration/Bin.
Code file is in .. /Dew/Source/Utilities.
Start_pest: type these command.
Pest program PARREP allows you to replace initial parameter values as recorded on a PEST control file with those recorded in a ‘parameter value file’, the latter (having been written by PEST) containing the best parameter values achieved on the previous PEST run.

Bin:
start_calibration.sh	// used in Multiple_parametes_Gaulfoss_elev_6 to compare the different times calibration.
generatePestFile	// used in Multiple_parametes_Gaulfoss_elev_6 to compare the different times calibration. Generate pest file, since they are almost same except the intial value set by random.
calibration_data_log	//
calibration_data_veps	//
8 Norsk
Mill. : million.
Dybdekart: bathymetric chart.
Vannkraftverk: hydro-electric station.
Kraft: power.
Minimikro: mini-micro.
Vind: wind.
Trafo: transformer.
Delfelt: subfield.
Everk: electicity plant, electricity works.
Vassdrag: water way; watercourse.
Nedbørfelt: rainfall precipitation drainage basin.
Hav: sea.
Minste-:minimal.
Het:hot.
Elvenett: drainage network (river).
Målestasjon: measure station.
Avsluttet: finished.
Local: local.
Drift: management, operation.
Sikringstiltak: safety measure.
Lavpunkt: low point.
Elv: river.
År: year.
Kvikkleire: quick clay.
Faregrad: risk degree.
Skred: landslide.
Konsekvens: consequences.
Risiko: risk.
Meget: a lot of; plenty of.
Mindre: less; minor.
Alvor: serious.
Verneplan: protection map.
Tilgang: acess; admission.
Innsyn: access; inspection.
Vedtak: decision; resolution; adoption; passage.
Konsesjon: concession; licence.
Stadium: stage; phase.
Under: below; during.
Bygging: building.
Gitt: take for granted.
Behandle: handle; treat.
Søkt: strained; artificial.
Melding: Information; message. 
Utkast: design; draft; outline; sketch.
Søknad: application; petition.
Fritak: exemption.
Magasin: store; warehouse; storage house.
Potensial: potential.
Staten: the state; the government.
Kartverk: series of maps.
Bygning: building; house.
Anlegg: plan; design; layout.
Turisthytter: tourist cattage.
vegbom: road barrier; road block.
Veg: route.
Jernbane: iron rail way.
Høydepunkt: peak; height; climax.
Fastmerke: bench mark. (fast: solid. Merk: mark.)
Linje: line.
Ledning: cable; conduction; pipe.
Naturinfo: nature info.
Grense: limit; boundary.
Eiendom: residual area.
Ferge: ferry; ferry boat; train ferry.
Passasje: passage.
Anne: other; second.
Båtrute: boating route.
Bil: car.
Ferdsel: traffic; coming and going.
Somferdsel: communication.
Bru: bridge.
Bane: court; course; path; track; line.
Lufthvan: airport.
Tunnel: tunnel.
Veger: break fresh ground.
Verneområde: protected area.
Råde: connecting road and advise.
Skyte: shoot; put out; put forth.
Tank: tank.
Flate: flat; plain.
Høydekurver: contours.
Vannløp: stream; water course. (løp: race.)
Vannkontur: vann outline.
Arealdekke: land cover.
Havområde: maritime zone.
Ortofoto: orthophoto. (正像摄影).
Bygg: building; construction.
Bolig: residence.
Overnatting: over night.
Kirke: Church.
Hytte: cottage; cabin.
Gård: farm.
Servering: service.
Musser:
Bedehus: church.
Annet: other.
Offentlig: public place.
Idrett: sport.
Seter: summer pasture. 
Uthus: out house.
Kommune-: municipal; local.
Bre: glacier.
Skog: forest.
Myr-: bog.
Vatn-: vann.
Restriksjon: restriction.
Reservat: chief park warden (看守); reserve.
Biotop: Biotope (生物栖息地).
Plante: plant.
Minne: memory; remind.
Midlertidig: temporary.
Dus: soft.
Utlandet: from abroad; from oversea.
Bakgrunn: background.
Dekke: covering.
Felt: catchment.
Høydelag:
Fjellskygge:
f
Veg avgrensninger:
Regine: catchment database.
Vannvei l: tunnel; pipe. Line.
Min steenhet: small unit in Regine.
Sidenedbørfelt: side tributary.
Områder: area. 
Tidsserier: time-series.
Års avrenning: anual runoff.
Flomsoner: flood-zone.
Flom utsatt: flood export; out. Risk mapping.
Analyseflate: 
Everks område: energy company area.
Kraftledninger: power line.
Vassdrags område: basins.
Avslått: reject; decline.


.


