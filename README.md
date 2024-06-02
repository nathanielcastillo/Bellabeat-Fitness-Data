# Bellabeat-Fitness-Data

## A Data Analysis Case Study using Excel, SQL and Tableau

This Case Study will follow the APPASA format  

### [1. Ask](#ask)  
### [2. Prepare](#prepare)  
### [3. Process](#process)  
### [4. Analyze](#analyze)  
### [5. Share](#share)  
### [6. Act](#act) 

# Scenario

Bellabeat is a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global small device market. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing small device fitness data could help unlock new growth opportunities for the company. You have been asked to focus on one of Bellabeat’s products and analyze small device data to gain insight into how consumers are using their small devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy. 

## Ask
### Main Business Task

* Analyze trends in smart device usage. 

* Share how these trends apply to Bellabeat customers.

* Share how these trends could help influence Bellabeat marketing strategy to increase growth

### Stakeholders

* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer 

* Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team 

* Bellabeat marketing analytics team: A team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy.

# Prepare
### Data Source

Data is downloaded from https://www.kaggle.com/datasets/arashnic/fitbit  
Fitness data is spread out across 11 different CSVs

Data does not pass the ROCC method

- [ ] Reliable - Low Sample Size, 30 eligible FitBit users that responded to survey  
Data is from Amazon Mechanical Turk which relies on crowdsourcing work
- [ ] Original - 3rd party data source, not validated
- [ ] Comprehensive - Data only ranges 2 months from 3/12 to 5/12, not extensive enough
- [ ] Current - Data is from 2016, not current enough for the year 2024
- [ ] Cited - Not cited, relies on crowdsourced data

The 11 CSVs of data will be used

dailyActivity_merged.csv   
heartrate_seconds_merged.csv   
hourlyCalories_merged.csv   
hourlyIntensities_merged.csv   
hourlySteps_merged.csv   
minuteCaloriesNarrow_merged.csv   
minuteIntensititesNarrow_merged.csv   
minutesMETsNarrow_merged.csv   
minuteSleep_merged.csv     
minuteStepsNarrow_merged.csv   
weightLogInfo_merged.csv   

# Process

``` SQL

-- Creating Tables

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

## DailyActivity 

``` SQL

-- Null Check

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

--No NULLs returned

```

``` SQL

-- Standardizing column names and adding unit of measure

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

``` SQL

-- Adding Total Minutes Columns 

ALTER TABLE dailyactivity_merged
ADD COLUMN "TotalMinutes" INT 
;

UPDATE dailyactivity_merged
	SET 
	"TotalMinutes" = "VeryActiveMinutes" + "ModeratelyActiveMinutes" + "LightlyActiveMinutes" + "SedentaryMinutes"
;
```

``` SQL

-- There is a discrepancy between the TotalDistanceKm Column vs the calculated sum of the distances

SELECT *, "LightlyActiveDistanceKm" + "ModeratelyActiveDistanceKm" + "VeryActiveDistanceKm" + "SedentaryDistanceKm" AS "TotalDistanceCheck"
FROM dailyactivity_merged
;	

-- Updating TotalDistanceKm with recalculated value

UPDATE dailyactivity_merged
	SET 
	"TotalDistanceKm" = "LightlyActiveDistanceKm" + "ModeratelyActiveDistanceKm" + "VeryActiveDistanceKm" + "SedentaryDistanceKm"
;
```

## HeartrateData

``` SQL

-- NULL Check

SELECT * FROM heartrate_seconds_merged
WHERE 
"Id" IS NULL
OR
"Time" IS NULL
OR
"Value" IS NULL

-- No NULLs returned

```

```SQL

-- Renaming "Value" to "Heartrate"

ALTER TABLE heartrate_seconds_merged
RENAME COLUMN "Value" TO "Heartrate"
```

## HourlyData

3 tables will be joined 

hourlyCalories_merged  
hourlyIntensities_merged  
hourlySteps._merged     

``` SQL

-- Creating hourlydata table to insert JOINS

CREATE TABLE hourlydata (
	"Id" BIGINT,
    "ActivityHour" TIMESTAMP,
    "Calories" INT,
    "TotalIntensity" NUMERIC,
    "AverageIntensity" NUMERIC,
    "StepTotal" INT
);
```

``` SQL

-- Joining on column "Id" and Inserting to hourlydata table

INSERT INTO hourlydata
SELECT hcal."Id", hcal."ActivityHour", "Calories", "TotalIntensity", "AverageIntensity", "StepTotal"
FROM hourlycalories_merged AS hcal
FULL OUTER JOIN hourlyintensities_merged AS hint
ON hcal."Id" = hint."Id"
AND hcal."ActivityHour" = hint."ActivityHour"
FULL OUTER JOIN hourlysteps_merged AS hste
ON hcal."Id" = hste."Id"
AND hcal."ActivityHour" = hste."ActivityHour"
;
```

