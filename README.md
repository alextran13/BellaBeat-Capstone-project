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

>
          CREATE TABLE daily_activity
          (
             id bigint,
              activity_date date,
              tot_step bigint,
              tot_distance numeric,
              tracker_distance numeric,
              logged_distance numeric,
              very_active_distance numeric,
              moderately_active_distance numeric,
              light_active_distance numeric,
              sedentary_active_distance numeric,
              very_active_minutes integer,
              fairly_active_minutes integer,
             light_active_minutes integer,
              sedentary_active_minutes integer,
              calories integer
              );
Now that we have imported the data, let's process it!


## Process

I checked if there were duplicated rows in the three tables daily_activity, sleep_day, and weight_log_info using the code below:

>
          select count(*) as count
          from daily_activity
          group by id, activity_date, tot_step, calories
          having count(*) >1
          order by id`

After finding out that there was no duplicates in all three tables, I checked for missing values by this code below: 

>         
          SELECT *
          from daily_activity 
          where id is Null
          or activity_date is null
          or tot_step is null
          or tot_distance is null
          or tracker_distance is null
          or logged_distance is null
          or very_active_distance is null
          or moderately_active_distance is null
          or light_active_distance is null
          or sedentary_active_distance is null
          or very_active_minutes is null
          or fairly_active_minutes is null
          or lightly_active_minutes is null
          or sedentary_minutes is null
          or calories is null;

By doing this, I managed to find in weight_log_info table, there was 65 missing values out of 67 in a column so I decided to drop the column since 2 values don't help me anything in finding the patterns of data.

> 
          alter table weight_log_info 
          drop column fat;
          
Voila! No more fat!

After the cleaning data is done, I can now move on to merging. 

On exploring the datasets, I saw that sleep_day data has 24 unique id while daily_activity has 33 unique id, and weight_log_info only has 8 unique id so I'm not too sure merging is the best idea right now. Ooooohhhh this is a tough call but I will try to see if there is any relationships between the data but will let the stakeholders know the obvious limitations of the datasets (if I can find the relationships at all that is!)

Now let's move the fun zone - Data analysis!

## Analyze

> 
          /*statistics summary*/
          
          create table summary_table as
          select 'total_distance' as category, 
          round(sum(tot_distance)) as sum,
          round(min(tot_distance)) as min,
          round(avg(tot_distance)) as mean,
          round(percentile_cont(0.5) 
          within group (order by tot_distance)) as median,
          round(max(tot_distance)) as max
          from joined_activity_sleep;
          
          insert into summary_table(category,sum,min,mean,median,max)
          select
          'tot_steps', 
          round(sum(tot_steps)),
          round(min(tot_steps)),
          round(avg(tot_steps)),
          round(percentile_cont(0.5) 
          within group (order by tot_steps)),
          round(max(tot_steps))
          from joined_activity_sleep;

<img width="520" alt="Screenshot 2023-03-06 at 1 02 52 PM" src="https://user-images.githubusercontent.com/74520739/223031589-44ba2603-808d-49ef-93ee-0ffd4ec1b2bb.png">

>
          /* summary stats on day of the week */
          
          select distinct dayofweek, count(*)
          from joined_activity_sleep
          group by dayofweek
          having dayofweek is not null
          order by count(*);
          


<img width="520" alt="Screenshot 2023-03-08 at 2 43 01 PM" src="https://user-images.githubusercontent.com/74520739/223651797-47e4d259-f6ea-4cf7-a206-400ad21dac3b.png">

I noticed that Monday is the lowest day of the week that users record data, which makes sense since it's the first day of the week and according to so many memes complaining about Monday. Whereas Thursday, Tuesday and Wednesday are in the middle of the week where users have time to pick up the pace for the week with the highest record data.

Relationships between user's difference between time in bed and time actually asleep (indicating hardship of falling to sleep), and the activity the next day


## Share

## Act



