## Introduction and background

![](images/paste-E79330DA.png)

-   For this case study, we are tasked to assist the marketing team at
    Bellabeat, a high-tech manufacturer of health-focused products for
    women.
-   Urška Sršen, cofounder and Chief Creative Officer of Bellabeat,
    believes that analyzing smart device fitness data could help unlock
    new growth opportunities for the company.
-   We have been asked to focus on one of Bellabeat’s products and
    analyze smart device data to gain insight into how consumers are
    using their smart devices.
-   The insights we discover will then help guide marketing strategy
    for the company.
-   We will present our analysis to the Bellabeat executive team along
    with our high-level recommendations for Bellabeat’s marketing
    strategy.

## Phase 1 - Ask

**Business Task** - Identify trends in usage of smart devices to make
recommendations to marketing team for growth opportunities.  
**Key Stakeholders** - Urška Sršen, Sando Mur and Bellabeat marketing
analytics team

## Phase 2 - Prepare

-   The data is stored in public domain, hosted at Kaggle by Mobius.
    [(Source)](https://www.kaggle.com/datasets/arashnic/fitbit)
-   **ROCCC Analysis** -
    -   Reliability - Data is crowdsourced. Acceptable.
    -   Original - The data is collected from survey data via Amazon
        Mechanical Turk. Acceptable.
    -   Comprehensive - The data comprises activity log ranging from
        seconds to days, but only for a period of 31 days. Okay.
    -   Current - Data is outdated, as it was collected between
        04/12/2016 - 05/12/2016. Bad.
    -   Cited - No citations available. Bad.
-   **Integrity** - Data is collected through a survey. Unable to
    ascertain the integrity of data.
-   **Licensing** - Data is publicly available, crowdsourced.
-   **Privacy & Security** - There are no personal identifiers in the
    data. The data is tagged with ‘id’.
-   **Help answer your question?** - The dataset contains data for
    personal habits relating to steps, calories, heart rate, sleep and
    weight. It can be used to explore trends.
-   **Any problems with data?** - The sample size of 30 users does not
    represent the entire smart device users’ population. Furthermore,
    the dataset is 6 years old and the trends will have most likely have
    evolved over the years.
-   **Dataset organization** -
    -   There are 18 files in the dataset, containing data for 33 Fitbit
        users.
    -   Minute data x8
        -   Steps (long & wide), calories (long & wide), intensity (long
            & wide). **~ maybe too specific for marketing-based
            insights**
    -   Hourly data x3
        -   Calories, Intensities, Steps (long) **~ could be useful to
            understand what time of the day users were most active**
    -   Daily data x4
        -   Daily activity (long) - comprising of Steps + calories and
            intensity
        -   Calories, intensity, Steps (long)
    -   Heartrate (by seconds) data x1 **~ maybe too specific for
        marketing-based insights**
    -   Sleep (by day) x1
    -   Weight log (by day) x1

## Phase 3 - Process

### Setting up environment

    library(tidyverse)

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.4.0      ✔ purrr   0.3.5 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.3      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

    library(janitor)

    ## 
    ## Attaching package: 'janitor'
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

### Importing CSV files

    setwd("D:/RStudio/Bellabeat")
    daily_activity <- read_csv("dailyActivity_merged.csv")

    ## Rows: 940 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): ActivityDate
    ## dbl (14): Id, TotalSteps, TotalDistance, TrackerDistance, LoggedActivitiesDi...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    daily_calories <- read_csv("dailyCalories_merged.csv")

    ## Rows: 940 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityDay
    ## dbl (2): Id, Calories
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    daily_intensities <- read_csv("dailyIntensities_merged.csv")

    ## Rows: 940 Columns: 10
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityDay
    ## dbl (9): Id, SedentaryMinutes, LightlyActiveMinutes, FairlyActiveMinutes, Ve...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    daily_steps <- read_csv("dailySteps_merged.csv")

    ## Rows: 940 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityDay
    ## dbl (2): Id, StepTotal
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    glimpse(daily_activity)

    ## Rows: 940
    ## Columns: 15
    ## $ Id                       <dbl> 1503960366, 1503960366, 1503960366, 150396036…
    ## $ ActivityDate             <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/…
    ## $ TotalSteps               <dbl> 13162, 10735, 10460, 9762, 12669, 9705, 13019…
    ## $ TotalDistance            <dbl> 8.50, 6.97, 6.74, 6.28, 8.16, 6.48, 8.59, 9.8…
    ## $ TrackerDistance          <dbl> 8.50, 6.97, 6.74, 6.28, 8.16, 6.48, 8.59, 9.8…
    ## $ LoggedActivitiesDistance <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …
    ## $ VeryActiveDistance       <dbl> 1.88, 1.57, 2.44, 2.14, 2.71, 3.19, 3.25, 3.5…
    ## $ ModeratelyActiveDistance <dbl> 0.55, 0.69, 0.40, 1.26, 0.41, 0.78, 0.64, 1.3…
    ## $ LightActiveDistance      <dbl> 6.06, 4.71, 3.91, 2.83, 5.04, 2.51, 4.71, 5.0…
    ## $ SedentaryActiveDistance  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …
    ## $ VeryActiveMinutes        <dbl> 25, 21, 30, 29, 36, 38, 42, 50, 28, 19, 66, 4…
    ## $ FairlyActiveMinutes      <dbl> 13, 19, 11, 34, 10, 20, 16, 31, 12, 8, 27, 21…
    ## $ LightlyActiveMinutes     <dbl> 328, 217, 181, 209, 221, 164, 233, 264, 205, …
    ## $ SedentaryMinutes         <dbl> 728, 776, 1218, 726, 773, 539, 1149, 775, 818…
    ## $ Calories                 <dbl> 1985, 1797, 1776, 1745, 1863, 1728, 1921, 203…

    glimpse(daily_calories)

    ## Rows: 940
    ## Columns: 3
    ## $ Id          <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 1503960366…
    ## $ ActivityDay <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/2016", "4/16/…
    ## $ Calories    <dbl> 1985, 1797, 1776, 1745, 1863, 1728, 1921, 2035, 1786, 1775…

    glimpse(daily_intensities)

    ## Rows: 940
    ## Columns: 10
    ## $ Id                       <dbl> 1503960366, 1503960366, 1503960366, 150396036…
    ## $ ActivityDay              <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/…
    ## $ SedentaryMinutes         <dbl> 728, 776, 1218, 726, 773, 539, 1149, 775, 818…
    ## $ LightlyActiveMinutes     <dbl> 328, 217, 181, 209, 221, 164, 233, 264, 205, …
    ## $ FairlyActiveMinutes      <dbl> 13, 19, 11, 34, 10, 20, 16, 31, 12, 8, 27, 21…
    ## $ VeryActiveMinutes        <dbl> 25, 21, 30, 29, 36, 38, 42, 50, 28, 19, 66, 4…
    ## $ SedentaryActiveDistance  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …
    ## $ LightActiveDistance      <dbl> 6.06, 4.71, 3.91, 2.83, 5.04, 2.51, 4.71, 5.0…
    ## $ ModeratelyActiveDistance <dbl> 0.55, 0.69, 0.40, 1.26, 0.41, 0.78, 0.64, 1.3…
    ## $ VeryActiveDistance       <dbl> 1.88, 1.57, 2.44, 2.14, 2.71, 3.19, 3.25, 3.5…

    glimpse(daily_steps)

    ## Rows: 940
    ## Columns: 3
    ## $ Id          <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 1503960366…
    ## $ ActivityDay <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/2016", "4/16/…
    ## $ StepTotal   <dbl> 13162, 10735, 10460, 9762, 12669, 9705, 13019, 15506, 1054…

-   At first glance, it seems daily\_activity is a consolidated
    dataframe of daily\_calories, daily\_intensities and daily\_steps.
    Let us verify.

<!-- -->

    dailyact_calories <- daily_activity %>% 
      rename(ActivityDay = ActivityDate) %>% 
       select(Id, ActivityDay, Calories)

    data_common1 <- inner_join(dailyact_calories, daily_calories)

    ## Joining, by = c("Id", "ActivityDay", "Calories")

    glimpse(data_common1)

    ## Rows: 940
    ## Columns: 3
    ## $ Id          <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 1503960366…
    ## $ ActivityDay <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/2016", "4/16/…
    ## $ Calories    <dbl> 1985, 1797, 1776, 1745, 1863, 1728, 1921, 2035, 1786, 1775…

    dailyact_intensities <- daily_activity %>% 
        rename(ActivityDay = ActivityDate) %>% 
        select(Id, ActivityDay, SedentaryMinutes, LightlyActiveMinutes, FairlyActiveMinutes, VeryActiveMinutes, SedentaryActiveDistance, LightActiveDistance, ModeratelyActiveDistance, VeryActiveDistance)
    data_common2 <- inner_join(dailyact_intensities, daily_intensities)

    ## Joining, by = c("Id", "ActivityDay", "SedentaryMinutes",
    ## "LightlyActiveMinutes", "FairlyActiveMinutes", "VeryActiveMinutes",
    ## "SedentaryActiveDistance", "LightActiveDistance", "ModeratelyActiveDistance",
    ## "VeryActiveDistance")

    glimpse(data_common2)

    ## Rows: 940
    ## Columns: 10
    ## $ Id                       <dbl> 1503960366, 1503960366, 1503960366, 150396036…
    ## $ ActivityDay              <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/…
    ## $ SedentaryMinutes         <dbl> 728, 776, 1218, 726, 773, 539, 1149, 775, 818…
    ## $ LightlyActiveMinutes     <dbl> 328, 217, 181, 209, 221, 164, 233, 264, 205, …
    ## $ FairlyActiveMinutes      <dbl> 13, 19, 11, 34, 10, 20, 16, 31, 12, 8, 27, 21…
    ## $ VeryActiveMinutes        <dbl> 25, 21, 30, 29, 36, 38, 42, 50, 28, 19, 66, 4…
    ## $ SedentaryActiveDistance  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, …
    ## $ LightActiveDistance      <dbl> 6.06, 4.71, 3.91, 2.83, 5.04, 2.51, 4.71, 5.0…
    ## $ ModeratelyActiveDistance <dbl> 0.55, 0.69, 0.40, 1.26, 0.41, 0.78, 0.64, 1.3…
    ## $ VeryActiveDistance       <dbl> 1.88, 1.57, 2.44, 2.14, 2.71, 3.19, 3.25, 3.5…

    dailyact_steps <- daily_activity %>% 
          rename(ActivityDay = ActivityDate) %>% 
      select(Id, ActivityDay, TotalSteps)
    data_common3 <- inner_join(dailyact_steps, daily_steps)

    ## Joining, by = c("Id", "ActivityDay")

    glimpse(data_common3)

    ## Rows: 940
    ## Columns: 4
    ## $ Id          <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 1503960366…
    ## $ ActivityDay <chr> "4/12/2016", "4/13/2016", "4/14/2016", "4/15/2016", "4/16/…
    ## $ TotalSteps  <dbl> 13162, 10735, 10460, 9762, 12669, 9705, 13019, 15506, 1054…
    ## $ StepTotal   <dbl> 13162, 10735, 10460, 9762, 12669, 9705, 13019, 15506, 1054…

-   It can be safely concluded that daily\_activity is a consolidated
    dataframe, comprising data from daily\_calories, daily\_intensities
    and daily\_steps dataframes. We can remove the latter 3 dataframes.

<!-- -->

    # Importing other dataframes
    hourly_calories <- read_csv("hourlyCalories_merged.csv")

    ## Rows: 22099 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (2): Id, Calories
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    hourly_intensities <- read_csv("hourlyIntensities_merged.csv")

    ## Rows: 22099 Columns: 4
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (3): Id, TotalIntensity, AverageIntensity
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    hourly_steps <- read_csv("hourlySteps_merged.csv")

    ## Rows: 22099 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): ActivityHour
    ## dbl (2): Id, StepTotal
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    sleep_day <- read_csv("sleepDay_merged.csv")

    ## Rows: 413 Columns: 5
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): SleepDay
    ## dbl (4): Id, TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    weight_log <- read_csv("weightLogInfo_merged.csv")

    ## Rows: 67 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Date
    ## dbl (6): Id, WeightKg, WeightPounds, Fat, BMI, LogId
    ## lgl (1): IsManualReport
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    glimpse(hourly_calories)

    ## Rows: 22,099
    ## Columns: 3
    ## $ Id           <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 150396036…
    ## $ ActivityHour <chr> "4/12/2016 12:00:00 AM", "4/12/2016 1:00:00 AM", "4/12/20…
    ## $ Calories     <dbl> 81, 61, 59, 47, 48, 48, 48, 47, 68, 141, 99, 76, 73, 66, …

    glimpse(hourly_intensities)

    ## Rows: 22,099
    ## Columns: 4
    ## $ Id               <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 15039…
    ## $ ActivityHour     <chr> "4/12/2016 12:00:00 AM", "4/12/2016 1:00:00 AM", "4/1…
    ## $ TotalIntensity   <dbl> 20, 8, 7, 0, 0, 0, 0, 0, 13, 30, 29, 12, 11, 6, 36, 5…
    ## $ AverageIntensity <dbl> 0.333333, 0.133333, 0.116667, 0.000000, 0.000000, 0.0…

    glimpse(hourly_steps)

    ## Rows: 22,099
    ## Columns: 3
    ## $ Id           <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 150396036…
    ## $ ActivityHour <chr> "4/12/2016 12:00:00 AM", "4/12/2016 1:00:00 AM", "4/12/20…
    ## $ StepTotal    <dbl> 373, 160, 151, 0, 0, 0, 0, 0, 250, 1864, 676, 360, 253, 2…

    glimpse(sleep_day)

    ## Rows: 413
    ## Columns: 5
    ## $ Id                 <dbl> 1503960366, 1503960366, 1503960366, 1503960366, 150…
    ## $ SleepDay           <chr> "4/12/2016 12:00:00 AM", "4/13/2016 12:00:00 AM", "…
    ## $ TotalSleepRecords  <dbl> 1, 2, 1, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, …
    ## $ TotalMinutesAsleep <dbl> 327, 384, 412, 340, 700, 304, 360, 325, 361, 430, 2…
    ## $ TotalTimeInBed     <dbl> 346, 407, 442, 367, 712, 320, 377, 364, 384, 449, 3…

    glimpse(weight_log)

    ## Rows: 67
    ## Columns: 8
    ## $ Id             <dbl> 1503960366, 1503960366, 1927972279, 2873212765, 2873212…
    ## $ Date           <chr> "5/2/2016 11:59:59 PM", "5/3/2016 11:59:59 PM", "4/13/2…
    ## $ WeightKg       <dbl> 52.6, 52.6, 133.5, 56.7, 57.3, 72.4, 72.3, 69.7, 70.3, …
    ## $ WeightPounds   <dbl> 115.9631, 115.9631, 294.3171, 125.0021, 126.3249, 159.6…
    ## $ Fat            <dbl> 22, NA, NA, NA, NA, 25, NA, NA, NA, NA, NA, NA, NA, NA,…
    ## $ BMI            <dbl> 22.65, 22.65, 47.54, 21.45, 21.69, 27.45, 27.38, 27.25,…
    ## $ IsManualReport <lgl> TRUE, TRUE, FALSE, TRUE, TRUE, TRUE, TRUE, TRUE, TRUE, …
    ## $ LogId          <dbl> 1.462234e+12, 1.462320e+12, 1.460510e+12, 1.461283e+12,…

