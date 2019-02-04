diq3
================
Subharthi
February 4, 2019

R Markdown
----------

``` r
# first set working directory 

lib.need = c("plyr", "rgdal", "maptools", "ggplot2", "rgeos", "RColorBrewer", 
             "stringr", "xtable", "scales", "foreign", "lubridate", "ggmap",
             "sp", "spatstat", "openxlsx", "gpclib", "PBSmapping", "lattice", 
             "Grid2Polygons", "RColorBrewer", "gstat",
             "KernSmooth", "MASS", "splancs", "spacetime", "plyr", "raster", "inlmisc")
new.packages <- lib.need[!(lib.need %in% installed.packages()[,"Package"])]
if(length(new.packages)) install.packages(new.packages)
```

    ## Installing package into 'C:/SPB_Data/R/win-library/3.5'
    ## (as 'lib' is unspecified)

    ## Warning in install.packages :
    ##   package 'Grid2Polygons' is not available (for R version 3.5.2)

``` r
lapply(lib.need, require, character.only=T)  # load needed library
```

    ## Loading required package: Grid2Polygons

    ## Warning in library(package, lib.loc = lib.loc, character.only = TRUE,
    ## logical.return = TRUE, : there is no package called 'Grid2Polygons'

    ## [[1]]
    ## [1] TRUE
    ## 
    ## [[2]]
    ## [1] TRUE
    ## 
    ## [[3]]
    ## [1] TRUE
    ## 
    ## [[4]]
    ## [1] TRUE
    ## 
    ## [[5]]
    ## [1] TRUE
    ## 
    ## [[6]]
    ## [1] TRUE
    ## 
    ## [[7]]
    ## [1] TRUE
    ## 
    ## [[8]]
    ## [1] TRUE
    ## 
    ## [[9]]
    ## [1] TRUE
    ## 
    ## [[10]]
    ## [1] TRUE
    ## 
    ## [[11]]
    ## [1] TRUE
    ## 
    ## [[12]]
    ## [1] TRUE
    ## 
    ## [[13]]
    ## [1] TRUE
    ## 
    ## [[14]]
    ## [1] TRUE
    ## 
    ## [[15]]
    ## [1] TRUE
    ## 
    ## [[16]]
    ## [1] TRUE
    ## 
    ## [[17]]
    ## [1] TRUE
    ## 
    ## [[18]]
    ## [1] TRUE
    ## 
    ## [[19]]
    ## [1] FALSE
    ## 
    ## [[20]]
    ## [1] TRUE
    ## 
    ## [[21]]
    ## [1] TRUE
    ## 
    ## [[22]]
    ## [1] TRUE
    ## 
    ## [[23]]
    ## [1] TRUE
    ## 
    ## [[24]]
    ## [1] TRUE
    ## 
    ## [[25]]
    ## [1] TRUE
    ## 
    ## [[26]]
    ## [1] TRUE
    ## 
    ## [[27]]
    ## [1] TRUE
    ## 
    ## [[28]]
    ## [1] TRUE

``` r
setwd("C:\\Users\\developer\\Documents\\nij_realtime_crime_forecasting")
getwd()
```

    ## [1] "C:/Users/developer/Documents/nij_realtime_crime_forecasting"

``` r
source("NIJ-funcs.R") # function files
```

    ## Loading required package: Grid2Polygons

    ## Warning in library(package, lib.loc = lib.loc, character.only = TRUE,
    ## logical.return = TRUE, : there is no package called 'Grid2Polygons'

``` r
# load from the shape file 
# and read spatial geometry data

crime.portland.shape = readOGR(dsn=path.expand("./maps/portland-police-districts"), 
                               layer = "Portland_Police_Districts")
```

    ## OGR data source with driver: ESRI Shapefile 
    ## Source: "C:\Users\developer\Documents\nij_realtime_crime_forecasting\maps\portland-police-districts", layer: "Portland_Police_Districts"
    ## with 60 features
    ## It has 9 fields
    ## Integer64 fields read as strings:  OBJECTID_1 OBJECTID

``` r
crime.dbf = read.dbf('./maps/portland-police-districts/Portland_Police_Districts.dbf')

# load from the shape file 
# and read spatial geometry data


origProj <- crime.portland.shape@proj4string ## Store original projection

PortlandBoundaryPolygon <- NIJ.getShapeBoundaryPolygon('./maps/portland-police-districts/Portland_Police_Districts.shp', origProj)
```

    ## Warning: readShapePoly is deprecated; use rgdal::readOGR or sf::st_read

