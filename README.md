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
month_1 <- read_csv("202101-divvy-tripdata.csv")
month_2 <- read_csv("202102-divvy-tripdata.csv")
month_3 <- read_csv("202103-divvy-tripdata.csv")
month_4 <- read_csv("202104-divvy-tripdata.csv")
month_5 <- read_csv("202105-divvy-tripdata.csv")
month_6 <- read_csv("202106-divvy-tripdata.csv")
month_7 <- read_csv("202107-divvy-tripdata.csv")
month_8 <- read_csv("202108-divvy-tripdata.csv")
month_9 <- read_csv("202109-divvy-tripdata.csv")
month_10 <- read_csv("202110-divvy-tripdata.csv")
month_11 <- read_csv("202111-divvy-tripdata.csv")
month_12 <- read_csv("202112-divvy-tripdata.csv")

df <- bind_rows(month_1, month_2, month_3, month_4, month_5, month_6, month_7, month_8, month_9, month_10, month_11, month_12)
```
4. Used `colnames(df)` to see the attributes:
>      [1] "ride_id"            "rideable_type"      "started_at"         "ended_at"           "start_station_name“ 
>      [6] "start_station_id"   "end_station_name"   "end_station_id"     "start_lat"          "start_lng"         
>      [11] "end_lat“
5. I summed total number of rows in 12 separated files and compared to the merged file, to be matched: **5,595,063 rows**.
6. Then, took several queries of ride_ids randomly to verify that the data is proper after merging. For example: the ride_id in row 96835th of df should be the same as the first ride_id in month_2. So, we can write: ` nth(month_2$ride_id, 1) == nth(df$ride_id, 96835) ` and the answer should be ` True `
7. checked how many types of user and bikes are there in *member_casual and *rideable_type accordingly:

```
unique(df$member_casual)
unique(df$rideable_type)

```
which its results were:
>      "member" "casual" 
>      "electric_bike" "classic_bike"  "docked_bike"  

8. The number of NA in every column was counted (using **map()** from **puur** library)
```
map(df, ~sum(is.na(.)))
```
The result would be 739170 NAs in *start_station_id* and 690806 NAs un *start_station_name*    
> $ride_id
> 
> [1] 0
> 
> $rideable_type
> 
> [1] 0
> 
> $started_at
> 
> [1] 0
> 
> $ended_at
> 
> [1] 0
> 
> $start_station_name
> 
> [1] 690809
> 
> $start_station_id
> 
> [1] 690806
> 
> $end_station_name
> 
> [1] 739170
> 
> $end_station_id
> 
> [1] 739170
> 
> $start_lat
> 
> [1] 0
> 
> $start_lng
> 
> [1] 0
> 
> $end_lat
> 
> [1] 4771
> 
> $end_lng
> 
> [1] 4771
> 
> $member_casual
> 
> [1] 0


9. Deleted duplicate records of the same **ride_id field** (0 records was deleted)
```
df <- distinct(df, ride_id, .keep_all=TRUE) 
```
10. Counting number of stations with different **start_station_id**, which there were 848 unique stations.
```
NROW(unique(df$start_station_name))
```
11. Separating *start_at to make new variables like **year**, **month**, **day**, **day_of_week**, and **time_of_day**
```
tripdata_2021 <- tripdata_2021 %>%  
  separate(
    started_at, 
    into = c("year", "month", "day"), 
    sep = "-", 
    remove = FALSE) %>%
  separate(
    day, 
    into = c("day_of_week", "time_of_day"), 
    sep = " ",
    remove = FALSE)
```
12. which we continued to edit **day**, **day_of_week** and **time_of_day** and also I changed **month** from number to characters with their names:
```
df <- df %>%  
  mutate(
    month = factor(month.name[as.numeric(month)], levels = month.name, ordered = TRUE),
    day_of_week = wday(
      as.Date(df$started_at), 
      label = TRUE, 
      abbr = FALSE), 
    day = as.numeric(str_sub(day, 1, 2)),
    time_of_day = hms(time_of_day),)
```
13. Added a new variable named **trip_duration** which displays the difference between starting and ending the trip and also filtering the positive ones:
```
df <- df %>%
  mutate(trip_duration = difftime(ended_at, started_at, units = "mins")) %>%
  filter(trip_duration > 0)