### Cleaning Data

#### Check for Null values

    sum(is.na(daily_activity))

    ## [1] 0

    sum(is.na(hourly_calories))

    ## [1] 0

    sum(is.na(hourly_intensities))

    ## [1] 0

    sum(is.na(hourly_steps))

    ## [1] 0

    sum(is.na(sleep_day))

    ## [1] 0

    sum(is.na(weight_log))

    ## [1] 65

-   The ‘Fat’ column in weight\_log has a lot of Null values.

#### Check for duplicate values

    sum(duplicated(daily_activity))

    ## [1] 0

    sum(duplicated(hourly_calories))

    ## [1] 0

    sum(duplicated(hourly_intensities))

    ## [1] 0

    sum(duplicated(hourly_steps))

    ## [1] 0

    sum(duplicated(sleep_day))

    ## [1] 3

    sum(duplicated(weight_log))

    ## [1] 0

    #Remove duplicate values from sleep_day dataframe
    sleep_day <- sleep_day %>% 
      distinct()

#### Standardizing column names

    #Cleaning dataframe column names

    daily_activity <- daily_activity %>% 
      clean_names() %>% 
      rename(date = activity_date)

    hourly_calories <- hourly_calories %>% 
      clean_names() %>% 
      rename(date = activity_hour)

    hourly_intensities <- hourly_intensities %>% 
      clean_names() %>% 
      rename(date = activity_hour)

    hourly_steps <- hourly_steps %>% 
      clean_names() %>% 
      rename(date = activity_hour)

    sleep_day <- sleep_day %>% 
      clean_names() %>% 
      rename(date = sleep_day)

    weight_log <- clean_names(weight_log)

