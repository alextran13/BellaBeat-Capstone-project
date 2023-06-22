# BellaBeat-App-project
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
* Is there a relationships between user's sleep and the activity?
* Is there a relationships between user's difference between time in bed and time actually asleep (indicating hardship of falling to sleep), and the activity?
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

Before moving on to Data Analysis part, I want to merge sleep table with daily activity table and replace some null values to zero for easier calculations later.

>

          /* replace null value with zeros */     
          update joined_activity_sleep
          set tot_distance = 0,
          tracker_distance = 0,
          logged_distance = 0,
          very_active_distance = 0,
          moderately_active_distance = 0,
          light_active_distance = 0,
          sedentary_active_distance = 0,
          very_active_minutes = 0,
          fairly_active_minutes = 0,
          lightly_active_minutes = 0,
          sedentary_minutes = 0,
          calories = 0
          where tot_distance is null
          and tracker_distance is null
          and logged_distance is null
          and very_active_distance is null
          and moderately_active_distance is null
          and light_active_distance is null
          and sedentary_active_distance is null
          and very_active_minutes is null
          and fairly_active_minutes is null
          and lightly_active_minutes is null
          and sedentary_minutes is null
          and calories is null;



          /* check through the table */

          select * from joined_activity_sleep



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

I noticed that Monday is the lowest day of the week that users record data, which makes sense since it's the first day of the week. Whereas Thursday, Tuesday and Wednesday are in the middle of the week where users have time to pick up the pace for the week with the highest record data. We can integrete this knowledge into a reminder for users to be active more and be attentive to their sleep and daily activity.

Ralationships between sleep and calories and activity

>

          select id, extract(week from activity_date) as week,
          sum(tot_min_sleep) as sum_sleep,
          sum(calories) as sum_cal, 
          count(very_active_minutes) as count_active_min,
          count(fairly_active_minutes) as count_fair_active_min,
          count(lightly_active_minutes) as count_light_active_min,
          count(sedentary_minutes) as count_sedentary_min
          from joined_activity_sleep
          where id is not null
          group by id, week
          order by id, week;
          
