# utl_last_80_years_of_daily_weather_data_for_BWI_airport_from_noaa
Last 80 years of daily weather data for BWI airport from NOAA.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Last 80 years of daily weather data for BWI airport from NOAA

    github
    https://tinyurl.com/ybrazkan
    https://github.com/rogerjdeangelis/utl_last_80_years_of_daily_weather_data_for_BWI_airport_from_noaa

    StackOverflow R
    https://stackoverflow.com/questions/49383436/rnoaa-r-package-data-access

    Val profile
    https://stackoverflow.com/users/5997555/val

    INPUT
    =====

     You need to google the lat/long of BWI

     SD1.HAVE total obs=1

                  NAME             LATITUDE    LONGITUDE

         BALTIMORE WASH INTL AP     39.1920     -76.6950


    PROCESS (working code)
    ======================

      1. Downlaod and save allmost realime  worlwide NOAA weather data

        Takes me 9 minutes to download and save NOAA climate data

          library(rnoaa);
          station_data <- ghcnd_stations();
          save(station_data,file="d:/rda/station_data.rda", compress = FALSE);

      2.  Find NOAA weather station within 3 km of BWI

          * can handle mutiple lat/lon records;
          have$id <- 1:nrow(have);

          * all stations within 3 km of BWI;
          stations <- lapply(1:nrow(have),
               function(i) meteo_nearby_stations(have[i,],
               lat_colname = "LATITUDE",
               lon_colname = "LONGITUDE",
               radius = 3,station_data = station_data)[[1]]);
          stations_data <- lapply(stations,function(x)  meteo_pull_monitors(x$id[1]));
          mat<-stations_data[[1]];
          want<-mat[,c(1,2,9,15,16)];

          * save data as sas datasets;
          import r=want data=sd1.weather;
          import r=stations data=sd1.stations;


    OUTPUT
    ======

      Dowloaded and save full NOAA station_data into an R list

      save(station_data,file="d:/rda/station_data.rda", compress = FALSE);

      d:/rda/station_data.rda   65,064mb


      SD1.STATIONS total obs=1

             ID                  NAME             LATITUDE    LONGITUDE    DISTANCE(KM)

         USW00093721    BALTIMORE WASH INTL AP     39.1667     -76.6833     2.99316


      SD1.WEATHER total obs=28,764

                         SAS
             ID          DATE    PRCP    TMAX    TMIN

         USW00093721    -7489     102     306     200
         USW00093721    -7488       0     322     189
         USW00093721    -7487       0     283     150
         USW00093721    -7486       0     311     206
         USW00093721    -7485       0     256     211
         USW00093721    -7484      43     300     167
         USW00093721    -7483       0     317     228


      SIMPLE FREQ

         YEAR    Frequency
         ------------------
         1939         184
         1940         366
         1941         365
         1942         365
         1943         365
         ....
         2015         365
         2016         366
         2017         365
         2018          90


    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
      name="BALTIMORE WASH INTL AP";
      input latitude longitude;
    cards4;
    39.192047 -76.69499 2015
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    * save all climate data locally;

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.1";
    libname wrk "%sysfunc(pathname(work))";
    libname hlp "C:\Program_Files\SASHome\SASFoundation\9.4\core\sashelp";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.1/etc/Rprofile.site", echo=T);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    head(have);
    library(rnoaa);
    station_data <- ghcnd_stations();
    save(station_data,file="d:/rda/station_data.rda", compress = FALSE);
    endsubmit;
    run;quit;
    ');


    * create SAS datasets;
    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.1";
    libname wrk "%sysfunc(pathname(work))";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.1/etc/Rprofile.site", echo=T);
    library(haven);
    library(rnoaa);
    have<-read_sas("d:/sd1/have.sas7bdat");
    load(file="d:/rda/station_data.rda");
    have$id <- 1:nrow(have);
    stations <- lapply(1:nrow(have),
         function(i) meteo_nearby_stations(have[i,],
         lat_colname = "LATITUDE",
         lon_colname = "LONGITUDE",
         radius = 3,station_data = station_data)[[1]]);
    str(stations);
    stations_data <- lapply(stations,function(x)  meteo_pull_monitors(x$id[1]));
    str(stations_data);
    mat<-stations_data[[1]];
    want<-mat[,c(1,2,9,15,16)];
    endsubmit;
    import r=want data=sd1.weather;
    import r=stations data=sd1.stations;
    run;quit;
    ');

    > stations <- lapply(1:nrow(have),
    function(i) meteo_nearby_stations(have[i,],
    lat_colname = "LATITUDE",
    lon_colname = "LONGITUDE",
    radius = 3,station_data
    = station_data)[[1]])
    > str(stations)
    > stations_data <- lapply(stations,function(x)  meteo_pull_monitors(x$id[1]))
    > str(stations_data)
    > mat<-stations_data[[1]]
    > want<-mat[,c(1,2,9,15,16)]

    NOTE: Processing of R statements complete

    19        import r=want data=sd1.weather;
    NOTE: Creating data set 'SD1.weather' from R data frame 'want'
    NOTE: Column names modified during import of 'want'
    NOTE: Data set "SD1.weather" has 28764 observation(s) and 5 variable(s)

    20        import r=stations data=sd1.stations;
    NOTE: Creating data set 'SD1.stations' from R data frame 'stations'
    NOTE: Column names modified during import of 'stations'
    NOTE: Data set "SD1.stations" has 1 observation(s) and 5 variable(s)

    21        run;
    NOTE: Procedure r step took :
          real time : 1:56.108
          cpu time  : 0:00.124


    22        quit;

    NOTE: Submitted statements took :
          real time : 1:56.318
          cpu time  : 0:00.280


    * add year and format datae;

    libname sd1 "d:/sd1";
    data stations;
     set sd1.stations;
    run;quit;

    data weather;
      set sd1.weather;
      dte=put(date,date9.);
      year=year(date);
    run;quit;

    proc freq data=weather;
    tables year;
    run;quit;