#### Fixing datatypes of columns

    # Changing datatype of date columns

    daily_activity <- daily_activity %>% 
      mutate(date = as.Date(date, "%m/%d/%Y"))

    ## hourly_calories dataframe
    #format date column from char to date-time
    hourly_calories$date = as.POSIXct(hourly_calories$date, format = "%m/%d/%Y %I:%M:%S %p")

    #create new column named 'time' extracting time from date column
    hourly_calories$time <- format(hourly_calories$date, format = "%H:%M:%S")


    ## hourly_intensities dataframe
    #format date column from char to date-time
    hourly_intensities$date = as.POSIXct(hourly_intensities$date, format = "%m/%d/%Y %I:%M:%S %p")

    #create new column named 'time' extracting time from date column
    hourly_intensities$time <- format(hourly_intensities$date, format = "%H:%M:%S")


    ## hourly_steps dataframe
    #format date column from char to date-time
    hourly_steps$date = as.POSIXct(hourly_steps$date, format = "%m/%d/%Y %I:%M:%S %p")

    #create new column named 'time' extracting time from date column
    hourly_steps$time <- format(hourly_steps$date, format = "%H:%M:%S")


    ## sleep_day dataframe
    sleep_day <- sleep_day %>% 
      mutate(date = as.Date(date, format="%m/%d/%Y"))


    ## weight_log dataframe
    weight_log <- weight_log %>% 
      mutate(date = as.Date(date, format="%m/%d/%Y"))

