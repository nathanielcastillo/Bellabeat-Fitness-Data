# Bellabeat-Fitness-Data

## A Data Analysis Case Study using Excel, SQL and Tableau

THE APPASA method

### [Ask](#1-ask)

# Introduction

You are a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global small device market. Urška Sršen, cofounder and Chief Creative Ocer of Bellabeat, believes that analyzing small device fitness data could help unlock new growth opportunities for the company. You have been asked to focus on one of Bellabeat’s products and analyze small device data to gain insight into how consumers are using their small devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy. 

## 1. Ask
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

- [ ] Reliable - Low Sample Size,   
- [x] Original - 
- [x] Comprehensive - 
- [x] Current - 
- [x] Cited - 

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

PostgreSQL will be used for data cleaning

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
ALTER TABLE dailyactivity_merged
RENAME COLUMN "TotalDistance" TO "TotalDistanceKm"
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "TrackerDistance" TO "TrackerDistanceKm"
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "LoggedActivitiesDistance" TO "LoggedActivitiesDistanceKm" 
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "VeryActiveDistance" TO "VeryActiveDistanceKm" 
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "ModeratelyActiveDistance" TO "ModeratelyActiveDistanceKm" 
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "LightActiveDistance" TO "LightlyActiveDistanceKm"
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "SedentaryActiveDistance" TO "SedentaryDistanceKm"
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "VeryActiveMinute" TO "VeryActiveMinutes"
;
ALTER TABLE dailyactivity_merged
RENAME COLUMN "FairlyActiveMinutes" TO "ModeratelyActiveMinutes"
;
```

Adding a Total Minutes Columns 

``` SQL
ALTER TABLE dailyactivity_merged
ADD COLUMN "TotalMinutes" INT 
;

UPDATE dailyactivity_merged
	SET 
	"TotalMinutes" = "VeryActiveMinutes" + "ModeratelyActiveMinutes" + "LightlyActiveMinutes" + "SedentaryMinutes"
;
```

Recalculating the total distance column

``` SQL

SELECT *, "LightlyActiveDistanceKm" + "ModeratelyActiveDistanceKm" + "VeryActiveDistanceKm" + "SedentaryDistanceKm" AS "TotalDistanceCheck"
FROM dailyactivity_merged
;	

UPDATE dailyactivity_merged
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
SELECT * FROM minutesleep_merged
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
ALTER TABLE minutesleep_merged
RENAME COLUMN "date" TO "Date"
;
ALTER TABLE minutesleep_merged
RENAME COLUMN "value" TO "SleepMinutes"
;
ALTER TABLE minutesleep_merged
RENAME COLUMN "logId" TO "SleeplogId"
;
```
```SQL
SELECT * FROM weightloginfo_merged
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

# Act
## Recommendations

### Top 3 Recommendations to convert Casual Riders to Annual Members

1. During the Summer Months, offer a discount on the annual membership
Since summer are busiest months, casual riders may opt to purchase an annual membership if they plan on riding a lot during the summer

2. Display advertising in & around popular casual stations
Popular casual stations such as Streeter Dr/Grand Ave and DuSable Lake Shore Dr/Monroe St offer high visibility to casual riders

3. Increase casual price during the weekends to incentivize membership purchases
Since weekends are the most popular casual days, casual riders may opt for an annual membership if the daily prices are going to be higher on the weekends



