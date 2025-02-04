First Walkthrough Notes
================
Kristina Riemer

## Video 1: Objectives + TERRA REF Overview + Getting Started

  - They should take this time during the intro bit to install
    everything and/or get RStudio (local or VICE) running.
  - Put up instructions on screen from email for VICE.
  - If this takes a while, can catch up during second weather section.
  - Paste all links into chat on Zoom.

#### Objectives

These walkthroughs are a hands-on introduction to how to use data
produced by TERRA REF project.

The intended audience is community members and anyone interested in
these data.

These live walkthroughs will be turned into video tutorials that can be
viewed at any time. They will be put on YouTube, and I’ll provide links
once they’re ready.

#### Project overview

TERRA REF is a project funded by the Advanced Research Projects Agency
for Energy (ARPA-E). The objective is to advance the science and
technology of high throughput field phenotyping, which is the automated
measurement of plants. TERRA REF has deployed an exceptionally large
robot capable of collecting data from a range of sensors at 1 mm spatial
resolution every other day. You can learn more about the project from
the website, terraref.org. The suite of sensors include visible light
cameras, hyperspectral cameras, a laser 3d scanner, and environmental
data. In addition there are weather stations and high resolution genome
sequences for hundreds of varieties of Sorghum being monitored.

These tutorials are to make these data accessible and useable. I will
provide an overview of how to use computational tools to get these data,
and combine, plot, and analyze them.

