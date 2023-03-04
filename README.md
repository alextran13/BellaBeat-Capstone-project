# BellaBeat-Capstone-project
Author: Alex T

Date: March 2, 2023

Tools used: postgreSQL pgadmin4, Tableau public desktop, Github


## Scenario

#### Customer summary

BellaBeat is a platform that designes health tracker for women. It is an app that connect to your smart devices such as smart phones, smart watches to track 
your sleep, calories, active distance, heart rate, etc. to gain your awareness about your own health. 
It was awarded Times's best invention in 2022 and has become Women's #1 health tracker. 
The Company has partnered with various accomplished and famous magazines such as Poosh, Voge, Women's Health and Shape. Bellabeat's motto is "Make informed decision about your health everyday".

#### Key stakeholders

* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer

* Sando Mur: Mathematician and Bellabeat’s cofounder

* Bellabeat marketing analytics team

#### Products 

* Bellabeat app: Tracks health data such as user's activity, sleep, stress, menstrual cycle, and mindfulness habits.
* Leaf: Can be worn as a bracelet, necklace, or clip. This tracker connects to the Bellabeat app and track stress, sleep, and activity.
* Time: This smart watch connecting to Bellabeat app, tracks user's activity, sleep, and stress. 
* Spring: This is a smart water bottle which connects to the Bellabeat app, tracks your hydration level during the day.
* Bellabeat membership: A subscription-based membership program for users that gives assistance and guidance on nutrition, sleep, mindful habits and so much more based on user's tracked data and personal goals.

#### My role

As a junior data analyst, I was asked to access datasets provided by the company to determine how data are already tracked and used by users. From the data patterns
and relationship, stakeholders will be informed to better make data-based marketing decisions to expand business.

## Ask
* Is there a relationships between user's sleep and the activity the next day?
* Is there a relationships between user's difference between time in bed and time actually asleep (indicating hardship of falling to sleep), and the activity the next day?
* Does intensity of activity help sleep?
* Does intensity of activity help user change calories?
* Does sleep help user lose calories or gain calories?
* If there is any relationship of these data, how could it applied into marketing strategies?

## Prepare

#### General info about the datasets
* Data link: https://www.kaggle.com/datasets/arashnic/fitbit 
* Availability: There are 18 data files in .csv format.
* Data source: https://zenodo.org/record/53894#.X9oeh3Uzaao 
Preprocessed and shared on Kaggle platform by Mobius. 
* Reliability: Datasets were consented by thirty Fitbit users.
* Originality: Datasets were collected by thirty Fitbit users and made available via distributed survey Amazon Mechanical Turk.
* Compresensiveness: The data covers sleep habits, daily intensity of activity, durations of those sleep and activities, and calories changes.
* Currentness: Data was recorded from April 12, 2016 to May 12, 2026
* Citation: Unknown
* Limitations: 
          Datasets only has a sample size of 30 therefore any relationships established from this datasets will have a low confidence level. 
          Data was collected in 2016 so it has been outdated. Any habits or prequencies of these habits of users have most likely changed.
          Finally, this data contains daily sleep, activity - habits which is formed after a long time. Only 2-month-duration of data is most likely not enough to make conclusive patterns, and therefore not enough for business decisions for a long run. 

#### Data preparation
Download the datasets using the link on Kaggle above. After that, create tables daily_activity and sleep_day using PostgreSQL Pgadmin4 and import the main tables daily_activity and sleep_day in csv format using import function in PostgreSQL. 

`CREATE TABLE daily_activity
(
   "Bellabeat_Id" bigint,
    "ActivityDate" date,
    "TotalSteps" bigint,
    "TotalDistance" numeric,
    "TrackerDistance" numeric,
    "LoggedactivitiesDistance" numeric,
    "VeryActiveDistance" numeric,
    "ModeratelyActiveDistance" numeric,
    "LightActiveDistance" numeric,
    "SedentaryActiveDistance" numeric,
    "VeryActiveMinutes" integer,
    "FairlyActiveMinutes" integer,
    "LightlyActiveMinutes" integer,
    "SedentaryMinutes" integer,
    "Calories" integer
    )`



## Process



## Analyze

## Share

## Act



