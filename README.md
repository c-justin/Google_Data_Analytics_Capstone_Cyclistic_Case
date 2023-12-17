# Google Data Analytics Capstone Cyclistic Case

## Introduction

In this case study, I will be taking on the role of a junior data analyst at the fictional company, Cyclistic. To answer the key business questions, I will be following the steps of the data analysis process: **ask, prepare, analyze, share, and act**. 

Source Data: [Here](https://divvy-tripdata.s3.amazonaws.com/index.html) [Last accessed 12/16/2023]

## Case Summary
### Cyclistic
Established in 2016, Cyclistic has become a successful bike-share program with a fleet of over 5,800 bicycles and 600 docking stations. What sets Cyclistic apart is its dedication to inclusivity, offering diverse bike options like reclining bikes, hand tricycles, and cargo bikes to accommodate people with disabilities who cannot use standard two-wheeled bikes. While the majority of riders prefer traditional bikes, approximately 8% opt for assistive options. Despite the primary focus on leisurely rides, a notable 30% of users utilize bikes for daily commuting.

Lily Moreno, the director of marketing and manager, plays a crucial role in developing campaigns and initiatives through channels such as email and social media. Cyclistic's previous marketing strategy emphasized building general awareness and appealing to broad consumer segments, featuring flexible single-ride, full-day, and annual memberships. Recognizing the profitability of annual memberships over casual riders, Moreno aims to convert casual riders, already acquainted with Cyclistic, into long-term members. The goal is clear: design marketing strategies focused on converting casual riders into annual members. To achieve this, the marketing analyst team, including myself, is tasked with understanding the distinctions between annual members and casual riders, exploring why casual riders would choose membership, and assessing the impact of digital media on marketing tactics.

## Ask 

- [x] Business Task
How to convert more casual riders into annual members by designing a specific and targeted marketing strategy. 

Guiding questions
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

## Prepare
### Data Source
The [data source](https://divvy-tripdata.s3.amazonaws.com/index.html) for the analysis is Cyclistic's historical trip data spanning from January 2022 to December 2022. This data has been provided by Motivate International Inc. under a specific [license](https://divvybikes.com/data-license-agreement). It is considered public data and is being utilized to gain insights into the usage patterns of Cyclistic's bike ride-sharing service among customers.

However, there are certain limitations to be acknowledged. Due to data privacy concerns, the analysis cannot incorporate personally identifiable information of riders. Consequently, it is not possible to link pass purchases to credit card numbers. This limitation means that the analysis won't be able to determine whether casual riders reside within Cyclistic's service area or if they have made multiple single-pass purchases. 

### Data Organization
I will utilize 12 csv files with the following naming convention: YYYYMM-divvy-tripdata. Each file includes the fields: ride_id, rideable_type, started_at, ended_at, start_station_name, start_station_id, end_station_name, end_station_id, start_lat, start_lng, end_lat, end_lng, and member_casual. 

## Process
Given the worksheet limitation of 1,048,576 rows in Microsoft Excel we will be using BigQuery to clean and analyze the data which contains over 5.6 million rows. 

### Combining the Data
To merge the 12 CSV files, I employed the batch upload operation in BigQuery. Initially, I uploaded all the files to a Google Cloud Storage bucket named gc_cyclist_data. Subsequently, I utilized the wildcard function to upload and append the content of the 12 CSV files into a single table named [2022_trip_data_raw](https://console.cloud.google.com/bigquery?hl=en&project=voltaic-spider-405020&ws=!1m5!1m4!4m3!1svoltaic-spider-405020!2sCyclist_Data!3s2022_trip_data_raw). This approach was feasible due to the consistent schema across all files.
