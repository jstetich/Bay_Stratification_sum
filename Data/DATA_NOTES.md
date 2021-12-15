# Vertical Profile Data

## FOCB Monitoring Sites.xlsx
Contains data on the location of FOCB monitoring locations.

  Name       |  Contents
-------------|----------------------------------
Station_ID	 | FOCB Site Codes
Station_Name | Site name, using familiar place names
Town	       | Station location, as a town.
Y	           | Latitude, WGS 1984
X	           | Longitude WGS 1984
Category     | Surface, Continuous, or Profile


## FOCB Profile All Current Sites.xlsx
Data file contains downcast data collected by FOCB using YSI sondes.  Details
of instrumentation vary over time. For details, contact FOCB or CBEP.

 Name              |  Contents                           | Units
-------------------|-------------------------------------|----------
Site Number	       |  FOCB Alphanumeric site code        |  
Date	             |  Excel date formatted as dd/mm/yy   | Integer   
Time	             |  Excel time, formatted as hh:mm     | Numeric 
Sample Depth       |  Depth of sonde when data collected | meters 
Water Temperature	 |  Temperature recorded by sonde      | degree C
Salinity           |  Salinity (based on conductivity)   |	PSU
Density	           |  Calculated water density           | kg/m3
Dissolved Oxygen   |  Dissolved oxygen by sonde (technology changes) |	mg/l
DO Percent         |  Oxygen percent saturation          | Saturation	%
Chlorophyll        |  Chlorophyll concentration (fluorescence) | ug/l
pH	               |  pH                                 | NBS pH scale
Month	             |  Month, January = 1                 | Integer
Year	             |  Year, four digit                   | Integer


We did not confirm the nature of the time coordinates for these data, as they
made no practical difference for our analyses. We believe that they are local 
clock time, but we can not rule out the possibility that they are based on a
fixed offset from UTC, thus ignoring annual shifts between daylight savings 
time and standard time. If that distinction is important, data users should
request clarification directly from Friends of Casco Bay.

Chlorophyll sensors were calibrated, but chlorophyll data was not calibrated
against laboratory chlorophyll analyses.

# DEP Data
## dep_sonde_data.csv
CBEP staff produced this file by rearranging data provided by Maine DEP. The
represents data collected by DEPE using YSI sondes.  We did not retreive details
of specific sonde models or sensors used.


 Name              |  Contents                           | Units
-------------------|-------------------------------------|----------
site_name          | Long name of site wit hsite code
site               | Site Code
dt                 | Date of data collection             | Month / Day / Year
month              | Month                               | Three letter codes
year               | Year                                | Four digit integer
time               | Time of sample collection           | Hour: minute
hour               | Hour during which sample was collected | Integer
depth              | Measured depth of data collection   | meters
temp               | Temperature recorded by sonde       | degree C
salinity           | Salinity (based on conductivity)    |	PSU (roughly, PPTH)
ph                 | pH                                  | NBS pH scale
pctsat             | Oxygen percent saturation           | Saturation	%
do                 | Dissolved oxygen by sonde           |	mg/l
chl_a_sonde        | Chlorophyll concentration (fluorescence) | ug/l
turbidity          | Turbidity                           | NTU
turbidity_cens     | Flag for left censored turbidity data |


Maine DEP sampled some locations in common with FOCB.  The two organizations use
the same site codes for those locations.  We double checked to ensure that
there is no duplication of data from the two sources. This file and
"FOCB profile All Sites.xlsx" are independent data.

We did not receive information on the nature of the time coordinates for these 
data. We believe that they are local clock time, but we can not rule out the 
possibility that they are based on a fixed offset from UTC, thus ignoring annual
shifts between daylight savings time and standard time. If that distinction is 
important, data users should request clarification directly from DEP.

## combined_locations.csv
We created this table when we combined data on monitoring locations 
tracked by FOCB and Maine DEP.  Details are in the `Combined_Nutrients`
[archive](https://github.com.CBEP-SoCB-Details/Combined_Nutrients).  The file
includes information on location of sampling sites used by DEP and FOCB.  We use
it here because it offers a regionalization of sampling stations. to compare
"inshore" vs "offshore" stations in trend analyses.

site,site_name,short_name,latitude,longitude,source,region

  Name       |  Contents
-------------|----------------------------------
site      	 | FOCB / DEP Site Codes  
site_name | Site name, using familiar place names, differs by source  
short_name   | Shorter and more consistent version, convenient for mappiing and plotting  
latitude	   | Latitude, WGS 1984  
longitude    | Longitude WGS 1984  
Source       | DEP, FOCB, or Both
region       | String identifying certain regions of the Bay (used for plotting)  

