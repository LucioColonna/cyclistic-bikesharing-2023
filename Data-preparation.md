Cyclistic 2023 | Data Preparation
================
Lucio Colonna
<br>Last updated: Tuesday September 10, 2024

<br>

[⬅️ **Back to Index**](./README.md)

<br>

## Summary

<br>

<span style="color: #0047AB;">📌 <strong>Data Source</strong></span>

- The dataset is segmented into [12 .csv files](#1dataframes-import),
  with each file corresponding to a specific month in the year 2023
- The [consistency](#2check-consistency-of-dfs) of the dataframes was
  verified, and they were [merged](#3dataframes-merge) into a single
  dataset for further processing

------------------------------------------------------------------------

<span style="color: #0047AB;">📌 <strong>Data Privacy</strong></span>

- The dataset is **anonymized**, meaning it does not contain any
  personally identifiable information
- The only identifier is the **ride ID**, which uniquely identifies each
  ride

------------------------------------------------------------------------

<span style="color: #0047AB;">📌 <strong>Data
Limitations</strong></span>

- The anonymization of the dataset prevents linking pass purchases to
  credit card numbers, making it **impossible to determine** if riders
  live in the Chicago area or if they have purchased multiple rides
- Besides the anonymization point above, the dataset does not provide
  information on the **actual distance travelled** in kilometers. It
  only includes the starting point and ending point, which may lead to
  **approximations** in calculating the distance travelled and the speed

<br>

## 1. Dataframes Import

<br>

[⬅️ **Back to Summary**](#summary)

<br>

Let’s start by importing the dataframes:

``` r
jan23 <- read.csv("Source/202301-divvy-tripdata.csv")
feb23 <- read.csv("Source/202302-divvy-tripdata.csv")
mar23 <- read.csv("Source/202303-divvy-tripdata.csv")
apr23 <- read.csv("Source/202304-divvy-tripdata.csv")
may23 <- read.csv("Source/202305-divvy-tripdata.csv")
jun23 <- read.csv("Source/202306-divvy-tripdata.csv")
jul23 <- read.csv("Source/202307-divvy-tripdata.csv")
aug23 <- read.csv("Source/202308-divvy-tripdata.csv")
sep23 <- read.csv("Source/202309-divvy-tripdata.csv")
oct23 <- read.csv("Source/202310-divvy-tripdata.csv")
nov23 <- read.csv("Source/202311-divvy-tripdata.csv")
dec23 <- read.csv("Source/202312-divvy-tripdata.csv")
```

------------------------------------------------------------------------

Let’s analyze the strucure of the first df `jan23`:

``` r
str(jan23)
```

    ## 'data.frame':    190301 obs. of  13 variables:
    ##  $ ride_id           : chr  "F96D5A74A3E41399" "13CB7EB698CEDB88" "BD88A2E670661CE5" "C90792D034FED968" ...
    ##  $ rideable_type     : chr  "electric_bike" "classic_bike" "electric_bike" "classic_bike" ...
    ##  $ started_at        : chr  "2023-01-21 20:05:42" "2023-01-10 15:37:36" "2023-01-02 07:51:57" "2023-01-22 10:52:58" ...
    ##  $ ended_at          : chr  "2023-01-21 20:16:33" "2023-01-10 15:46:05" "2023-01-02 08:05:11" "2023-01-22 11:01:44" ...
    ##  $ start_station_name: chr  "Lincoln Ave & Fullerton Ave" "Kimbark Ave & 53rd St" "Western Ave & Lunt Ave" "Kimbark Ave & 53rd St" ...
    ##  $ start_station_id  : chr  "TA1309000058" "TA1309000037" "RP-005" "TA1309000037" ...
    ##  $ end_station_name  : chr  "Hampden Ct & Diversey Ave" "Greenwood Ave & 47th St" "Valli Produce - Evanston Plaza" "Greenwood Ave & 47th St" ...
    ##  $ end_station_id    : chr  "202480.0" "TA1308000002" "599" "TA1308000002" ...
    ##  $ start_lat         : num  41.9 41.8 42 41.8 41.8 ...
    ##  $ start_lng         : num  -87.6 -87.6 -87.7 -87.6 -87.6 ...
    ##  $ end_lat           : num  41.9 41.8 42 41.8 41.8 ...
    ##  $ end_lng           : num  -87.6 -87.6 -87.7 -87.6 -87.6 ...
    ##  $ member_casual     : chr  "member" "member" "casual" "member" ...

<br>

## 2. Check consistency of DFs

<br>

[⬅️ **Back to Summary**](#summary)

<br>

Our primary task is to merge all datasets into a single dataframe for
further analysis.

However, before proceeding, it’s crucial to **ensure uniformity** across
all datasets. This means verifying that each dataset has: 1. the same
**number of columns** 2. identical **column names** 3. identical **data
types**

------------------------------------------------------------------------

What I want to achieve is a comprehensive comparison between **all pairs
of dataframes**. To do that, I have created a function called
`compare_dataframes` that takes a vector of dataframe names as an
argument. - I’m creating a vector `df_names` containing the names of the
12 dataframes, each corresponding to a month. Then, I’m using `get()` to
fetch these dataframes in combination with `lapply()` and store them in
a list called `df_list`

- Next, I initialize an empty list called `results` to store the
  comparison results

- I’m running nested loops to compare each dataframe with every other
  dataframe

- Within these loops, I’m evaluating three conditions: if they have the
  same number of columns (`same_ncol`), if all columns have the same
  data type (`same_class`), and if they have the same column names
  (`same_names`)

- For each comparison, I’m creating a 1x4 tibble containing the names of
  the dataframes being compared (`comparison`), along with the results
  of the three conditions. This tibble is then appended to the `results`
  list

- After all comparisons are done, I’m using `bind_rows()` to merge all
  the tibbles into a new tibble named `comparison_results`

- Finally, I’m returning out the `comparison_results` tibble

``` r
compare_dataframes <- function(df_names) {

    # get DF names
    df_list <- lapply(df_names, get)

    # initialize empty list
    results <- list()

    # Selection of DF pairs to be compared
    for (i in 1:(length(df_list) - 1)) {
        for (j in (i + 1):length(df_list)) {

            # check column numbers
            same_ncol <- suppressWarnings(ncol(df_list[[i]]) ==
                ncol(df_list[[j]]))

            # check data types
            same_class <- suppressWarnings(all(sapply(df_list[[i]],
                class) == sapply(df_list[[j]], class)))

            # check column names
            same_names <- suppressWarnings(all(names(df_list[[i]]) ==
                names(df_list[[j]])))

            # append 1x4 tibbles to results list
            results[[length(results) + 1]] <- tibble(comparison = paste(df_names[i],
                "vs", df_names[j]), same_ncol = same_ncol, same_class = same_class,
                same_names = same_names)
        }
    }
    # merge tibbles
    comparison_result <- bind_rows(results)
    return(comparison_result)
}

df_names <- c("jan23", "feb23", "mar23", "apr23", "may23", "jun23",
    "jul23", "aug23", "sep23", "oct23", "nov23", "dec23")

comparison_result <- compare_dataframes(df_names)

print(comparison_result, n = Inf)
```

    ## # A tibble: 66 × 4
    ##    comparison     same_ncol same_class same_names
    ##    <chr>          <lgl>     <lgl>      <lgl>     
    ##  1 jan23 vs feb23 TRUE      TRUE       TRUE      
    ##  2 jan23 vs mar23 TRUE      TRUE       TRUE      
    ##  3 jan23 vs apr23 TRUE      TRUE       TRUE      
    ##  4 jan23 vs may23 TRUE      TRUE       TRUE      
    ##  5 jan23 vs jun23 TRUE      TRUE       TRUE      
    ##  6 jan23 vs jul23 TRUE      TRUE       TRUE      
    ##  7 jan23 vs aug23 TRUE      TRUE       TRUE      
    ##  8 jan23 vs sep23 TRUE      TRUE       TRUE      
    ##  9 jan23 vs oct23 TRUE      TRUE       TRUE      
    ## 10 jan23 vs nov23 TRUE      TRUE       TRUE      
    ## 11 jan23 vs dec23 TRUE      TRUE       TRUE      
    ## 12 feb23 vs mar23 TRUE      TRUE       TRUE      
    ## 13 feb23 vs apr23 TRUE      TRUE       TRUE      
    ## 14 feb23 vs may23 TRUE      TRUE       TRUE      
    ## 15 feb23 vs jun23 TRUE      TRUE       TRUE      
    ## 16 feb23 vs jul23 TRUE      TRUE       TRUE      
    ## 17 feb23 vs aug23 TRUE      TRUE       TRUE      
    ## 18 feb23 vs sep23 TRUE      TRUE       TRUE      
    ## 19 feb23 vs oct23 TRUE      TRUE       TRUE      
    ## 20 feb23 vs nov23 TRUE      TRUE       TRUE      
    ## 21 feb23 vs dec23 TRUE      TRUE       TRUE      
    ## 22 mar23 vs apr23 TRUE      TRUE       TRUE      
    ## 23 mar23 vs may23 TRUE      TRUE       TRUE      
    ## 24 mar23 vs jun23 TRUE      TRUE       TRUE      
    ## 25 mar23 vs jul23 TRUE      TRUE       TRUE      
    ## 26 mar23 vs aug23 TRUE      TRUE       TRUE      
    ## 27 mar23 vs sep23 TRUE      TRUE       TRUE      
    ## 28 mar23 vs oct23 TRUE      TRUE       TRUE      
    ## 29 mar23 vs nov23 TRUE      TRUE       TRUE      
    ## 30 mar23 vs dec23 TRUE      TRUE       TRUE      
    ## 31 apr23 vs may23 TRUE      TRUE       TRUE      
    ## 32 apr23 vs jun23 TRUE      TRUE       TRUE      
    ## 33 apr23 vs jul23 TRUE      TRUE       TRUE      
    ## 34 apr23 vs aug23 TRUE      TRUE       TRUE      
    ## 35 apr23 vs sep23 TRUE      TRUE       TRUE      
    ## 36 apr23 vs oct23 TRUE      TRUE       TRUE      
    ## 37 apr23 vs nov23 TRUE      TRUE       TRUE      
    ## 38 apr23 vs dec23 TRUE      TRUE       TRUE      
    ## 39 may23 vs jun23 TRUE      TRUE       TRUE      
    ## 40 may23 vs jul23 TRUE      TRUE       TRUE      
    ## 41 may23 vs aug23 TRUE      TRUE       TRUE      
    ## 42 may23 vs sep23 TRUE      TRUE       TRUE      
    ## 43 may23 vs oct23 TRUE      TRUE       TRUE      
    ## 44 may23 vs nov23 TRUE      TRUE       TRUE      
    ## 45 may23 vs dec23 TRUE      TRUE       TRUE      
    ## 46 jun23 vs jul23 TRUE      TRUE       TRUE      
    ## 47 jun23 vs aug23 TRUE      TRUE       TRUE      
    ## 48 jun23 vs sep23 TRUE      TRUE       TRUE      
    ## 49 jun23 vs oct23 TRUE      TRUE       TRUE      
    ## 50 jun23 vs nov23 TRUE      TRUE       TRUE      
    ## 51 jun23 vs dec23 TRUE      TRUE       TRUE      
    ## 52 jul23 vs aug23 TRUE      TRUE       TRUE      
    ## 53 jul23 vs sep23 TRUE      TRUE       TRUE      
    ## 54 jul23 vs oct23 TRUE      TRUE       TRUE      
    ## 55 jul23 vs nov23 TRUE      TRUE       TRUE      
    ## 56 jul23 vs dec23 TRUE      TRUE       TRUE      
    ## 57 aug23 vs sep23 TRUE      TRUE       TRUE      
    ## 58 aug23 vs oct23 TRUE      TRUE       TRUE      
    ## 59 aug23 vs nov23 TRUE      TRUE       TRUE      
    ## 60 aug23 vs dec23 TRUE      TRUE       TRUE      
    ## 61 sep23 vs oct23 TRUE      TRUE       TRUE      
    ## 62 sep23 vs nov23 TRUE      TRUE       TRUE      
    ## 63 sep23 vs dec23 TRUE      TRUE       TRUE      
    ## 64 oct23 vs nov23 TRUE      TRUE       TRUE      
    ## 65 oct23 vs dec23 TRUE      TRUE       TRUE      
    ## 66 nov23 vs dec23 TRUE      TRUE       TRUE

------------------------------------------------------------------------

``` r
all(comparison_result$same_ncol & comparison_result$same_class &
    comparison_result$same_names)
```

    ## [1] TRUE

All comparisons turned out to be `TRUE`! It means that all datframes
match, so we are clear to proceed with the merging.

<br>

## 3. Dataframes Merge

<br>

[⬅️ **Back to Summary**](#summary)

<br>

Now we can merge all the dataframes using the `rbind()` function:

``` r
trips_2023_merged <- rbind(jan23, feb23, mar23, apr23, may23,
    jun23, jul23, aug23, sep23, oct23, nov23, dec23)

glimpse(trips_2023_merged)
```

    ## Rows: 5,719,877
    ## Columns: 13
    ## $ ride_id            <chr> "F96D5A74A3E41399", "13CB7EB698CEDB88", "BD88A2E670…
    ## $ rideable_type      <chr> "electric_bike", "classic_bike", "electric_bike", "…
    ## $ started_at         <chr> "2023-01-21 20:05:42", "2023-01-10 15:37:36", "2023…
    ## $ ended_at           <chr> "2023-01-21 20:16:33", "2023-01-10 15:46:05", "2023…
    ## $ start_station_name <chr> "Lincoln Ave & Fullerton Ave", "Kimbark Ave & 53rd …
    ## $ start_station_id   <chr> "TA1309000058", "TA1309000037", "RP-005", "TA130900…
    ## $ end_station_name   <chr> "Hampden Ct & Diversey Ave", "Greenwood Ave & 47th …
    ## $ end_station_id     <chr> "202480.0", "TA1308000002", "599", "TA1308000002", …
    ## $ start_lat          <dbl> 41.92407, 41.79957, 42.00857, 41.79957, 41.79957, 4…
    ## $ start_lng          <dbl> -87.64628, -87.59475, -87.69048, -87.59475, -87.594…
    ## $ end_lat            <dbl> 41.93000, 41.80983, 42.03974, 41.80983, 41.80983, 4…
    ## $ end_lng            <dbl> -87.64000, -87.59938, -87.69941, -87.59938, -87.599…
    ## $ member_casual      <chr> "member", "member", "casual", "member", "member", "…

------------------------------------------------------------------------

To improve readibility, I will assign `trips_ 2023_merged` to a new
variable named `df`.

Additionally, to streamline memory usage, I will remove
`trips_2023_merged` and the individual monthly dataframes (`jan23`,
`feb23`, …, `dec23`).

``` r
df <- trips_2023_merged

rm(trips_2023_merged, jan23, feb23, mar23, apr23, may23, jun23,
    jul23, aug23, sep23, oct23, nov23, dec23)
```