#### Final check for dataframes

    head(daily_activity)

    ## # A tibble: 6 × 15
    ##           id date       total_…¹ total…² track…³ logge…⁴ very_…⁵ moder…⁶ light…⁷
    ##        <dbl> <date>        <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ## 1 1503960366 2016-04-12    13162    8.5     8.5        0    1.88   0.550    6.06
    ## 2 1503960366 2016-04-13    10735    6.97    6.97       0    1.57   0.690    4.71
    ## 3 1503960366 2016-04-14    10460    6.74    6.74       0    2.44   0.400    3.91
    ## 4 1503960366 2016-04-15     9762    6.28    6.28       0    2.14   1.26     2.83
    ## 5 1503960366 2016-04-16    12669    8.16    8.16       0    2.71   0.410    5.04
    ## 6 1503960366 2016-04-17     9705    6.48    6.48       0    3.19   0.780    2.51
    ## # … with 6 more variables: sedentary_active_distance <dbl>,
    ## #   very_active_minutes <dbl>, fairly_active_minutes <dbl>,
    ## #   lightly_active_minutes <dbl>, sedentary_minutes <dbl>, calories <dbl>, and
    ## #   abbreviated variable names ¹​total_steps, ²​total_distance,
    ## #   ³​tracker_distance, ⁴​logged_activities_distance, ⁵​very_active_distance,
    ## #   ⁶​moderately_active_distance, ⁷​light_active_distance

    head(hourly_calories)

    ## # A tibble: 6 × 4
    ##           id date                calories time    
    ##        <dbl> <dttm>                 <dbl> <chr>   
    ## 1 1503960366 2016-04-12 00:00:00       81 00:00:00
    ## 2 1503960366 2016-04-12 01:00:00       61 01:00:00
    ## 3 1503960366 2016-04-12 02:00:00       59 02:00:00
    ## 4 1503960366 2016-04-12 03:00:00       47 03:00:00
    ## 5 1503960366 2016-04-12 04:00:00       48 04:00:00
    ## 6 1503960366 2016-04-12 05:00:00       48 05:00:00

    head(hourly_intensities)

    ## # A tibble: 6 × 5
    ##           id date                total_intensity average_intensity time    
    ##        <dbl> <dttm>                        <dbl>             <dbl> <chr>   
    ## 1 1503960366 2016-04-12 00:00:00              20             0.333 00:00:00
    ## 2 1503960366 2016-04-12 01:00:00               8             0.133 01:00:00
    ## 3 1503960366 2016-04-12 02:00:00               7             0.117 02:00:00
    ## 4 1503960366 2016-04-12 03:00:00               0             0     03:00:00
    ## 5 1503960366 2016-04-12 04:00:00               0             0     04:00:00
    ## 6 1503960366 2016-04-12 05:00:00               0             0     05:00:00

    head(hourly_steps)

    ## # A tibble: 6 × 4
    ##           id date                step_total time    
    ##        <dbl> <dttm>                   <dbl> <chr>   
    ## 1 1503960366 2016-04-12 00:00:00        373 00:00:00
    ## 2 1503960366 2016-04-12 01:00:00        160 01:00:00
    ## 3 1503960366 2016-04-12 02:00:00        151 02:00:00
    ## 4 1503960366 2016-04-12 03:00:00          0 03:00:00
    ## 5 1503960366 2016-04-12 04:00:00          0 04:00:00
    ## 6 1503960366 2016-04-12 05:00:00          0 05:00:00

    head(sleep_day)

    ## # A tibble: 6 × 5
    ##           id date       total_sleep_records total_minutes_asleep total_time_in…¹
    ##        <dbl> <date>                   <dbl>                <dbl>           <dbl>
    ## 1 1503960366 2016-04-12                   1                  327             346
    ## 2 1503960366 2016-04-13                   2                  384             407
    ## 3 1503960366 2016-04-15                   1                  412             442
    ## 4 1503960366 2016-04-16                   2                  340             367
    ## 5 1503960366 2016-04-17                   1                  700             712
    ## 6 1503960366 2016-04-19                   1                  304             320
    ## # … with abbreviated variable name ¹​total_time_in_bed

    head(weight_log)

    ## # A tibble: 6 × 8
    ##           id date       weight_kg weight_pounds   fat   bmi is_manual_…¹  log_id
    ##        <dbl> <date>         <dbl>         <dbl> <dbl> <dbl> <lgl>          <dbl>
    ## 1 1503960366 2016-05-02      52.6          116.    22  22.6 TRUE         1.46e12
    ## 2 1503960366 2016-05-03      52.6          116.    NA  22.6 TRUE         1.46e12
    ## 3 1927972279 2016-04-13     134.           294.    NA  47.5 FALSE        1.46e12
    ## 4 2873212765 2016-04-21      56.7          125.    NA  21.5 TRUE         1.46e12
    ## 5 2873212765 2016-05-12      57.3          126.    NA  21.7 TRUE         1.46e12
    ## 6 4319703577 2016-04-17      72.4          160.    25  27.5 TRUE         1.46e12
    ## # … with abbreviated variable name ¹​is_manual_report

