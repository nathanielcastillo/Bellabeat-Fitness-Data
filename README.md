# Bellabeat-Fitness-Data

## A Data Analysis Case Study using Excel, SQL and Tableau

THE APPASA method

# Introduction

Bellabeat is a high-tech manufacturer of health-focused products for women. Bellabeat is a small company, but they have potential to become a larger player in the global smart device market. The CEO believes that analyzing smart device fitness data could help umlock new growth oppurtunites for the company.

You have been asked to focus on one of Bellabeat’s products and analyze small device data to gain insight into how consumers are using their small devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy.

You are a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global small device market. Urška Sršen, cofounder and Chief Creative Ocer of Bellabeat, believes that analyzing small device fitness data could help unlock new growth opportunities for the company. You have been asked to focus on one of Bellabeat’s products and analyze small device data to gain insight into how consumers are using their small devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy. 

# Ask
### Main Business Task

* Analyze trends in smart device usage. 

* Share how these trends apply to Bellabeat customers.

* Share how these trends could help influence Bellabeat marketing strategy 

### Stakeholders

* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer 

* Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team 

* Bellabeat marketing analytics team: A team of data analysts responsible for collecting, analyzing, and repoing data that helps guide Bellabeat’s marketing strategy. You joined this team six months ago and have been busy learning about Bellabeat’’s mission and business goals — as well as how you, as a junior data analyst, can help Bellabeat achieve them. 

# Prepare
### Data Source

Data is downloaded from https://www.kaggle.com/datasets/arashnic/fitbit
Fitness data is spread out across 11 different CSVs

Data does not fully pass the ROCC method

The sample size is too small

- [ ] Reliable - High Sample Size, over 5 million rows of data    
- [x] Original - Public Dataset provided by Motivate International Inc. 
- [x] Comprehensive - 5 million rows with 13 columns of relevant data
- [x] Current - Data is from 2023
- [x] Cited - Motivate International Inc.

The 11 CSVs of data will be used

dailyActivity_merged.csv 
heartrate_seconds_merged.csv 
hourlyCalories_merged.csv 
hourlyIntensities_merged.csv 
hourlySteps.csv_merged 
minuteCaloriesNarrow.csv_merged 
minuteIntensititesNarrow.csv_merged 
minutesMETsNarrow_merged 
minuteSleep_merged   
minuteStepsNarrow_merged.csv 
weightLogInfo_merged.csv 

# Process
### Data Cleaning Tools

Data cleaning will be done in PostgreSQL
The code used will be documented below  

### Creating Tables

``` SQL
CREATE TABLE dailyActivity_merged (
    "Id" BIGINT,
    "ActivityDate" TIMESTAMP,
    "TotalSteps" INT,
    "TotalDistance" NUMERIC,
    "TrackerDistance" NUMERIC,
    "LoggedActivitiesDistance" NUMERIC,
    "VeryActiveDistance" NUMERIC,
    "ModeratelyActiveDistance" NUMERIC,
    "LightActiveDistance" NUMERIC,
    "SedentaryActiveDistance" NUMERIC,
    "VeryActiveMinute" INT,
    "FairlyActiveMinutes" INT,
    "LightlyActiveMinutes" INT,
    "SedentaryMinutes" INT,
    "Calories" INT
);

CREATE TABLE heartrate_seconds_merged (
    "Id" BIGINT,
    "Time" TIMESTAMP,
    "Value" INT
);

CREATE TABLE hourlyCalories_merged (
	"Id" BIGINT,
	"ActivityHour" TIMESTAMP,
	"Calories" INT
);

CREATE TABLE hourlyIntensities_merged (
	"Id" BIGINT,
	"ActivityHour" TIMESTAMP,
	"TotalIntensity" NUMERIC,
    "AverageIntensity" NUMERIC
);

CREATE TABLE hourlySteps_merged (
	"Id" BIGINT,
	"ActivityHour" TIMESTAMP,
	"StepTotal" INT
);

CREATE TABLE minuteCaloriesNarrow_merged (
	"Id" BIGINT,
	"ActivityMinute" TIMESTAMP,
	"Calories" NUMERIC
);

CREATE TABLE minuteIntensitiesNarrow_merged (
	"Id" BIGINT,
	"ActivityMinute" TIMESTAMP,
	"Intensity" NUMERIC
);

CREATE TABLE minuteMETsNarrow_merged (
	"Id" BIGINT,
	"ActivityMinute" TIMESTAMP,
	"METs" INT
);

CREATE TABLE minuteSleep_merged (
	"Id" BIGINT,
	"date" TIMESTAMP,
	"value" INT,
	"logId" BIGINT
	);

CREATE TABLE minuteStepsNarrow_merged (
	"Id" BIGINT,
	"ActivityMinute" TIMESTAMP,
	"Steps" INT
	);

CREATE TABLE weightLogInfo_merged (
	"Id" BIGINT,
	"Date" TIMESTAMP,
	"WeightKg" NUMERIC,
	"WeightPounds" NUMERIC,
	"Fat" INT,	
	"BMI" NUMERIC,	
	"IsManualReport" VARCHAR(255),
	"LogId" BIGINT
	);	
```