We have written online tutorials
[here](https://terraref.github.io/tutorials/). These walkthroughs and
videos complement those materials, which are growing and improving
continually.

#### VICE setup and use

Launching TERRA REF app on VICE:

1.  Google “Cyverse” and click link to website
2.  Select Products tab
3.  Under “Discovery Environment”, select Launch button
4.  Hit “Log in with your CyVerse ID” button
5.  Log in with CyVerse credentials (username and password)
6.  Select “Apps”, type in “terra”, select “TERRA REF Rstudio 3.6.0”,
    and “Use App”
7.  Click “Launch Analysis”, hit Analysis button, and select arrow
    button in Analyses window
8.  Wait patiently
9.  Sign into RStudio using username: rstudio and password: rstudio1

Reopen running TERRA REF instance:

1.  Google “Cyverse” and click link to website
2.  Select Products tab
3.  Under “Discovery Environment”, select Launch button
4.  Hit “Log in with your CyVerse ID” button
5.  Log in with CyVerse credentials (username and password)
6.  Open Analyses window and hit arrow button to open up RStudio

Using R language within RStudio. Just like you’d use on your own
computer when you open up RStudio except because this is within VICE,
all the packages we need are already installed.

## Video 2: Downloading Trait Data

Use `traits` R package to download TERRA REF trait data. Package
developed by rOpenSci more generally for downloading trait data.
Implemented for several other trait databases in addition to TERRA REF.
Package is on CRAN.

``` r
library(traits)
```

    ## Registered S3 method overwritten by 'httr':
    ##   method                 from
    ##   as.character.form_file crul

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

First set some global options that will apply across all instances of
using this package’s function. Limits redundant typing.

Specify:

  - Where from internet to download from (NCSA is where TERRA REF data
    is)
  - Want to use most recent version of API for data
  - Using public API key to access data, will show later how to access
    your own API key to get to more data

<!-- end list -->

``` r
options(betydb_url = "https://terraref.ncsa.illinois.edu/bety/", 
        betydb_api_version = 'beta', 
        betydb_key = '9999999999999999999999999999999999999999')
```

Function from `traits` is `betydb_query`. Getting first 1000 rows of
data from fourth season. Can get all by not setting `limit` argument,
but it takes a while.

``` r
season_4 <- betydb_query(sitename = "~Season 4", 
                         limit = 1000)
```

Each row is trait observation. Lots of info, including location (`lat`,
`lon`), species, time, type of `trait`, and value (`mean`).

## Video 3: Available Traits

Look at which traits are available. Using popular data cleaning R
package `dplyr`.

Take dataframe with trait observations, return all the unique values
from trait column with `distinct` and print them all out.

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
season_4 %>% 
  distinct(trait) %>% 
  print(n = Inf)
```

    ## # A tibble: 53 x 1
    ##    trait                        
    ##    <chr>                        
    ##  1 leaf_temperature             
    ##  2 planter_seed_drop            
    ##  3 seedling_emergence_rate      
    ##  4 panicle_height               
    ##  5 stand_count                  
    ##  6 leaf_width                   
    ##  7 leaf_length                  
    ##  8 flag_leaf_emergence_time     
    ##  9 flowering_time               
    ## 10 stalk_diameter_fixed_height  
    ## 11 stalk_diameter_minor_axis    
    ## 12 stalk_diameter_major_axis    
    ## 13 grain_stage_time             
    ## 14 surface_temperature          
    ## 15 canopy_height                
    ## 16 relative_chlorophyll         
    ## 17 absorbance_730               
    ## 18 vH+                          
    ## 19 light_intensity_PAR          
    ## 20 SPAD_880                     
    ## 21 SPAD_850                     
    ## 22 SPAD_650                     
    ## 23 SPAD_420                     
    ## 24 LEF                          
    ## 25 FoPrime                      
    ## 26 FmPrime                      
    ## 27 Phi2                         
    ## 28 leaf_temperature_differential
    ## 29 ECSt                         
    ## 30 gH+                          
    ## 31 FvP/FmP                      
    ## 32 proximal_air_temperature     
    ## 33 pitch                        
    ## 34 leaf_angle_clamp_position    
    ## 35 ambient_humidity             
    ## 36 leaf_thickness               
    ## 37 SPAD_730                     
    ## 38 SPAD_605                     
    ## 39 SPAD_530                     
    ## 40 RFd                          
    ## 41 qP                           
    ## 42 qL                           
    ## 43 NPQt                         
    ## 44 Fs                           
    ## 45 absorbance_940               
    ## 46 absorbance_880               
    ## 47 absorbance_605               
    ## 48 absorbance_530               
    ## 49 PhiNPQ                       
    ## 50 PhiNO                        
    ## 51 roll                         
    ## 52 absorbance_850               
    ## 53 absorbance_650

Focus on one trait, canopy\_height, from more recent season.

``` r
canopy_height <- betydb_query(trait     = "canopy_height", 
                              sitename  = "~Season 6",
                              limit     =  250)
```

## Video 4: Plot Trait Data

Want to plot canopy height across time using `raw_date`. Column is in
wrong format, so first use a function from `lubridate` package to
transform.

Add this as new column using `mutate` from `dplyr`.

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following object is masked from 'package:base':
    ## 
    ##     date

``` r
canopy_height <- canopy_height %>% 
  mutate(formatted_date = ymd_hms(raw_date))
```

Use common R package `ggplot` to plot these data. Specify dataframe,
then what axes should be with `aes` argument. Use scatter plot function.

``` r
library(ggplot2)
ggplot(data = canopy_height, aes(x = formatted_date, y = mean)) +
  geom_point()
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

Add better axis labels. Can find canopy\_height units from `units`.

``` r
ggplot(data = canopy_height, aes(x = formatted_date, y = mean)) +
  geom_point() +
  labs(x = "Date", y = "Plant height (cm)")
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Can do same plot across all the traits in the original dataframe from
fourth season. Do same column time transformation.

``` r
season_4 <- season_4 %>%
  mutate(formatted_date = ymd_hms(raw_date))
```

Create plot same as for canopy\_height, but add in `facet_wrap` to
produce one per unique trait type.

Some data only take at one or a few times of year.

``` r
ggplot(data = season_4, aes(x = formatted_date, y = mean)) +
  geom_point() +
  facet_wrap(~trait, scales = "free_y")
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

## Video 5: Access API Key

Each beta user will have their own unique key. Do not share these with
anyone.

Find the key:

1.  Log into betydb.org
2.  Go to data/users
3.  See your account there with API key listed

Change my key right away on monitor.

Any questions?

## Video 6: Downloading Weather Data

There’s no R package for downloading TERRA REF weather data.

Data is on a platform called Clowder. It’s in format called JSON. Use an
R package that works with JSON data. `fromJSON` is function from that
package that pulls data down and turns into R’s data frame format.

``` r
library(jsonlite)
```

First specify URL where data comes from. Parts are from NCSA Clowder,
using API and geostreams, then weather station with stream\_id and since
and until for date range.

Then read into `fromJSON`. This gets all available weather data for
2017.

``` r
weather_url <- "https://terraref.ncsa.illinois.edu/clowder/api/geostreams/datapoints?stream_id=46431&since=2017-01-02&until=2017-01-31"
weather <- fromJSON(weather_url, flatten = FALSE)
```

Subset of data is called properties, which contains what we want. Pull
that out. Rows are weather observations, and columns are different
weather variables.

``` r
weather_obs <- weather$properties
```

Want to get date, which is in original weather data frame. Take that
column, change into correct format, and add to observations data frame.

``` r
weather_obs <- weather$properties %>% 
  mutate(formatted_date = ymd_hms(weather$end_time))
```

## Video 7: Plot Single Weather Variable

Want to look at air temperature across. Plot these data like we did
before for trait data. Turns out these data are only for month of
January in
2017.

``` r
ggplot(data = weather_obs, aes(x = formatted_date, y = air_temperature)) +
  geom_point() +
  labs(x = "Date", y = "Temperature (K)")
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

## Video 8: Plot All Weather Variables

Want to plot all weather variables across time to see when they are
available and what their values are like.

Need to rearrange the dataframe to do this in ggplot. Data are currently
in wide format, will change into long format. That is, we want to have
one column with the name of the weather variable and another column with
the value for that variable.

First remove a couple of unneeded columns. Then turn variable header
names into a new column `variable` and their values in a new `value`
column. Last, the date column is included.

``` r
library(tidyr)
weather_obs_long <- weather_obs %>% 
  select(-source, -source_file) %>% 
  gather(variable, value, -formatted_date)
```

All observations are plotted across time then. `facet_wrap` is used to
create one plot per weather variable.

``` r
ggplot(data = weather_obs_long, aes(x = formatted_date, y = value)) +
  geom_point() +
  facet_wrap(~variable, scales = "free_y") +
  labs(x = "Date", y = "Weather variable")
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

In addition to temperature, there’s wind speed, rainfall (not much in
Arizona), humidity, and radiation.

Any questions?

## Video 9: Calculate Heat Metric

Now pull down different weather and trait data, combine them, and model
their relationship.

First pull down weather data for the entire year of 2018. Create new
correctly formatted date column like before
too.

``` r
weather_2018_url <- "https://terraref.ncsa.illinois.edu/clowder/api/geostreams/datapoints?stream_id=46431&since=2018-01-01&until=2018-12-31"
weather_2018 <- fromJSON(weather_2018_url, flatten = FALSE)

weather_all <- weather_2018$properties %>% 
  mutate(time = ymd_hms(weather_2018$end_time))
```

We’ll calculate a metric from temperature called growing degree days.
Basically a simple measure of how much heat plants have been exposed to
over time during the year.

First we just want the date, not including time of day. Pull out date
using built-in `as.Date` and put into new column `day`.

Also create new column with air temperature in Celcius instead of
Kelvin.

``` r
weather_daily <- weather_all %>% 
  mutate(day = as.Date(time), 
         air_temp_convert = air_temperature - 273.15)
```

Multiple temperature observations per day. We need to get the lowest and
highest temperature from each day to calculate growing degree days.

First use `group_by` to pull out all rows for each unique day. Then use
`summarise` to do calculations on each subset of data. Use built-in
`min` and `max` to do this to get lowest and highest temp for each day
in their own columns.

``` r
weather_daily <- weather_all %>% 
  mutate(day = as.Date(time), 
         air_temp_convert = air_temperature - 273.15) %>% 
  group_by(day) %>% 
  summarise(min_temp = min(air_temp_convert), 
            max_temp = max(air_temp_convert))
```

The value actually needed for GDD is the mean of lowest and highest
temps, so add that calculation in as a new column.

``` r
weather_daily <- weather_all %>% 
  mutate(day = as.Date(time), 
         air_temp_convert = air_temperature - 273.15) %>% 
  group_by(day) %>% 
  summarise(min_temp = min(air_temp_convert), 
            max_temp = max(air_temp_convert), 
            avg_temp = (max_temp + min_temp) / 2)
```

GDD is measure of the accumulation of heat over time that a plant is
exposed to, but only above a chosen base temperature. The amount of heat
a plant is exposed to affects timing and ability to grow, produce
flowers, ect.

Set a base temp as 10, then combine with average temp to get GDD.

Use an `ifelse` to set that up. If the mean temp is above the base temp,
then take mean temp and subtract base temp of 10 to indicate degrees of
heat they’re exposed to that day. Anything below base temp gets no
degrees.

``` r
base_temp <- 10
gdd_daily <- weather_daily %>% 
  mutate(gdd = ifelse(avg_temp > base_temp, avg_temp - base_temp, 0))
```

This is amount of heat each day they’re exposed to. We want to know
total amount they’ve been exposed to over the year. Use built-in
`cumsum` to add each day’s degrees onto total.

``` r
base_temp <- 10
gdd_daily <- weather_daily %>% 
  mutate(gdd = ifelse(avg_temp > base_temp, avg_temp - base_temp, 0), 
         gdd_cum = cumsum(gdd))
```

## Video 10: Combine Trait and Weather Data

Now that we have temperature-derived metric from trait data, want to
pull down corresponding weather data. Do this like before, except choose
based on data instead of season.

Also only interested in the date and not time, so use same `as.Date`
like from before to get a date column.

``` r
cover <- betydb_query(trait = "canopy_cover", 
                                   date = "~2018", 
                                   limit = "none") #remove/add limit?
```

    ## 

``` r
cover_daily <- cover %>% 
  mutate(day = as.Date(raw_date))
```

Want to combine the growing degree day data frame with this canopy cover
one. We want observations of both for each day.

There’s a bunch of ways to combine two dataframes together. We’re using
a full join here.

Use `full_join`, and also include only necessary columns. Will use
`cultivar` column later.

Every observation from GDD and canopy data frames gets a row, which they
share if they have a common date. Otherwise there’s an NA if there’s not
one of the values for that date. This is because there are days where
either cover or temperature were not observed.

``` r
cover_gdd_daily <- full_join(cover_daily, gdd_daily, by = "day") %>% 
  select(day, cultivar, mean, gdd_cum)
```

Remove all of the rows with an NA in any column.

``` r
cover_gdd_daily <- full_join(cover_daily, gdd_daily, by = "day") %>% 
  select(day, cultivar, mean, gdd_cum) %>% 
  na.omit()
```

## Video 11: Relationship Between Trait and Weather Data

Now that we have all of our data together, we want to model the
relationship between the two variables. This is because increasing
amounts of heat provide energy to plants to grow, which will increase
canopy cover.

First we want to look at data for just one cultivar, as there are
differences between cultivars in this relationship.

``` r
single_cultivar <- cover_gdd_daily %>% 
  filter(cultivar == "PI656026")
```

Before modeling any data, should look at it. With more heat, cover
increases quite quickly before asymptoting.

``` r
ggplot(single_cultivar, aes(x = gdd_cum, y = mean)) +
  geom_point()
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-28-1.png)<!-- -->

Seems like logistic growth model would be a good fit to these data.
Wrote a function to model these data. Copy and paste function and point
out parts.

``` r
model_logistic_growth <- function(data){
  #parameter estimates
  c <- 90
  a <- 0.1
  y <- single_cultivar$mean[3]
  g <- single_cultivar$gdd_cum[3]
  b <- ((log((c/y) - 1)) - a)/g
  #model
  model <- nls(mean ~ c / (1 + exp(a + b * gdd_cum)), 
                             start = list(c = c, a = a, b = b),
                             data = data)
  #model coefficients
  single_c <- coef(model)[1]
  single_a <- coef(model)[2]
  single_b <- coef(model)[3]
  #canopy value predictions
  mean_predict = single_c / (1 + exp(single_a + single_b * data$gdd_cum))
  return(mean_predict)
}
```

Run the logistic model on the dataframe to get out predicted values of
canopy cover for all GDD values. Add this to dataframe.

``` r
cover_predictions <- model_logistic_growth(single_cultivar)
single_cultivar$predictions <- cover_predictions
```

Plot the data like before, and plot model as line using predicted values
to compare. Looks like a good fit.

``` r
ggplot(single_cultivar) +
  geom_point(aes(x = gdd_cum, y = mean)) +
  geom_line(aes(x = gdd_cum, y = predictions), color = "orange") +
  labs(x = "Cumulative growing degree days", y = "Canopy Height")
```

![](first_walkthrough_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

Did this for one cultivar, could go ahead and repeat for each of the
cultivars. The max slope of the model is the maximum growth rate for
each cultivar, could compare these amongst cultivars to see which is
growing best given this GDD.

Any questions?

Thanks for doing pre-walkthrough survey. Will email out post-walkthrough
survey, if everyone can do it. Very helpful feedback to see if these are
helpful.

Entire session was recorded, will be putting these up as short videos on
YouTube soon so everyone can use them. You can also reference notes for
this session and look at the TERRA REF tutorials website.

Will be doing three more of these on various other TERRA REF data,
including image data.