### Guiding Questions

**What tools are you choosing and why?**  
I’m choosing to work with R, as it is accessible to import, clean,
process, analyze and visualize dataframes.  
**What steps have you taken to ensure that your data is clean?**  
I have checked for null data, duplicate entries, formatted column names
and fixed datatypes for date columns.  
**How can you verify that your data is clean and ready to analyze?**  
I have double-checked the data post cleaning.  
**Have you documented your cleaning process so you can review and share
those results?**  
I have documented the cleaning process for review.

## Phase 4 - Analyze

### Quick Summary Statistics

    # Analyzing Participants 
    n_distinct(daily_activity$id)

    ## [1] 33

    n_distinct(hourly_calories$id)

    ## [1] 33

    n_distinct(hourly_intensities$id)

    ## [1] 33

    n_distinct(hourly_steps$id)

    ## [1] 33

    n_distinct(sleep_day$id)

    ## [1] 24

    n_distinct(weight_log$id)

    ## [1] 8

-   sleep\_day dataframe has data for 24 and weight\_log has data for 8
    participants, while rest have data for 33 participants.

<!-- -->

    # Analyzing no. of observations
    nrow(daily_activity)

    ## [1] 940

    nrow(hourly_calories)

    ## [1] 22099

    nrow(hourly_intensities)

    ## [1] 22099

    nrow(hourly_steps)

    ## [1] 22099

    nrow(sleep_day)

    ## [1] 410

    nrow(weight_log)

    ## [1] 67

    # Summary Statistics
    daily_activity %>% 
      summary()

    ##        id                 date             total_steps    total_distance  
    ##  Min.   :1.504e+09   Min.   :2016-04-12   Min.   :    0   Min.   : 0.000  
    ##  1st Qu.:2.320e+09   1st Qu.:2016-04-19   1st Qu.: 3790   1st Qu.: 2.620  
    ##  Median :4.445e+09   Median :2016-04-26   Median : 7406   Median : 5.245  
    ##  Mean   :4.855e+09   Mean   :2016-04-26   Mean   : 7638   Mean   : 5.490  
    ##  3rd Qu.:6.962e+09   3rd Qu.:2016-05-04   3rd Qu.:10727   3rd Qu.: 7.713  
    ##  Max.   :8.878e+09   Max.   :2016-05-12   Max.   :36019   Max.   :28.030  
    ##  tracker_distance logged_activities_distance very_active_distance
    ##  Min.   : 0.000   Min.   :0.0000             Min.   : 0.000      
    ##  1st Qu.: 2.620   1st Qu.:0.0000             1st Qu.: 0.000      
    ##  Median : 5.245   Median :0.0000             Median : 0.210      
    ##  Mean   : 5.475   Mean   :0.1082             Mean   : 1.503      
    ##  3rd Qu.: 7.710   3rd Qu.:0.0000             3rd Qu.: 2.053      
    ##  Max.   :28.030   Max.   :4.9421             Max.   :21.920      
    ##  moderately_active_distance light_active_distance sedentary_active_distance
    ##  Min.   :0.0000             Min.   : 0.000        Min.   :0.000000         
    ##  1st Qu.:0.0000             1st Qu.: 1.945        1st Qu.:0.000000         
    ##  Median :0.2400             Median : 3.365        Median :0.000000         
    ##  Mean   :0.5675             Mean   : 3.341        Mean   :0.001606         
    ##  3rd Qu.:0.8000             3rd Qu.: 4.782        3rd Qu.:0.000000         
    ##  Max.   :6.4800             Max.   :10.710        Max.   :0.110000         
    ##  very_active_minutes fairly_active_minutes lightly_active_minutes
    ##  Min.   :  0.00      Min.   :  0.00        Min.   :  0.0         
    ##  1st Qu.:  0.00      1st Qu.:  0.00        1st Qu.:127.0         
    ##  Median :  4.00      Median :  6.00        Median :199.0         
    ##  Mean   : 21.16      Mean   : 13.56        Mean   :192.8         
    ##  3rd Qu.: 32.00      3rd Qu.: 19.00        3rd Qu.:264.0         
    ##  Max.   :210.00      Max.   :143.00        Max.   :518.0         
    ##  sedentary_minutes    calories   
    ##  Min.   :   0.0    Min.   :   0  
    ##  1st Qu.: 729.8    1st Qu.:1828  
    ##  Median :1057.5    Median :2134  
    ##  Mean   : 991.2    Mean   :2304  
    ##  3rd Qu.:1229.5    3rd Qu.:2793  
    ##  Max.   :1440.0    Max.   :4900

    hourly_calories %>% 
      summary()

    ##        id                 date                           calories     
    ##  Min.   :1.504e+09   Min.   :2016-04-12 00:00:00.00   Min.   : 42.00  
    ##  1st Qu.:2.320e+09   1st Qu.:2016-04-19 01:00:00.00   1st Qu.: 63.00  
    ##  Median :4.445e+09   Median :2016-04-26 06:00:00.00   Median : 83.00  
    ##  Mean   :4.848e+09   Mean   :2016-04-26 11:46:42.58   Mean   : 97.39  
    ##  3rd Qu.:6.962e+09   3rd Qu.:2016-05-03 19:00:00.00   3rd Qu.:108.00  
    ##  Max.   :8.878e+09   Max.   :2016-05-12 15:00:00.00   Max.   :948.00  
    ##      time          
    ##  Length:22099      
    ##  Class :character  
    ##  Mode  :character  
    ##                    
    ##                    
    ## 

    hourly_intensities %>% 
      summary()

    ##        id                 date                        total_intensity 
    ##  Min.   :1.504e+09   Min.   :2016-04-12 00:00:00.00   Min.   :  0.00  
    ##  1st Qu.:2.320e+09   1st Qu.:2016-04-19 01:00:00.00   1st Qu.:  0.00  
    ##  Median :4.445e+09   Median :2016-04-26 06:00:00.00   Median :  3.00  
    ##  Mean   :4.848e+09   Mean   :2016-04-26 11:46:42.58   Mean   : 12.04  
    ##  3rd Qu.:6.962e+09   3rd Qu.:2016-05-03 19:00:00.00   3rd Qu.: 16.00  
    ##  Max.   :8.878e+09   Max.   :2016-05-12 15:00:00.00   Max.   :180.00  
    ##  average_intensity     time          
    ##  Min.   :0.0000    Length:22099      
    ##  1st Qu.:0.0000    Class :character  
    ##  Median :0.0500    Mode  :character  
    ##  Mean   :0.2006                      
    ##  3rd Qu.:0.2667                      
    ##  Max.   :3.0000

    hourly_steps %>% 
      summary()

    ##        id                 date                          step_total     
    ##  Min.   :1.504e+09   Min.   :2016-04-12 00:00:00.00   Min.   :    0.0  
    ##  1st Qu.:2.320e+09   1st Qu.:2016-04-19 01:00:00.00   1st Qu.:    0.0  
    ##  Median :4.445e+09   Median :2016-04-26 06:00:00.00   Median :   40.0  
    ##  Mean   :4.848e+09   Mean   :2016-04-26 11:46:42.58   Mean   :  320.2  
    ##  3rd Qu.:6.962e+09   3rd Qu.:2016-05-03 19:00:00.00   3rd Qu.:  357.0  
    ##  Max.   :8.878e+09   Max.   :2016-05-12 15:00:00.00   Max.   :10554.0  
    ##      time          
    ##  Length:22099      
    ##  Class :character  
    ##  Mode  :character  
    ##                    
    ##                    
    ## 

    sleep_day %>% 
      summary()

    ##        id                 date            total_sleep_records
    ##  Min.   :1.504e+09   Min.   :2016-04-12   Min.   :1.00       
    ##  1st Qu.:3.977e+09   1st Qu.:2016-04-19   1st Qu.:1.00       
    ##  Median :4.703e+09   Median :2016-04-27   Median :1.00       
    ##  Mean   :4.995e+09   Mean   :2016-04-26   Mean   :1.12       
    ##  3rd Qu.:6.962e+09   3rd Qu.:2016-05-04   3rd Qu.:1.00       
    ##  Max.   :8.792e+09   Max.   :2016-05-12   Max.   :3.00       
    ##  total_minutes_asleep total_time_in_bed
    ##  Min.   : 58.0        Min.   : 61.0    
    ##  1st Qu.:361.0        1st Qu.:403.8    
    ##  Median :432.5        Median :463.0    
    ##  Mean   :419.2        Mean   :458.5    
    ##  3rd Qu.:490.0        3rd Qu.:526.0    
    ##  Max.   :796.0        Max.   :961.0

    weight_log %>% 
      summary()

    ##        id                 date              weight_kg      weight_pounds  
    ##  Min.   :1.504e+09   Min.   :2016-04-12   Min.   : 52.60   Min.   :116.0  
    ##  1st Qu.:6.962e+09   1st Qu.:2016-04-19   1st Qu.: 61.40   1st Qu.:135.4  
    ##  Median :6.962e+09   Median :2016-04-27   Median : 62.50   Median :137.8  
    ##  Mean   :7.009e+09   Mean   :2016-04-26   Mean   : 72.04   Mean   :158.8  
    ##  3rd Qu.:8.878e+09   3rd Qu.:2016-05-04   3rd Qu.: 85.05   3rd Qu.:187.5  
    ##  Max.   :8.878e+09   Max.   :2016-05-12   Max.   :133.50   Max.   :294.3  
    ##                                                                           
    ##       fat             bmi        is_manual_report     log_id         
    ##  Min.   :22.00   Min.   :21.45   Mode :logical    Min.   :1.460e+12  
    ##  1st Qu.:22.75   1st Qu.:23.96   FALSE:26         1st Qu.:1.461e+12  
    ##  Median :23.50   Median :24.39   TRUE :41         Median :1.462e+12  
    ##  Mean   :23.50   Mean   :25.19                    Mean   :1.462e+12  
    ##  3rd Qu.:24.25   3rd Qu.:25.56                    3rd Qu.:1.462e+12  
    ##  Max.   :25.00   Max.   :47.54                    Max.   :1.463e+12  
    ##  NA's   :65

