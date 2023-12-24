# Google Data Analytics Capstone Cyclistic Case

## Introduction

In this case study, I will be taking on the role of a junior data analyst at the fictional company, Cyclistic. To answer the key business questions, I will be following the steps of the data analysis process: **ask, prepare, analyze, share, and act**. 

Source Data: [Here](https://divvy-tripdata.s3.amazonaws.com/index.html) [Last accessed 12/16/2023]

## Case Summary
### Cyclistic
Established in 2016, Cyclistic has become a successful bike-share program with a fleet of over 5,800 bicycles and 600 docking stations. What sets Cyclistic apart is its dedication to inclusivity, offering diverse bike options like reclining bikes, hand tricycles, and cargo bikes to accommodate people with disabilities who cannot use standard two-wheeled bikes. While the majority of riders prefer traditional bikes, approximately 8% opt for assistive options. Despite the primary focus on leisurely rides, a notable 30% of users utilize bikes for daily commuting.

Lily Moreno, the director of marketing and manager, plays a crucial role in developing campaigns and initiatives through channels such as email and social media. Cyclistic's previous marketing strategy emphasized building general awareness and appealing to broad consumer segments, featuring flexible single-ride, full-day, and annual memberships. Recognizing the profitability of annual memberships over casual riders, Moreno aims to convert casual riders, already acquainted with Cyclistic, into long-term members. The goal is clear: design marketing strategies focused on converting casual riders into annual members. To achieve this, the marketing analyst team, including myself, is tasked with understanding the distinctions between annual members and casual riders, exploring why casual riders would choose membership, and assessing the impact of digital media on marketing tactics.

## Ask 

- [x] Business Task: How to convert more casual riders into annual members by designing a specific and targeted marketing strategy. 

Guiding questions
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

## Prepare
### Data Source
The [data source](https://divvy-tripdata.s3.amazonaws.com/index.html) for the analysis is Cyclistic's historical trip data spanning from January 2022 to December 2022. This data has been provided by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement). It is considered public data and is being utilized to gain insights into the usage patterns of Cyclistic's bike ride-sharing service among customers.

However, there are certain limitations to be acknowledged. Due to data privacy concerns, the analysis cannot incorporate personally identifiable information of riders. Consequently, it is not possible to link pass purchases to credit card numbers. This limitation means that the analysis won't be able to determine whether casual riders reside within Cyclistic's service area or if they have made multiple single-pass purchases. 

### Data Organization
I will utilize 12 csv files with the following naming convention: YYYYMM-divvy-tripdata. Each file includes the fields: ride_id, rideable_type, started_at, ended_at, start_station_name, start_station_id, end_station_name, end_station_id, start_lat, start_lng, end_lat, end_lng, and member_casual. 

## Process
Given the worksheet limitation of 1,048,576 rows in Microsoft Excel, we will be using BigQuery to clean and analyze the data which contains over 5.6 million rows. 

### Combining the Data
To merge the 12 CSV files, I employed the batch upload operation in BigQuery. Initially, I uploaded all the files to a Google Cloud Storage bucket named gc_cyclist_data. Subsequently, I utilized the wildcard function to upload and append the content of the 12 CSV files into a single table named [2022_trip_data_raw](https://console.cloud.google.com/bigquery?hl=en&project=voltaic-spider-405020&ws=!1m5!1m4!4m3!1svoltaic-spider-405020!2sCyclist_Data!3s2022_trip_data_raw). This approach was feasible due to the consistent schema across all files.

### Data Exploration

SQL Query: [Data Exploration and Validation](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/commit/b848d4154ad38c3e95d1bd90430c7d07137b9290)

Before cleaning the data, I review the data to understand the following: 
* What data types and inputs are present?
* What is my primary key?
* Are there any potential calculations/logic errors that may arise?

Observations: 
1. The following table contains a count of all null values
![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/8b34653e-6fd8-4ae3-8e30-8b96f606d4c2)

**start_station_id, end_station_name, end_station_id, end_lat,end_lng** fields contain null values. This could be from trips that were not recorded correctly or may not have been completed at the time of data gathering. These rows will need to be removed during the cleaning stage.

2. To check for any duplicate trips we can use the distinct function in **ride_id**
![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/afc13c12-8a73-489b-8771-0da05dde9ef4)

Given that the count of all observations minus the count of distinct **ride_id** is equal to 0, we know that **ride_id** is the primary key and there are no duplicate entries in **2022_trip_data_raw**. 

3. Input Validation

![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/d687f1a3-1042-4e3e-8652-23d9a4f96ca5)

   **ride_id** All values are consistent and have a length of 16.


![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/213bf0e2-5b9b-44d2-96b8-4edc8d6748a7)

**rideable_type** 3 types of bikes account for all trips: electric_bike, classic_bike, and docked_bike.


![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/c93fb317-fa06-43c5-b027-e3cf93b3cfa2)

**member_casual** 2 types of rides account for all trips: casual and member.


![image](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/6dac5e7f-3910-4136-9da6-65deed4a3cf2)

**ride_length_minutes** There were 5891 trips under 1 minute or over 24 hours. Trips under 1 minute were excluded to account for riders who were testing the service or started a trip and changed their minds. Trips over 24 hours were excluded to account for riders who did not return bikes because they forgot, chose not to, or were not recognized by the docking station. 


### Data Cleaning
SQL Query: [Data Cleaning](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/blob/main/2.%20Data%20Cleaning)

Cleaning Criteria
1. Added 4 fields: ride_length_minutes, day_of_week, month and hour
2. Remove all observations containing null values and trip durations equal/less than 60 seconds or over/equal to 24 hours.

A total of 1,375,912 observations were removed.
A total of 4,291,805 observations remain. 


## Analyze

SQL Query: [Data Analysis](https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/blob/main/3.%20Data%20Analysis)

Tableau Visualization: [Dashboards](https://public.tableau.com/app/profile/justin.chiu5869/viz/GDACTableau_17031313922010/RideType)

Using the cleaned data set I queried tables to analyze the difference in each field type and visualized them in Tableau. Following the guiding questions, we first take a look at how casual riders differ from annual members in the following: 

### Types of Bikes Used in 2022
I first examined the percentage of users based on each type of bike, further segmented by member type with blue representing casual riders and orange for annual members. 
<img width="1401" alt="image" src="https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/cfccb57e-fb0e-44b2-9b66-031cae0eab10">

Of the 4,292,644 cleaned observations remaining, 59.67% of users are annual members and 40.33% are casual users. The percentages shown above are part of the total number of riders in 2022. Annual members exclusively prefer classic or electric bikes and were not interested in docked bikes whatsoever. 


### Total Trips by Month, Day, and Time of Day
Next, I plotted the number of trips per month, day of the week, and hour of the day with the blue line for casual riders and the orange line for members. 
<img width="1404" alt="image" src="https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/5afcbbe7-bf89-4682-bbf1-9c0b39f50774">

#### Month 
When examining the trip count by month both types of members exhibit similar tendencies with usage peaking in the summer months and bottoming out during the winter months. 

#### Day of Week 
The trip count for members remains relatively consistent, holding throughout the weekday and slightly tapering off on the weekends. For casual riders, we observe a rider count significantly lower that remains steady from Monday to Thursday, picking up on Friday,  heavily picking up and crossing over on Saturday, and tapering off on Sunday. 

#### Hour of Day 
In the last chart, we examine the rider count based on the hour of the day when a trip starts. Member usage picks up starting at 5 am, and peaks twice, once at 8 am and again at 5 pm and tapers off at 10 pm.  For casual members, we observe a steady increase in the number of trips beginning at 5 am, peaking at 5 pm then dropping off significantly by 8 pm. 

####  Key Insights
From the above time frames, we gather that both types of members are greatly affected by seasonality with the highest trip count occurring in July. Rider count by day of the week is reflective of the typical Monday-to-Friday commute. Usage by casual riders is fairly consistent throughout the week with a crossover on Saturdays most likely as a result of leisure activities. Trip count by hour of day for members is in line with the typical 9 am to 5 pm work schedule observing peaks at 8 am and 5 pm. 



### Ride length by month, day of week, and time of day
Next, the average length of each trip in minutes is plotted against the month, day of the week, and hour of the day.
<img width="1396" alt="image" src="https://github.com/c-justin/Google_Data_Analytics_Capstone_Cyclistic_Case/assets/151407442/888fe8f6-6966-419e-83b7-6b8f0528fa72">



#### Month 
Looking at the average trip duration in minutes, we notice that for annual members, trip duration remains constant, slightly increasing in the summer months. Trip duration for casual members is consistently higher throughout all months, peaks in May, and gradually tapers off at the end of the year. 

#### Day of Week
For annual members, trip duration by day of the week remains constant throughout the weekdays and slightly increases on the weekend. Casual members have trip durations consistently higher throughout the entire week, with slightly elevated levels from Saturday to Monday. 

#### Hour of Day 
Annual members exhibit a consistent trip duration throughout the hours of the day. Casual members display a moderate increase between 8 am to 5 pm with a slight drop off at 5 pm that is sustained until 3 am. 

#### Key Insights
Trip duration for annual members does not vary significantly between time frames. Casual member trip durations are consistently longer throughout all time frames. The difference in trip durations is in line with the nature of commutes for annual members and leisure trips for casual riders.  


## Act

### Summary

| Member | Casual|
| --- | --- |
| Classic and Electric | Docked |
|  |  |
| Generally prefers trips on the weekdays and between the hours of 8 am to 5 pm | Trip count increases significantly over the weekends and steadily increases from 8 am to 5 pm |
|  |  |
| Travels more frequently but with average trip durations much shorter at approximately half the duration of casual riders | Travels less frequently in general, peaks on weekends, and has an average trip duration twice that of annual members |

### Recommendations 

1. Marketing campaigns should be targeted towards casual riders in the spring and summer with a focus on weekends. Can offer early bird discounts to incentivize subscriptions in the late winter/early spring to offset seasonality.
2. Causl riders generally have longer trip durations and seldom use the service on weekdays, offering a weekend pass or flat rate bulk discount would be effective. 
3. The most effective deployment of ad spend would be to users of classic and electric bikes, docked bikes have no utility in converting members
4. Geographical analysis can be conducted to identify common hotspots for start and end locations. Trips originating from schools or hospitals can be used to identify organizations for collaboration/sponsorship (student/medical professional discounts). 