```
14. we can also round the ride duration to two decimals:
```
df <- df %>%
  round(df$ride_length, 2)
```
15.Deleted the records which did not have 1 min <trip duration< 24 hrs:
```
df <- df[!(df$trip_duration<1 | df$trip_duration>1440),]
 ```
16. Also deleted the rows with missing values in important variables:
```
tripdata_v3 <- tripdata_2021[!(is.na(tripdata_2021$start_station_id) | 
                is.na(tripdata_2021$end_station_id) | is.na(tripdata_2021$ride_id) | 
                is.na(tripdata_2021$rideable_type) | is.na(tripdata_2021$started_at) | 
                is.na(tripdata_2021$ended_at) | is.na(tripdata_2021$end_lat) | 
                is.na(tripdata_2021$end_lng)),]
```
17. Counting number of NA in every column again, just for checking that we do not have NA before analyzing
```
map(df, ~sum(is.na(.)))
```


At first, the dataset had **5,595,063** records. After cleaning, **4,527,659** records remained.

## 4) Analyze

I had to run some queries to find some basic information about the dataset and main variables. Then I checked important variables like *casual_member* or timing of the rides for more consideration and recognition of differences between casual and annual members trips.
1. I started by descriptive analysis on the duration of rides and computed the mean, median, max and minimum of ride duration for annual members and casual users:
```
summary(df$trip_duration)
mean_user_duration   <- aggregate(df$trip_duration ~ df$member_casual, FUN = mean)
median_user_duration <- aggregate(df$trip_duration ~ df$member_casual, FUN = median)
max_user_duration    <- aggregate(df$trip_duration ~ df$member_casual, FUN = max)
min_user_duration    <- aggregate(df$trip_duration ~ df$member_casual, FUN = min)
```
2. Then, calculated the average ride time by each day for members and casual users:
```
user_day_duration <- aggregate(df$trip_duration ~ df$member_casual + df$day_of_week, FUN = mean)
```
3. Days of the week has been sorted 
df$day_of_week <- ordered(df$day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))

4. Calculated the total number of rides for each day, considering annual members and casual users:
```
 df %>% 
                           mutate(weekday = wday(started_at, label = TRUE)) %>%  
                           group_by(member_casual, weekday) %>%  
                           summarise(number_of_rides = n(), average_duration = mean(trip_duration)) %>%    
                           arrange(member_casual, weekday) %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) + geom_col(position = "dodge") +
  labs(
    title = "Daily number of trips in 2021",
    subtitle = "Based on user type: Member and Casual",
    x = "Day of week",
    y = "Number of trips",
    fill = "Member/Casual")
# making a plot from the table and saving it
ggsave("number of rides by member_casual.png", width=7, height=7)
```
![image](https://user-images.githubusercontent.com/63174846/155890481-f46e6d3b-fea5-4972-9ccd-05dffdeb25ab.png "Daily number of trips in 2021")

5. Calculated the average duration of rides for each day, considering annual members and casual users:
```
df %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
  group_by(member_casual, weekday) %>%  
  summarise(number_of_rides = n(), average_duration = mean(trip_duration)) %>%    
  arrange(member_casual, weekday) %>%   
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) + 
  geom_col(position = "dodge") +
  labs(
    title = "Daily average of trip duration in 2021",
    subtitle = "Based on user type: Member and Casual",
    x = "Day of week",
    y = "Average duration (min)",
    fill = "Member/Casual")