```SQL

-- NULL Check

SELECT * FROM hourlydata
WHERE 
"Id" IS NULL 
OR
"ActivityHour" IS NULL 
OR
"Calories" IS NULL 
OR
"TotalIntensity" IS NULL 
OR
"AverageIntensity" IS NULL 
OR
"StepTotal" IS NULL 
;

-- No NULLs returned

```

## MinuteData

4 tables will be joined

minuteCaloriesNarrow_merged  
minuteIntensititesNarrow_merged  
minutesMETsNarrow_merged  
minuteStepsNarrow_merged  

``` SQL

-- Creating minutedata table to insert JOINs

CREATE TABLE minutedata (
	"Id" BIGINT,
    "ActivityMinute" TIMESTAMP,
    "Calories" NUMERIC,
    "Intensity" NUMERIC,
    "METs" INT,
    "Steps" INT
);	
```

```SQL

-- Joining on column "Id" and Inserting to minutedata table

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
;
```

``` SQL

-- NULL Check

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

-- No NULLs returned

```

## SleepData

```SQL

-- NULL Check

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

-- No NULLs returned

```

```SQL

-- Standardizing and renaming columns

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

## Weightlog

```SQL

-- NULL Check

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

-- No NULLs returned

```

```SQL

-- Renaming "LogId" to "WeightLogId"

ALTER TABLE weightloginfo_merged
RENAME COLUMN "logId" TO "WeightlogId"
;
```
# Analyze

## Usage Statistics 

![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Usage%20Statistics.png)

Minute data are the most used functions in terms of Users and records of data collected
Weightlog is the least used function in terms of users and records of data collected

## Daily Activity Minutes Percentage 
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Daily%20Active%20Minutes%20Percentage.png)

More than 80% of a user's daily minutes is spent sedentary (including sleep)

## Sleep Minutes Per Weekday
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Sleep%20Minutes%20Per%20Weekday.png)

Average Sleep Minutes is 423  
Users sleep more than average on Sunday, Monday and Tuesday  
Sleep less than average minutes on Wednesday, Thursday, Friday and Saturday  

## Average Minutes Per Weekday

![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Activity%20Minutes%20Per%20Weekday.png)

Sedentary Minutes Range from 850 to 1055  
Average Sleep Minutes is 423  
Sedentary Minutes not asleep range from 427 to 632

## Fitness Time of Day
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Fitness%20Time%20of%20Day.png)

Heartrate, METs, Average Intesity and Step Total values peak around 12:00 PM and 7:00 PM  
So during lunch hours and after work hours

## Weight and BMI Distribution Per User

![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Weight%20Distribution%20Per%20User.png)

|            | Min   | Max   | Avg   |
|------------|-------|-------|-------|
| Weight Lbs | 117.5 | 285.7 | 175.6 |

## Average Weightlog
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Average%20Weightlog.png)

Over a 12 day period the average weight decreased by 6.18 pounds,  
however the sample size is too small for the results to be conclusive 

## Calories Burned Scatter Plots
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Calories%20Burned%20Fitness%20Correlations.png)

The Avg Intensity value has the strongest and most reliable correlation to Calories Burned followed by Step Total  

![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Calories%20Burned%20Activity%20Type%20Correlations.png)

Very Active Mins is the Activity Type that has the strongest and most reliable correlation to Calories Burned

## Correlation Values
![
](https://github.com/nathanielcastillo/Bellabeat-Fitness-Data/blob/main/Images/Correlation%20Values.png)

The R value indicates how strong the correlation is from a scale of -1 to 1  
The R^2 value is one way to show how reliable the correlation is from a scale of 0 to 1

Users who wish to burn the most calories can try increasing their very active minutes, high average intensity activities and step total  
However it is important to note that correlation does not imply causation  
There may be other factors that are influencing the amount of calories burned

# Share

The Data has been analyzed and visualized in Tableau  

[Dashboard
](https://public.tableau.com/views/BellabeatDashboard_17171734796480/BellaBeatDashboard?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

# Act
## Recommendations

### Marketing Recommendations to Increase Growth Based on Data

1. Since the sample size and range of the data analyzed is so small, The first recommendation is to gather more reliable data so that more effective and reliable analysis can occur

2. Focus on marketing the minute tracking capabilities of Bellabeat products because minute tracking is the most used feature in the Fitbit data. Shift marketing away from weightlog features since it is the least used 
   
3. Feature women of various weights in Bellabeat's advertising. The weight range of women in the data is 117.5 - 285.7, showcasing women of all different weights may appeal to the customers who share the same weight

4. Showcase the product in use during lunch hours and afterwork hours as these are the times of day where fitness activites peak