## Checking for NULL or Blank values

``` SQL
SELECT * FROM dailyactivity_merged 
WHERE 
"Id" IS NULL
OR
"ActivityDate" IS NULL
OR
"TotalSteps" IS NULL
OR
"TotalDistance" IS NULL
OR
"TrackerDistance" IS NULL
OR
"LoggedActivitiesDistance" IS NULL
OR
"VeryActiveDistance" IS NULL
OR
"ModeratelyActiveDistance" IS NULL
OR
"LightActiveDistance" IS NULL
OR
"SedentaryActiveDistance" IS NULL 
OR
"VeryActiveMinute" IS NULL 
OR
"FairlyActiveMinutes" IS NULL 
OR
"LightlyActiveMinutes" IS NULL
OR
"SedentaryMinutes" IS NULL
OR
"Calories" IS NULL
;
```

Renaming Columns to add units of measure standardize

``` SQL
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "TotalDistance" TO "TotalDistanceKm"
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "TrackerDistance" TO "TrackerDistanceKm"
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "LoggedActivitiesDistance" TO "LoggedActivitiesDistanceKm" 
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "VeryActiveDistance" TO "VeryActiveDistanceKm" 
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "ModeratelyActiveDistance" TO "ModeratelyActiveDistanceKm" 
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "LightActiveDistance" TO "LightlyActiveDistanceKm"
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "SedentaryActiveDistance" TO "SedentaryDistanceKm"
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "VeryActiveMinute" TO "VeryActiveMinutes"
;
ALTER TABLE public.dailyactivity_merged
RENAME COLUMN "FairlyActiveMinutes" TO "ModeratelyActiveMinutes"
;
```

Adding a Total Minutes Columns 

``` SQL
ALTER TABLE public.dailyactivity_merged
ADD COLUMN "TotalMinutes" INT 
;

UPDATE public.dailyactivity_merged
	SET 
	"TotalMinutes" = "VeryActiveMinutes" + "ModeratelyActiveMinutes" + "LightlyActiveMinutes" + "SedentaryMinutes"
;
```

Recalculating the total distance column

``` SQL

SELECT *, "LightlyActiveDistanceKm" + "ModeratelyActiveDistanceKm" + "VeryActiveDistanceKm" + "SedentaryDistanceKm" AS "TotalDistanceCheck"
FROM public.dailyactivity_merged
;	

UPDATE public.dailyactivity_merged
	SET 
	"TotalDistanceKm" = "LightlyActiveDistanceKm" + "ModeratelyActiveDistanceKm" + "VeryActiveDistanceKm" + "SedentaryDistanceKm";
```

heartrate seconds

``` SQL
SELECT * FROM heartrate_seconds_merged
WHERE 
"Id" IS NULL
OR
"Time" IS NULL
OR
"Value" IS NULL
```
```SQL
ALTER TABLE heartrate_seconds_merged
RENAME COLUMN "Value" TO "Heartrate"
```

HourlyData

``` SQL
CREATE TABLE hourlydata
(
	"Id" BIGINT,
    "ActivityHour" TIMESTAMP,
    "Calories" INT,
    "TotalIntensity" NUMERIC,
    "AverageIntensity" NUMERIC,
    "StepTotal" INT
);
```
Joining

``` SQL
INSERT INTO hourlydata
SELECT hcal."Id", hcal."ActivityHour", "Calories", "TotalIntensity", "AverageIntensity", "StepTotal"
FROM hourlycalories_merged AS hcal
FULL OUTER JOIN hourlyintensities_merged AS hint
ON hcal."Id" = hint."Id"
AND hcal."ActivityHour" = hint."ActivityHour"
FULL OUTER JOIN hourlysteps_merged AS hste
ON hcal."Id" = hste."Id"
AND hcal."ActivityHour" = hste."ActivityHour"
# Analyze & Share
```

MinuteData

``` SQL
CREATE TABLE minutedata
(
	"Id" BIGINT,
    "ActivityMinute" TIMESTAMP,
    "Calories" NUMERIC,
    "Intensity" NUMERIC,
    "METs" INT,
    "Steps" INT
);	
```
```SQL
INSERT INTO minutedata
SELECT mcal."Id", mcal."ActivityMinute", "Calories", "Intensity", "METs", "Steps"
FROM minutecaloriesnarrow_merged AS mcal
FULL OUTER JOIN minuteintensitiesnarrow_merged AS mint
ON mcal."Id" = mint."Id"
AND mcal."ActivityMinute" = mint."ActivityMinute"
FULL OUTER JOIN public.minutemetsnarrow_merged AS met
ON mcal."Id" = met."Id"
AND mcal."ActivityMinute" = met."ActivityMinute"
FULL OUTER JOIN minutestepsnarrow_merged AS mste
ON mcal."Id" = mste."Id"
AND mcal."ActivityMinute" = mste."ActivityMinute"



SELECT DISTINCT * 
FROM minutedata
;


SELECT * FROM minutedata
WHERE 
"Id" IS NULL
OR
"ActivityMinute" IS NULL
OR
"Calories" IS NULL
OR
"Intensity" IS NULL
OR
"METs" IS NULL
OR
"Steps" IS NULL
;
```