### Plotting Data

#### Relationship between Total steps and sedentary minutes

    ggplot(data=daily_activity) + geom_point(mapping=aes(x=total_steps, y=sedentary_minutes, color=calories)) + 
      geom_smooth(mapping=aes(x=total_steps, y=sedentary_minutes))

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](images/Plotting%20Data%20between%20Total%20steps%20and%20Sedentary%20minutes-1.png)

**Observations:**

-   No trend is observed between total steps and inactive minutes.

-   No relationship between calories burnt and inactive minutes.

#### Relationship between very active minutes & calories

    ggplot(data=daily_activity) + geom_point(mapping=aes(x=very_active_minutes, y=calories, color=very_active_distance)) +
      geom_smooth(mapping=aes(x=very_active_minutes, y=calories))

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](images/Plotting%20Data%20between%20very%20active%20minutes%20&%20calories-1.png)

**Observations:**

-   A positive relation between very active minutes and calories burnt.

#### Average distribution of active minutes, by percentage

    total_sums <- daily_activity %>% 
      summarize(total_sm = sum(sedentary_minutes), total_lam = sum(lightly_active_minutes), total_fam=sum(fairly_active_minutes), total_vam=sum(very_active_minutes))

    total_tm <- total_sums %>% 
      summarize(sum(total_sm, total_lam, total_fam, total_vam))

    average_tm <- total_sums %>% 
      summarize(avg_sm = (total_sm/total_tm)*100, avg_lam = (total_lam/total_tm)*100, avg_fam = (total_fam/total_tm)*100, avg_vam = (total_vam/total_tm)*100)

    average_tm

    ## # A tibble: 1 × 4
    ##   avg_sm$sum(total_sm, total_lam, total_fam, total_vam…¹ avg_l…² avg_f…³ avg_v…⁴
    ##                                                    <dbl>   <dbl>   <dbl>   <dbl>
    ## 1                                                   81.3    15.8    1.11    1.74
    ## # … with abbreviated variable names
    ## #   ¹​avg_sm$`sum(total_sm, total_lam, total_fam, total_vam)`,
    ## #   ²​avg_lam$`sum(total_sm, total_lam, total_fam, total_vam)`,
    ## #   ³​avg_fam$`sum(total_sm, total_lam, total_fam, total_vam)`,
    ## #   ⁴​avg_vam$`sum(total_sm, total_lam, total_fam, total_vam)`