``` r
#Getting polygon area
PortlandBoundaryPolygonArea <- NIJ.getPolygonArea(PortlandBoundaryPolygon)

# area of POrtland in square miles
PortlandBoundaryPolygonArea$area * 3.58700658032258e-08 # mi^2 
```

    ## [1] 147.7037

``` r
# length


cell.length = sqrt(NIJ.convSqMi2SqFt(0.25)) #sqrt(.25) * 5280
cell.width = sqrt(NIJ.convSqMi2SqFt(0.75)) #sqrt(.75) * 5280
# width  
SpatialPortlandGrid <- NIJ.createCellGrid(crime.portland.shape, cell.length,cell.width
                                          ,origProj)
PortlandBoundaryPolygonGridded <- NIJ.createGriddedBoundaryPolygon(crime.portland.shape, SpatialPortlandGrid)


# handle crime data for notebook

data.2012 = read.xlsx ("./data/2012/NIJ2012_MAR01_DEC31.xlsx", sheet = 1, colNames=T)
```

Including Plots
---------------

You can also embed plots, for example:

![](README_figs/README-plots2-1.png)

    ## Warning: use *apply and slot directly

    ##    CATEGORY         CALL.GROUPS        final_case_type   
    ##  Length:146927      Length:146927      Length:146927     
    ##  Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character  
    ##                                                          
    ##                                                          
    ##                                                          
    ##                                                          
    ##   CASE.DESC            occ_date      x_coordinate      y_coordinate   
    ##  Length:146927      Min.   :40969   Min.   :7564263   Min.   :641652  
    ##  Class :character   1st Qu.:41050   1st Qu.:7644097   1st Qu.:676143  
    ##  Mode  :character   Median :41121   Median :7654718   Median :683655  
    ##                     Mean   :41121   Mean   :7657759   Mean   :684555  
    ##                     3rd Qu.:41192   3rd Qu.:7671602   3rd Qu.:691719  
    ##                     Max.   :41274   Max.   :7721767   Max.   :787862  
    ##                                                                       
    ##   census_tract   
    ##  Min.   :   100  
    ##  1st Qu.:  2303  
    ##  Median :  4800  
    ##  Mean   :  6254  
    ##  3rd Qu.:  8202  
    ##  Max.   :980000  
    ##  NA's   :9056

    ## [1] "BURGLARY"            "MOTOR VEHICLE THEFT" "OTHER"              
    ## [4] "STREET CRIMES"

    ## [1] " SUSPICIOUS"        "DISORDER"           "NON CRIMINAL/ADMIN"
    ## [4] "PERSON CRIME"       "PROPERTY CRIME"     "TRAFFIC"

    ##  [1] "77"     "ACCHR " "ACCHRP" "ACCINJ" "ACCNON" "ACCUNK" "ANIML "
    ##  [8] "ANIMLP" "AREACK" "ASSIST" "ASSLT " "ASSLTP" "ASSLTW" "BOMBTH"
    ## [15] "BURG  " "BURGP " "CHEM  " "CHEMTH" "CIVIL " "DEVICE" "DIST  "
    ## [22] "DISTP " "DISTW " "DUII  " "ESCAPE" "EVICT " "FLAG  " "FOLLOW"
    ## [29] "FPURS " "FRAUD " "FRAUDP" "FWB   " "FWH   " "FWI   " "FWN   "
    ## [36] "GANG  " "HAZARD" "IDENT " "MSG   " "NOISE " "PARK  " "PARTY "
    ## [43] "POLINV" "PREMCK" "PROP  " "PROWLP" "RED   " "RIVPOL" "ROB   "
    ## [50] "ROBP  " "ROBW  " "SCHL  " "SCHLP " "SEIZE " "SERVE " "SHOOT "
    ## [57] "SHOOTW" "SHOTS " "STAB  " "STABW " "STNDBY" "SUBSTP" "SUSP  "
    ## [64] "SUSPP " "SUSPW " "THEFT " "THEFTC" "THEFTP" "THRET " "THRETP"
    ## [71] "THRETW" "TMET  " "TMETP " "TMETW " "TPURS " "TRANS " "TRASH "
    ## [78] "TRASHP" "TRASTP" "UNWNT " "UNWNTP" "UNWNTW" "VAND  " "VANDP "
    ## [85] "VEHREC" "VEHST " "VEHSTP" "VICE  " "W26   " "WARR  " "WARRC "
    ## [92] "WELCK " "WELCKP" "WRONG "

    ##  [1] "ACCIDENT - HIT & RUN - PRIORITY                   "
    ##  [2] "ACCIDENT - HIT AND RUN - COLD                     "
    ##  [3] "ACCIDENT - INJURY                                 "
    ##  [4] "ACCIDENT - NON INJURY                             "
    ##  [5] "ACCIDENT - UNKNOWN INJURY                         "
    ##  [6] "ANIMAL PROBLEM - PRIORITY                         "
    ##  [7] "ANIMAL PROBLEM                                    "
    ##  [8] "AREA CHECK                                        "
    ##  [9] "ASSAULT - COLD                                    "
    ## [10] "ASSAULT - PRIORITY                                "
    ## [11] "ASSAULT - WITH WEAPON *H                          "
    ## [12] "ASSIST - CITIZEN OR AGENCY                        "
    ## [13] "ASSISTANCE - FIRE / EMS NEED POLICE *H            "
    ## [14] "BOMB - DEVICE DISCOVERED (33B) *H                 "
    ## [15] "BOMB - THREAT (33B)                               "
    ## [16] "BOMB OR CHEM POLICE INVESTIGATION (33B/33C)       "
    ## [17] "BURGLARY - COLD                                   "
    ## [18] "BURGLARY - PRIORITY *H                            "
    ## [19] "CHEMICAL OR BIOLOGICAL (33C)                      "
    ## [20] "CHEMICAL OR BIOLOGICAL THREAT (33CTH)             "
    ## [21] "CIVIL - CIVIL PROBLEM                             "
    ## [22] "CIVIL - EVICTION                                  "
    ## [23] "CIVIL - PROPERTY SEIZURE                          "
    ## [24] "CIVIL - SERVE PAPERS                              "
    ## [25] "CIVIL - STANDBY                                   "
    ## [26] "DELIVER MESSAGE                                   "
    ## [27] "DETOX TRANSPORT                                   "
    ## [28] "DISTURBANCE - COLD                                "
    ## [29] "DISTURBANCE - PRIORITY                            "
    ## [30] "DISTURBANCE - WITH WEAPON *H                      "
    ## [31] "DRIVING UNDER INFLUENCE                           "
    ## [32] "ESCAPE FROM CUSTODY                               "
    ## [33] "FIREWORKS - HAZARD                                "
    ## [34] "FIREWORKS - ILLEGAL                               "
    ## [35] "FIREWORKS - NOISE (BROADCAST ONLY)                "
    ## [36] "FIREWORKS - NOISE (MDC DISPATCH)                  "
    ## [37] "FLAGDOWN                                          "
    ## [38] "FOLLOW-UP                                         "
    ## [39] "FOOT PURSUIT *H                                   "
    ## [40] "FRAUD - COLD                                      "
    ## [41] "FRAUD - PRIORITY                                  "
    ## [42] "GANG RELATED                                      "
    ## [43] "HAZARD - HAZARDOUS CONDITION                      "
    ## [44] "HAZARD - WRONG-WAY DRIVER *H                      "
    ## [45] "IDENTITY THEFT                                    "
    ## [46] "ILLEGAL DUMPING - COLD                            "
    ## [47] "ILLEGAL DUMPING - PRIORITY                        "
    ## [48] "NOISE DISTURBANCE                                 "
    ## [49] "PARKING PROBLEM                                   "
    ## [50] "PARTY DISTURBANCE                                 "
    ## [51] "PERSON CONTACT (86)                               "
    ## [52] "PREMISE CHECK                                     "
    ## [53] "PROPERTY LOST, FOUND, RECOVERED                   "
    ## [54] "PROWLER                                           "
    ## [55] "RIVER - MARINE INCIDENT                           "
    ## [56] "ROBBERY - COLD                                    "
    ## [57] "ROBBERY - PRIORITY *H                             "
    ## [58] "ROBBERY - WITH WEAPON *H                          "
    ## [59] "SCHOOL INCIDENT - COLD                            "
    ## [60] "SCHOOL INCIDENT - PRIORITY                        "
    ## [61] "SHOOTING - COLD                                   "
    ## [62] "SHOOTING - WITH WEAPON *H                         "
    ## [63] "SHOTS FIRED                                       "
    ## [64] "STABBING - COLD                                   "
    ## [65] "STABBING - WITH WEAPON *H                         "
    ## [66] "SUBJECT STOP - SDC                                "
    ## [67] "SUSPICIOUS - PRIORITY                             "
    ## [68] "SUSPICIOUS - WITH WEAPON *H                       "
    ## [69] "SUSPICIOUS SUBJ, VEH, OR CIRCUMSTANCE             "
    ## [70] "THEFT - COLD                                      "
    ## [71] "THEFT - PRIORITY                                  "
    ## [72] "THEFT - SUBJECT IN CUSTODY                        "
    ## [73] "THREAT - COLD                                     "
    ## [74] "THREAT - PRIORITY                                 "
    ## [75] "THREAT - WITH WEAPON *H                           "
    ## [76] "TRAFFIC PURSUIT *H                                "
    ## [77] "TRAFFIC STOP                                      "
    ## [78] "TRANSPORT                                         "
    ## [79] "TRIMET INCIDENT - COLD                            "
    ## [80] "TRIMET INCIDENT - PRIORITY                        "
    ## [81] "TRIMET INCIDENT - WITH WEAPON *H                  "
    ## [82] "UNWANTED PERSON - PRIORITY                        "
    ## [83] "UNWANTED PERSON - WITH WEAPON *H                  "
    ## [84] "UNWANTED PERSON                                   "
    ## [85] "VANDALISM - COLD                                  "
    ## [86] "VANDALISM - PRIORITY                              "
    ## [87] "VEHICLE RECOVERED                                 "
    ## [88] "VEHICLE STOLEN - COLD                             "
    ## [89] "VEHICLE STOLEN - PRIORITY                         "
    ## [90] "VICE-DRUGS, LIQUOR, PROSTITUTION, GAMBLING        "
    ## [91] "WARRANT - WALK-IN / COUNTER                       "
    ## [92] "WARRANT                                           "
    ## [93] "WELFARE CHECK - COLD                              "
    ## [94] "WELFARE CHECK - PRIORITY                          "

    ##                 CATEGORY               CALL.GROUPS  final_case_type
    ##  BURGLARY           :934    SUSPICIOUS       :187   BURGP  :747    
    ##  MOTOR VEHICLE THEFT:  0   DISORDER          :  0   PROWLP :187    
    ##  OTHER              :  0   NON CRIMINAL/ADMIN:  0   77     :  0    
    ##  STREET CRIMES      :  0   PERSON CRIME      :  0   ACCHR  :  0    
    ##                            PROPERTY CRIME    :747   ACCHRP :  0    
    ##                            TRAFFIC           :  0   ACCINJ :  0    
    ##                                                     (Other):  0    
    ##                                               CASE.DESC      occ_date    
    ##  BURGLARY - PRIORITY *H                            :747   Min.   :40969  
    ##  PROWLER                                           :187   1st Qu.:41053  
    ##  ACCIDENT - HIT & RUN - PRIORITY                   :  0   Median :41134  
    ##  ACCIDENT - HIT AND RUN - COLD                     :  0   Mean   :41131  
    ##  ACCIDENT - INJURY                                 :  0   3rd Qu.:41210  
    ##  ACCIDENT - NON INJURY                             :  0   Max.   :41274  
    ##  (Other)                                           :  0                  
    ##   x_coordinate      y_coordinate     census_tract  
    ##  Min.   :7616228   Min.   :656504   Min.   :  100  
    ##  1st Qu.:7646948   1st Qu.:673554   1st Qu.: 2000  
    ##  Median :7660383   Median :682481   Median : 4102  
    ##  Mean   :7660193   Mean   :683588   Mean   : 5041  
    ##  3rd Qu.:7673212   3rd Qu.:692672   3rd Qu.: 8202  
    ##  Max.   :7693318   Max.   :724482   Max.   :30600  
    ##                                     NA's   :2

![](README_figs/README-plots2-2.png)

    ##     cell.ids          week                  V1         
    ##  0 X115 :   45   Min.   :2012-02-27   Min.   :0.00000  
    ##  0 X116 :   45   1st Qu.:2012-05-14   1st Qu.:0.00000  
    ##  0 X149 :   45   Median :2012-07-30   Median :0.00000  
    ##  0 X150 :   45   Mean   :2012-07-30   Mean   :0.02624  
    ##  0 X151 :   45   3rd Qu.:2012-10-15   3rd Qu.:0.00000  
    ##  (Other):35370   Max.   :2012-12-31   Max.   :3.00000  
    ##  NA's   :    1

![](README_figs/README-plots3-1.png)

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
