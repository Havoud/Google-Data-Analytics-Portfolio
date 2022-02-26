# Google Data Analytics Capstone (Cyclistic Project)

### by Hamed Davoudabadi

## Scenario
You are a junior data analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. As a result, the director of marketing believes the company’s future success depends on maximizing the number of annual memberships. So, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members.

In order to do that, however, the marketing analyst team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. The director of marketing and her team are interested in analyzing the Cyclistic historical bike trip data to identify trends.

## 1) Ask
Three questions will guide the future marketing program:
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

#### Asking about Cyclistic and explaining the problem
* Cyclistic is a bike-share program based in Chicago that features more than 5,824 bicycles and 692 docking stations. 
* There are some flexibilities in its pricing plans: single-ride passes, full-day passes, and annual memberships. 
* Customers who purchase single-ride or full-day passes are referred to as casual riders. 
* Customers who purchase annual memberships are Cyclistic members. 
* Their finance analysts have concluded that annual members are much more profitable than casual riders. 
* Also, converting casual riders into annual members is their main goal.

#### What should we do?
So, in this project we have to find the differences between casual riders and annual members in using Cyclistic bikes. When we understand the differences, we can design a new strategy to convert casual riders to annual members.

## 2) Prepare
In the preparation phase I have collected some information about the data I am going to work with:
* The datasets are collected by Cyclistic. 
* They contain Cyclistic’s monthly trip data.
* I have used the data that happened in 2021 year. 
* The data contains information about location and time of using City of Chicago’s Divvy bicycle-sharing service.
* I didn’t find any bias with data.
* I’ll be using the first-party data. So, it is original, reliable, and cited.
* This is a public data set.
* The data has been prepared by Divvy Bikes under this [Data License Agreement](https://www.divvybikes.com/data-license-agreement).

## 3) Process
The phase of processing data was started by choosing R for working with data and I have done the actions mentioned before to get ready for analyzing.
1. I have used these four libraries:
```
library(tidyverse)
library(skimr)
library(janitor)
library(lubridate)
```
2. Initially, I opened all 12 months data to check which they exact columns.
3. Since, the data was separated by month, I merged all of twelve different months into one with name df.
```
month_1 <- read_csv("202110-divvy-tripdata/202101-divvy-tripdata.csv")
month_2 <- read_csv("202110-divvy-tripdata/202102-divvy-tripdata.csv")
month_3 <- read_csv("202110-divvy-tripdata/202103-divvy-tripdata.csv")
month_4 <- read_csv("202110-divvy-tripdata/202104-divvy-tripdata.csv")
month_5 <- read_csv("202110-divvy-tripdata/202105-divvy-tripdata.csv")
month_6 <- read_csv("202110-divvy-tripdata/202106-divvy-tripdata.csv")
month_7 <- read_csv("202110-divvy-tripdata/202107-divvy-tripdata.csv")
month_8 <- read_csv("202110-divvy-tripdata/202108-divvy-tripdata.csv")
month_9 <- read_csv("202110-divvy-tripdata/202109-divvy-tripdata.csv")
month_10 <- read_csv("202110-divvy-tripdata/202110-divvy-tripdata.csv")
month_11 <- read_csv("202110-divvy-tripdata/202111-divvy-tripdata.csv")
month_12 <- read_csv("202110-divvy-tripdata/202112-divvy-tripdata.csv")

df <- bind_rows(month_1, month_2, month_3, month_4, month_5, month_6, month_7, month_8, month_9, month_10, month_11, month_12)
```
4. Used `colnames(df)` to see the attributes:
>      [1] "ride_id"            "rideable_type"      "started_at"         "ended_at"           "start_station_name“ 
>      [6] "start_station_id"   "end_station_name"   "end_station_id"     "start_lat"          "start_lng"         
>      [11] "end_lat“
5. I summed total number of rows in 12 separated files and compared to the merged file, to be matched: **5,595,063 rows**.
6. Then, queried several ride_ids randomly to verify that the data is proper after merging.
7. Deleted all the rows which had the same ride_id.
8. Added a new variable named trip_duration which displays the difference between starting and ending the trip.
9. Deleted the rows which had ending time before starting time.
10. Checked for legit values and spelling errors across rideable_type, start_station_name, end_station_name, member_casual columns.
11. Found no null values in important columns. i.e, ride_id, started_at, ended_at, member_casual
12. There were so many rows which they did not have start_station_id or end_station_id and even the names of the stations were empty. I used the start_lat/start_lng and end_lat/end_lng which were the geographical addresses of the stations for making a new ID for all stations with NA values in their ID columns.
13. used trim to clean double spaces strings.


