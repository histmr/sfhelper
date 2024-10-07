# sfhelper

License: MIT

For more information please contact <mark.ravina@austin.utexasedu>

## Installation

To install from GitHub use the devtools package

    devtools::install_github("histmr/sfhelper")

## Functions

### geolocate()

The geolocate() function takes a data frame with a column of place names
(toponyms) and a column of regions, specified by their two-digits ISO
codes. The defualt colums names are “place” and “iso”. The function uses
the API of the World Historical Gazeteer (<https://whgazetteer.org/>) to
generate a four column data frame with the toponym, ISO code, longitude,
and latitude. Many toponyms will retrun multple hits. You will probably
want to import a data frame, but here’s an example of a data frame
create within R

    library(sfhelper)
    df <- data.frame("place"=c("Tokyo","Edo","Prague","Prague"),"iso"=c("JP","JP","CZ",""))
    geolocate(df,place,iso)

    ##    toponym codes     long     lat
    ## 2    Tokyo    JP 139.6917 35.6895
    ## 3    Tōkyō  NULL 139.5000 35.7500
    ## 4    Tokyo  NULL 139.7500 35.6667
    ## 5    Tokyo    JP 139.6917 35.6895
    ## 6    Tōkyō  NULL 139.5000 35.7500
    ## 7   Prague    CZ       NA      NA
    ## 8   Prague    US -92.2667 34.2833
    ## 9   Prague    US -96.6833 35.4833
    ## 10  Prague    US -96.8000 41.3000
    ## 11 Bohemia  NULL -87.1500 30.4833
    ## 12 Bohemia  NULL  14.4333 50.1000
    ## 13   Praha  NULL -93.5667 44.5333

### st\_transform\_meridian

Changing the meridian when re-projecting sf object often creates broken
polygons and other unwanted artifacts. This function repairs most common
errors. For example, using the map from
**rnaturalearth**,**st\_transform\_repair()** corrects for the new
meridian.

    library("rnaturalearth")
    library("tidyverse")

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

    library("sf")

    ## Linking to GEOS 3.11.0, GDAL 3.5.3, PROJ 9.1.0; sf_use_s2() is TRUE

    world.sf <- ne_countries(scale = "medium", returnclass = "sf")

    new_crs <- "+proj=moll +lat_1=-10 +lon_0=-70"

    new.sf <- st_transform(x = world.sf, crs = new_crs)
    ggplot() + geom_sf(data=new.sf)

![](README_files/figure-markdown_strict/unnamed-chunk-3-1.png)

    new.sf <- st_transform_repair(x = world.sf, crs= new_crs)

    ## Spherical geometry (s2) switched off
    ## although coordinates are longitude/latitude, st_intersection assumes that they
    ## are planar

    ## Warning: attribute variables are assumed to be spatially constant throughout
    ## all geometries

    ## Spherical geometry (s2) switched on

    ggplot() + geom_sf(data=new.sf)

![](README_files/figure-markdown_strict/unnamed-chunk-3-2.png)

For the orthographic projection …

    new_crs <- "+proj=ortho +lat_0=40 +lon_0=-12"
    ggplot() + geom_sf(data= world.sf %>% st_transform(new_crs))

![](README_files/figure-markdown_strict/unnamed-chunk-4-1.png)

…**st\_transform\_repair** fixes the broken polygons

    ggplot() + geom_sf(data=world.sf %>% st_transform_repair(new_crs))

    ## Warning in st_cast.sf(., "LINESTRING", do_split = TRUE): repeating attributes
    ## for all sub-geometries for which they may not be constant

![](README_files/figure-markdown_strict/unnamed-chunk-5-1.png)