**Observations:**

-   81.3% is occupied by sedentary minutes.

-   Only 1.74% of activity is occupied by very active minutes.  

#### Relationship between total steps & calories

    ggplot(data=daily_activity) + geom_point(mapping=aes(x=total_steps, y=calories, color=total_distance)) +
      geom_smooth(mapping=aes(x=total_steps, y=calories))

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](images/Plotting%20Data%20between%20total%20steps%20&%20calories-1.png)

**Observations:**

-   A positive relationship between total steps and calories burnt.

#### Determining the time users were most active

    #Merging hourly datasets

    hcal_mean <- hourly_calories %>% 
      group_by(time) %>% 
      summarize(mean_calories = mean(calories))
    head(hcal_mean)

    ## # A tibble: 6 × 2
    ##   time     mean_calories
    ##   <chr>            <dbl>
    ## 1 00:00:00          71.8
    ## 2 01:00:00          70.2
    ## 3 02:00:00          69.2
    ## 4 03:00:00          67.5
    ## 5 04:00:00          68.3
    ## 6 05:00:00          81.7

    hint_mean <- hourly_intensities %>% 
      group_by(time) %>% 
      summarize(mean_intensity = mean(total_intensity))
    head(hint_mean)

    ## # A tibble: 6 × 2
    ##   time     mean_intensity
    ##   <chr>             <dbl>
    ## 1 00:00:00          2.13 
    ## 2 01:00:00          1.42 
    ## 3 02:00:00          1.04 
    ## 4 03:00:00          0.444
    ## 5 04:00:00          0.633
    ## 6 05:00:00          4.95

    hsteps_mean <- hourly_steps %>% 
      group_by(time) %>% 
      summarize(mean_steps = mean(step_total))
    head(hsteps_mean)

    ## # A tibble: 6 × 2
    ##   time     mean_steps
    ##   <chr>         <dbl>
    ## 1 00:00:00      42.2 
    ## 2 01:00:00      23.1 
    ## 3 02:00:00      17.1 
    ## 4 03:00:00       6.43
    ## 5 04:00:00      12.7 
    ## 6 05:00:00      43.9

    hourly_mean <- merge(hcal_mean, hint_mean, by.x="time", by.y="time")
    hourly_mean <- merge(hourly_mean, hsteps_mean, by.x="time", by.y="time")
    head(hourly_mean)

    ##       time mean_calories mean_intensity mean_steps
    ## 1 00:00:00      71.80514      2.1295503  42.188437
    ## 2 01:00:00      70.16506      1.4190782  23.102894
    ## 3 02:00:00      69.18650      1.0439443  17.110397
    ## 4 03:00:00      67.53805      0.4437299   6.426581
    ## 5 04:00:00      68.26180      0.6330472  12.699571
    ## 6 05:00:00      81.70815      4.9506438  43.869099

    ggplot(data=hourly_mean, aes(x=time, y=mean_calories, fill=mean_calories)) + geom_bar(stat="identity") + theme(axis.text.x = element_text(angle = 45))