ggsave(duration of ride by member_casual.png", width=7, height=7)
```
![image](https://user-images.githubusercontent.com/63174846/155890553-9db13b0d-6913-4009-a20d-1c56b3445cdc.png "Daily average of trip duration in 2021")

6. Calculated the statistical summary of data for *rideable_type* variable
```
summary_rideable <- df %>%     
  group_by(rideable_type) %>%
  summarize(
    mean_trip_duration = mean(trip_duration),
    median_trip_duration = median(trip_duration),
    min_trip_duration = min(trip_duration),
    max_trip_duration = max(trip_duration),
    total_trips = n())
```
7. Analyzed, visualized, and saved the ridership data by member/casual and month
```
user_month_rides <- df %>%
  group_by(member_casual, month) %>%
  summarize(
    number_of_trips = n(),
    average_duration = mean(trip_duration),
    median_duration = median(trip_duration)
  ) %>%
  arrange(member_casual)

user_month_rides %>%
  ggplot(aes(month, number_of_trips, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(
    title = "The monthly number of trips based on member/casual",
    subtitle = "Based on user type: Member or Casual",
    x = "Month",
    y = "Number of trips",
    fill = "Member/Casual"
  ) +
  coord_flip()
ggsave("The monthly number of trips based on member_casual.png", width=7, height=7)

```
![image](https://user-images.githubusercontent.com/63174846/155890593-4d5e6903-0d82-474a-a223-3611254d5f8e.png "The monthly number of trips based on member/casual")

8. Visualized the monthly average duration by Member/Casual
```
user_month_rides %>%
  ggplot(aes(month, average_duration, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(
    title = "The monthly average duration based on Member/Casual",
    subtitle = "Based on user type: Member or Casual",
    x = "Month",
    y = "Average duration (min)",
    fill = "Member/Casual"
  ) +
  coord_flip()
ggsave("The monthly average duration based on member_casual.png", width=7, height=7)
```
![image](https://user-images.githubusercontent.com/63174846/155890618-7f7e0a54-1153-4f76-8167-070d775461e5.png "The monthly average duration based on Member/Casual")

9. Created a table of basic information
```
tripdata_merge %>%  
  group_by(member_casual) %>% 
  summarize(mean_total_duration = mean(trip_duration),max_total = max(trip_duration),
            min_total= min(trip_duration),  number_total = n())
```

#### I have also created three more tables for working in Tableau and having some other analyzes like showing top ten stations with most starting rides:

10. Exporting a table with total and average number of weekly rides by rider type
```
count_duration_user_day <- df %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
  group_by(member_casual, weekday) %>%  
  summarise(number_of_rides = n(), average_duration = mean(trip_duration)) %>%    
  arrange(member_casual, weekday)
write_csv(count_duration_user_day, "count_duration_user_day.csv", na = "NA", append = FALSE)
```
11. Exporting a table with total and average number of monthly rides by rider type
```
count_duration_user_month <- df %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
  group_by(month, member_casual) %>%  
  summarise(number_of_rides = n()
            ,average_duration = mean(trip_duration)) %>%    
  arrange(month, member_casual)
write_csv(count_duration_user_month, "count_duration_user_month.csv", na = "NA", append = FALSE)
```
12. Exporting a table containing the stations which were used the most by each user group
```
count_station_user_cord <- df %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
  group_by(start_station_name, member_casual,start_lat,start_lng) %>%  
  summarise(number_of_rides = n()) %>%    
  arrange(desc(number_of_rides))    #sort sescending

# Holding only top ten stations which most rides has been started from there:
count_station_user_cord <- count_station_user_cord %>% 
  arrange(desc(number_of_rides)) %>% 
  group_by(member_casual) %>% 
  slice(1:10)
write_csv(count_station_user_cord, "count_station_user_cord.csv", na = "NA", append = FALSE)
```

##### Outliers
It is also possible to find some outliers statistically from the dataset and remove them. 
Then, do the actions mentioned before in analyzing section again with the dataset which has no outliers. 
Removing outliers from a dataset sometimes needs a confirmation from the project manager.
```
# looking for outliers according to the trip duration. 
# The rides with duration of higher or lower than 85, and 15 percent of the whole data has been removed.
df %>% 
  df(month) %>%
  summarize(lmt = IQR(trip_duration),
    high_outliers = quantile[2] + 1.5 * iqr,
    down_outliers = quantile[1] - 1.5 * iqr) %>%
  select(month, down_outliers, iqr, high_outliers)
lmt <- IQR(df$trip_duration)
high_outliers <- quantile[2] + 1.5 * lmt
down_outliers <- quantile[1] - 1.5 * lmt
df %>%
  group_by(month) %>%
  summarize(total_n = n(),
    outliers = sum(trip_duration > high_outliers | trip_duration < down_outliers),
    percent = outliers/total_n) %>%
  arrange(desc(percent))

# remove outliers from dataset
df_no_outliers <- df %>%
  filter(trip_duration < high_outliers & trip_duration > down_outliers )
# Now it is possible to use this dataset instead of our main dataset (df).
```

## 5) Share
The average trip duration in each ride for casual members are more than 2 times higher compared to annual members for each weekday. For example, the day with the most average ride duration on the week is Sunday, which casual members ride ~38 minutes and annual members ride ~16 minutes.

The average trip duration for both members and casuals in weekends (Saturday and Sunday) are higher than the weekdays. Besides, this value for casual members is consistent through Tuesday to Thursday and gets a little higher on Monday and Friday, but for annual members, this value is almost consistent and similar in all of weekdays.

For comparing number of rides on weekdays and weekends for both annual members and casual users I have prepared a table named count_Weekend:
```
count_duration_weekend <- count_duration_user_day %>% 
    group_by(member_casual) %>% 
    summarise(weekend_sum = sum(number_of_rides[weekday == 'Sun' | weekday == 'Sat' ]),
              weekday_sum =sum(number_of_rides[weekday == 'Mon' | weekday == 'Tue' | 
              weekday == 'Wed' | weekday == 'Fri' | weekday == 'Thu' ]))
```
It's result would be:

| member_casual | weekend_sum | weekday_sum |
|:-------------:|:-----------:|:-----------:|
| casual        |  862668     |  1164011    |
| member        |  657027     |  1843953    |
 
Almost 43 percent of total rides for casual users has happened on weekends. For annual members the weekdays had higher total trip numbers in compared to weekends. For example, weekends had only 26 percent of member's rides. Also, highest total rides for casual users is Wednesday, but for members it is Saturday.

For casual users, June to September were the months with most total trip numbers, while for other months that value decreased extremely. For annual members, peak months are June to October and value reduction is not as extreme as for the casuals.

The most interesting point in average **ride duration** for different bike users regarding months was consistent value for annual members during year. It seems that these users have the same trips in all the year and although they could decide to not use the bike in winter, but they would not change their path or time of using. This value for casual users is higher for summer and late spring.

Results shows that there are 848 unique start station names in the data. I also, found the top ten stations which were being used as a start station for the whole rides, with respect to different users. It is possible to see the Tableau dashboard that I have created for top 10 starting to rides Cyclistic stations on 2021 from [here](https://havoud.github.io/Portfolio-for-Cyclistic-/). A regular png picture has been prepared for this report either:
![image](https://user-images.githubusercontent.com/63174846/155901309-ce670b1c-98e0-43bc-86ec-0b02e6e910f3.png)




## 6) Act
First, we can have some offers for companies or organization to encourage their employees to activate an annual membership in Cyclistic. Then we can design some campaigns to promote using Cyclictic bikes regularly in order to improving societies health and also saving environment form consuming fossil fuels. 

Regarding the top 10 stations which has been used more than others, it seems that the touristic locations in Chicago are the places with most usage of Cyclistic bikes. We can have more stations in this area and also we can have more bikes. 

Also, I would like to suggest the Cyclistic to collect some more variables in their future data, that could help planners more. For example, they can have some surveys which has the user age, or some questionaries which would ask the users: **in which stations they could not find bikes more than others? and when?** With respect to the age of users we can find out which is it possible that providing smaller bikes could encourage family members to use bikes together? Maybe if there were some bikes with smaller size, people would use bikes more than before because they can take their children to school and then they can go for work. In my idea, there are some people who would choose to cycle to their jobs in the whole year if they know their children would have a proper school service. 

I believe that end of May and first of June it would be a good time to have a campaign with some discount rates for absorbing casual members to be an annual member. Also, we can target students for enrollment at this period. Their school willbe closed and they are free for using bikes. 

As I have mentioned in sharing phase, the average trip duration for annual members during the different month of year does not change and it is a consistent value. It seems that if we can encourage people to use bikes for riding to their jobs, it would be more likely that they will use the bikes in the whole year. In other word, if we can eliminate some barriers like long distances from residential areas to bike stations, we could encourage people to use the bikes as an annual member for work trip destinations. Also, we have to realize which locations are the places with highes attraction in work trips. Then we can provide some new stations in there.