```SQL
SELECT * FROM public.minutesleep_merged
WHERE 
"Id" IS NULL 
OR
"date" IS NULL 
OR
"value" IS NULL 
OR
"logId" IS NULL 
;
```
```SQL
ALTER TABLE public.minutesleep_merged
RENAME COLUMN "date" TO "Date"
;
ALTER TABLE public.minutesleep_merged
RENAME COLUMN "value" TO "SleepMinutes"
;
ALTER TABLE public.minutesleep_merged
RENAME COLUMN "logId" TO "SleeplogId"
;
```
```SQL
SELECT * FROM public.weightloginfo_merged
WHERE 
"Id" IS NULL
OR
"Date" IS NULL
OR
"WeightKg" IS NULL 
OR
"WeightPounds" IS NULL
OR
"Fat" IS NULL 
OR
"BMI" IS NULL 
OR
"IsManualReport" IS NULL 
OR
"LogId" IS NULL
;
```

ALTER TABLE public.minutesleep_merged
RENAME COLUMN "date" TO "Date"


The Data has been analyzed and visualized in Tableau  
Interactive versions are available at links below

[2023 Cyclistic Ride Data Story 
](https://public.tableau.com/views/2023CyclisticRideDataStory/RideDataStory?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

[2023 Cyclistic Ride Data Dashboard
](https://public.tableau.com/views/2023CyclisticRideDataDashboard/RideDataDashboard?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

A PowerPoint Presentation is also available in the files 

## Membership Percentage
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Member%20Percent.png)

In 2023 approximately 2/3 of the rides were member rides

| Rider Type | Percentage | Count   |  
|------------|------------|---------|   
| Total      | 100%       | 4247346 |  
| Casual     | 35.44%     | 1505329 |  
| Member     | 64.56%     | 2742017 |

## Bicycle Type
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Bicycle%20Type.png)

Both members and casual riders ride classic bikes more than electric. Docked bicycles only appeared in casual rides

## Average Ride Duration
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Avg%20Ride%20Duration%20Minutes.png)

Members on average have shorter rides than casuals

| Rider Type | Average Ride Duration |
|------------|-----------------------|
| Casual     | 22.71                 |
| Member     | 11.87                 |

## Total Ride Count Per
### Hour
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Total%20Ride%20Count%20Per%20Hour.png)

Members are most likely commuting to and from work

| Rider Type | Peak Riding Hours | Count  |
|------------|-------------------|--------|
| Casual     | 5:00 PM           | 147950 |
| Member     | 8:00 AM           | 191013 |
| Member     | 5:00 PM           | 297284 |

### Weekday
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Total%20Ride%20Count%20per%20Weekday.png)

Casual and member riders have inverse ride preferences  
Members most likely ride for midweek work commutes and casuals most likely ride for weekend leisure

| Casual                 | Member                    |
|------------------------|---------------------------|
| Rides most on weekends | Rides most during midweek |

### Month
![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Total%20Ride%20Count%20per%20Month.png)

Both Casuals and member ride peak in the warmer summer months and fall off during the colder winter months

![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Start%20Station%20Map.png)

| Casual                           | Member                         |
|----------------------------------|--------------------------------|
| More concentrated start stations | More spread out start stations |

![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/End%20Station%20Map.png)

| Casual                         | Member                       |
|--------------------------------|------------------------------|
| More concentrated end stations | More spread out end stations |

![
](https://github.com/nathanielcastillo/Cyclistic-Case-Study/blob/main/Images/Top%2010%20Routes.png)

| Casual                                                 | Member                                                  |
|--------------------------------------------------------|---------------------------------------------------------|
| Most popular rides have the same start and end station | Most popular rides have different start and end station |

# Act
## Recommendations

### Top 3 Recommendations to convert Casual Riders to Annual Members

1. During the Summer Months, offer a discount on the annual membership
Since summer are busiest months, casual riders may opt to purchase an annual membership if they plan on riding a lot during the summer

2. Display advertising in & around popular casual stations
Popular casual stations such as Streeter Dr/Grand Ave and DuSable Lake Shore Dr/Monroe St offer high visibility to casual riders

3. Increase casual price during the weekends to incentivize membership purchases
Since weekends are the most popular casual days, casual riders may opt for an annual membership if the daily prices are going to be higher on the weekends