![](images/Plotting%20time%20users%20were%20most%20active-1.png)

    ggplot(data=hourly_mean, aes(x=time, y=mean_intensity, fill=mean_intensity)) + geom_bar(stat="identity") + theme(axis.text.x = element_text(angle = 45))

![](images/Plotting%20time%20users%20were%20most%20active-2.png)

    ggplot(data=hourly_mean, aes(x=time, y=mean_steps, fill=mean_steps)) + geom_bar(stat="identity") + theme(axis.text.x = element_text(angle = 45))

![](images/Plotting%20time%20users%20were%20most%20active-3.png)

**Observations:**

-   Participants were most active between 16:00-18:59 hours followed by
    11:00-13:59 hours.

-   As expected, calories were least burnt between 23:00-04:59 hours.

#### Relationship between time in bed and minutes asleep

    ggplot(data=sleep_day) + geom_point(mapping=aes(x=total_minutes_asleep, y=total_time_in_bed, color=total_minutes_asleep))

![](images/Relationship%20between%20time%20in%20bed%20and%20minutes%20asleep-1.png)

**Observations:**

-   A positive linear relationship is observed between total time in bed
    and total minutes asleep.

-   Furthermore, few participants seem to have spent a lot of time in
    bed but didn’t asleep. The team could work to focus on sleep or
    insomnia related apps.

-   Out of 33 participants, 24 participants had logged their sleep
    activity. More marketing content towards sleep tracking features.

#### Trends with weight tracking

**Observations:**

-   Only 8 participants logged their weight in the Fitbit app, in
    contrast to 33 users logging their daily activities.

-   Bellabeat’s app could implement push notification services to
    encourage users to push for increased weight tracking.

## Phase 5 - Share

### [Bellabeat Analysis - Presentation](https://github.com/nipurngupta/nipurngupta.github.io/blob/main/Projects/Project%2001%20-%20Bellabeat/Bellabeat%20Presentation.pptx)

![](images/Presentation%20Caption.png)


### Bellabeat Analysis Summary (in R)

#### 1. Average Distribution of Active Minutes

![](images/Avg%20Dist%20of%20Active%20Minutes.png)

##### Findings:

-   Insights indicate most participants used the Fitbit app to track
    inactive moments such as daily work, commute etc. (approximately
    81.3%).

-   Marketing could push for campaigns such as small workout sessions
    between work, or encourage taking small walk breaks between work or
    leisure hours, such as taking a walk break every 30 minutes,
    encouraging more users to walk and track their fitness.

#### 2. Most Active Times of the Day

![](images/Sheet%201.png)

##### Findings:

-   Participants were found to be most active between evening hours of
    16:00-18:59 hours burning most calories, taking maximum no. of
    steps, with highest intensity during this time period. 2nd most
    active period was around lunch hours 12:00-14:59.

-   Surprisingly, the data indicates of calories being burnt during
    not-so intensive inactive time periods, which was tracked by the
    Fitbit app.

-   It could be suggested to market efficient health tracking and
    calorie tracking features by fitness devices, along with benefits of
    how proper sleep results in burning of calories.

#### 3. Sleep Tracking

![](images/Sheet%202.png)

##### Findings:

-   As anticipated, the data indicates a strict positive relationship
    between Total Time in Bed and Total Minutes asleep.

-   Furthermore, few participants seem to have spent a lot of time in
    bed but didn’t asleep. The team could work to focus on sleep or
    insomnia related apps.

-   Out of 33 participants, only 24 participants had logged their sleep
    activity. Marketing strategies encompassing sleep tracking features
    and benefits of sleep tracking could be adopted.

#### 4. Comparison of Activity Tracking

![](images/Sheet%203.png)

##### Findings:

-   While the sample size of 33 is not representative of the population,
    the data indicates Calorie and Sleep tracking to be the most popular
    Fitbit features used by participants.

-   Bellabeat’s marketing strategies could be guided to encourage
    participants for weight tracking and its benefits.

-   As a product suggestion, Smart Weight Measuring Scales could be
    introduced.

## Phase 6 - Act

### Trends in Smart device usage

-   Most participants used their FitBit app to track more than 80% of
    the sedentary minutes.

-   Less than 3% of the time is spent in active or intense workouts.

-   Participants were most active during evening hours between 16:00 to
    18:59 hours and 12:00 to 14:59 hours.

-   Step and Calorie tracking are the most popular Fitbit features.
    Weight tracking is the least popular feature.

### Application of these trends in Bellabeat’s marketing strategy

-   Both Fitbit and Bellabeat are engaged in the digital fitness
    industry. Bellabeat’s products could be used to encourage customers
    for health tracking by highlighting the benefits of smart-devices.

-   Benefits of sleep and weight tracking could be marketed with
    smart-watches and smart weighing scales, along with benefits of how
    proper sleep results in burning of calories. Proper sleep = active
    health.

-   Marketing could push for campaigns such as quick workouts between
    work, or encourage taking small walk breaks between work or leisure
    hours, such as taking a walk break every 30 minutes, encouraging
    more users to walk and track their fitness.

-   As the app is used to track more than 80% of sedentary activity,
    marketing campaigns highlighting, ‘Why being seated for long hours
    is bad for health’ could be strongly pushed.
