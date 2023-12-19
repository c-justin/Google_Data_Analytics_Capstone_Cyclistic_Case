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

A total of 1,375,073 observations were removed.
A total of 4,292,644 observations remain. 


## Analyze
SQL Query:
Tableau Visualization: 

Using the cleaned data set I queried tables to analyze the difference in each field type and visualized them in Tableau. Following the guiding questions, we first take a look at how casual riders differ from annual members in the following: 

### Types of Bikes Used in 2022

### Total Trips by Month, Day, and Time of Day

### Ride length by month, day of week, and time of day