![sleep and calories](https://user-images.githubusercontent.com/74520739/223945239-c92626ca-8c32-447d-af8e-8de3b927837f.png)

![sleep and active min](https://user-images.githubusercontent.com/74520739/223948410-1f0a293d-f2ba-417d-aa70-71f8985e79b0.png)

![sleep and active distance](https://user-images.githubusercontent.com/74520739/223949934-37a6180b-ada5-418a-96cd-6a5d3f70353c.png)

From the results, I can see that there is a linear relationship between sleep and active minutes, distance and therefore calories of users. 

Relationships between user's difference between time in bed and time actually asleep (indicating hardship of falling to sleep), and the activity the next day?
          
>         
          select id,
          extract(week from activity_date) as week,
          sum(tot_min_sleep) as sum_sleep,
          sum(tot_t_inbed) as sum_inbed,
          sum(t_awake_inbed) as sum_awake_inbed,
          round(sum(tot_distance)) as tot_distance,
          sum(calories) as sum_calories
          from joined_activity_sleep
          group by id, week
          order by id, week; 
          


![time struggle to sleep and calories ](https://user-images.githubusercontent.com/74520739/223963200-118bfd6e-3937-492f-829d-6f7fd41097e9.png)

![struggle sleep and active min](https://user-images.githubusercontent.com/74520739/223963252-733c0fac-ed50-4743-9e69-43dbf0f391ca.png)

![struggle to fall sleep and total active distance](https://user-images.githubusercontent.com/74520739/223963293-41ead0e5-a3a8-4467-9220-394263ae8852.png)


I assumed the difference between total time in bed and actual sleep time to be the time users struggle to fall asleep. I could imagine the longer the time users struggle to fall asleep, the more tired they would be and therefore lower their activity performance, hence their calories. So I put together three graphs to test my hypothesis. Nonetheless, after checking the graphs, I don't see a clear relationships between this time struggle falling asleep and avtivie distance, time, and calories.

Now I am looking at the impact of tensity of activity to calories and sleep

>         

          select distinct id,
          sum(calories) as tot_cal,
          sum(tot_min_sleep) as sleep,
          sum(very_active_minutes) as active,
          sum(fairly_active_minutes) as fair,
          sum(lightly_active_minutes) as light,
          sum(sedentary_minutes) as sedentary
          from joined_activity_sleep
          where id is not null
          group by id
          order by id;
![intensity and calories](https://user-images.githubusercontent.com/74520739/224560545-00bf38b4-31c2-4977-803b-73ada2c5cda1.png)



![Intensity of activity vs sleep time ](https://user-images.githubusercontent.com/74520739/224560748-0a8472e4-00d5-4f96-9a2e-75eb588ec1a0.png)



I can see that the the highest intensity of activity (very active minutes), more likely that users have less struggle time for falling asleep, more sleep time, and more likely to burn calories even though users spend less time in the very active intensity than other intensity groups. 

![Sleep time vs cal over week](https://user-images.githubusercontent.com/74520739/224560867-6f1da322-885c-43dd-82a2-3f013b31df8e.png)


My hypothesis was maybe the time user sleep can affect the calories burnt during the day. Well when i look at the graph, at first I don't see the impact of sleep on calories but there are so many users have the exact same sleep (by exact I mean exact minutes of sleep, not even 1 minute different every night or the first 20 days) but they logged in different amount of calories. I observe some linear trends showing that the more time of sleep, the highter the calories burnt. However, the datasets seemed invented so I'd say the patterns found were not conclusive. 



## Share

![sleep effect on calories dashboard](https://user-images.githubusercontent.com/74520739/224561057-f366b683-5f7b-4f03-8411-cea54705ad7d.png)

As you can see, there is a linear trend between total sleep time and calories. I made a dashboard here to show you the relationships during the week (upper graphs) and total calories burnt (lower graph). In general, the more users sleep, the more calories get burnt. The lower graph shows the stronggest correlation with R-squared = 0.85 while correlation on Tuesday and Wednesday to be the highest during the week, which is not surprised since we saw the recording count on those days to be the highest in the earlier graph. 

![Sleep and active t and s](https://user-images.githubusercontent.com/74520739/224561462-57b0c3fd-0dc2-4885-9acd-1ea28ef74aa9.png)

In addition, sleep time also has an effect on length of activity and distance. As can be seen from the graphs, a regression relationship is shown in both activiy and distance, in which time active being more impacted with a bigger R-squared = 0.95 while distance active R-squared = 0.79. This means, users tend to be active longer with more sleep. This makes sense, when one is more well rested, one tend to feel better, hit the gym more, or generally be more active. But doesn't more time active means more distance active? Well let's check it out, shall we?


![intensity vs cal and sleep](https://user-images.githubusercontent.com/74520739/224561340-063a15c6-b2b9-403d-b0a1-3ecccdc7a714.png)

Finally, this dashboard of relationship between activity intensity and sleep actually answers my question. So turns out more sleep time means more time active, however, doesn't necessarily mean more distance. Since users might tend to be more active with setentary and light exercises, which lead to more time but not necessarily longer distance. In fact, both graphs show us the light and sedentary intensity of activity have more consistent linear relationship on both calories and sleep time. 

## Act

* I found that users tend to be more proactive during the week, especially on Tuesday, Wednesday, and Thursday. 
We can apply this to notifications and reminders to encourage users to be more active during the rest of the week and be consistent with their exercises. 
* I also spot the linear relationship between sleep and active time, this essentially leads to more calories burnt. 
I believe this is not news. We can conduct the same analysis with more data points and present this to on our app to encourage women to get more sleep to get more health benefits. We can do more data analysis such as this to build a section on our app that specializes in fun facts that they can look into. 
* Another trend I saw is the relationship between the intensity of activity on sleep and calories. Turns out light and sedentary exercises tend to have more direct correlation with calories and sleep than very active and fairly active intensity. We can integrate this information into our fun facts or notifications as well, to remind and encourage users to be active. Even just light exercises on days, they don't want to get out of bed can help with calories and sleep according to our data.



