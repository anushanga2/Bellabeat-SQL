# Case Study: Bellabeat Fitness Data Analysis
 Bellabeat is a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market. This project analyzes smart device fitness data to unlock new growth opportunities for the company.

## Business Task  
Urška Sršen who is one of the founders of Bellabeat, would like an anlysis of Bellabeat's consumer data to identify opportunities for growth. This data includes data on physical activity, heart rate and sleep monitoring. She would like to gain insights about how people are using smart devices, and recommendations for how these trends can be utilised to make decisions on the marketign strategy for Bellabeat. The primary stakeholders in this case study are Urška Sršen and Sando Mur, executive team members. The major business task statement that will be explored in this case study is as follows,  
*  _Development of a comprehensive market analysis and strategy to identify and capitalize on new growth opportunities for Bellabeat._ 

## About the data  
The data for this case study is from [Fitbit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit), a public domain dataset on Kaggle. This dataset has been generated by respondents to a survery via Amazon Mechanical Turk between 2016/12/03 and 2016/12/05. The limitations of this data set is as follows,
* The number of users in the data set is 30. However, the users across sleep, weight, and daily activity varies across the data set. The n_distinct() function provides 33 distinct usuers for daily activity, 8 users for weight and 24 users for sleep. This amount of data will not follow the central limit throrem.
* The dataset is also limited and lacks information such as demographic data, and user information such as gender, age, or location.  
* For the 8 users for weight, 5 users have entered the data manulally and 3 via connected wifi device.

## Data Preparation    
The initial data cleaning and preparation was conducted using the Bigquery SQL and Google Sheets. These steps involve checking for inconsistencies of data sets with respect to verify overlappings, blank spaces, duplicates, etc. For the analysis in Bigquerry SQL, the datetime columns were processed to gain time specified insights. The Bigquerry SQL querries used for the analysis are attached as a seperate file in the Bellabeat Case Study folder.  For the stastical and graphical visualization in R, the following methods were followed.   
1. Load Packages  
```
install.packages("tidyverse")  
install.packages("ggplot2")  
install.packages("waffle")
install.packages("plotly")
library(tidyverse)  
library(lubridate)  
library(dplyr)  
library(ggplot2)  
library(tidyr)  
library(RColorBrewer)
library(plotly)
   
```
2. Load the Dataset and assign names
```
activity <- read.csv("Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")  
intensities <- read.csv("Fitabase Data 4.12.16-5.12.16/hourlyIntensities_merged.csv")  
sleep <- read.csv("Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")  
calories <- read.csv("Fitabase Data 4.12.16-5.12.16/hourlyCalories_merged.csv")  
weight <- read.csv("Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
hourly_steps <- read.csv("Fitabase Data 4.12.16-5.12.16/hourlySteps_merged.csv")
sleep <- unique(sleep)
activity <- rename_with(activity, tolower)  
sleep <- rename_with(sleep, tolower)  
hourly_steps <- rename_with(hourly_steps, tolower)  
intensities <- rename_with(intensities, tolower)  
calories <- rename_with(calories, tolower)
weight <- rename_with(weight, tolower)

```  
3. Fix Formats  
```
intensities$activityhour=as.POSIXct(intensities$activityhour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
intensities$time <- format(intensities$activityhour, format = "%H:%M:%S")
intensities$date <- format(intensities$activityhour, format = "%m/%d/%y")
sleep$sleepday=as.POSIXct(sleep$sleepday, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
sleep$date <- format(sleep$sleepday, format = "%m/%d/%y")
calories$activityhour=as.POSIXct(calories$activityhour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
calories$time <- format(calories$activityhour, format = "%H:%M:%S")
calories$date <- format(calories$activityhour, format = "%m/%d/%y")
activity$activitydate=as.POSIXct(activity$activitydate, format="%m/%d/%Y", tz=Sys.timezone())
activity$date <- format(activity$activitydate, format = "%m/%d/%y")
activity <- activity %>% mutate( weekday = weekdays(as.Date(activitydate, "%m/%d/%Y")))
hourly_steps <- hourly_steps %>% 
  rename(date_time = activityhour) %>% 
  mutate(date_time = as.POSIXct(date_time, format ="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone()))
merged_activity_sleep <- merge(activity, sleep, by=c("id","date"), all.x = TRUE)
merged_data_id <- merge(merged_activity_sleep, weight, by = c("id"), all=TRUE)
```
4. Calculating the number of participants for each data set
```
n_distinct(activity$Id)  
n_distinct(calories$Id)
n_distinct(sleep$Id)  
n_distinct(intensities$Id)    
n_distinct(weight$Id)  
```

## Analyze  
1. Calculating he summary statistics
  ```
activity %>%  
    select(totalsteps,
           totaldistance,
           sedentaryminutes, calories) %>%
    summary()
 
activity %>%
    select(veryactiveminutes, fairlyactiveminutes, lightlyactiveminutes) %>%
    summary()

weight %>%
    select(weightkg, bmi) %>%
    summary()

calories %>%
    select(calories) %>%
    summary()

sleep %>%
  select(totalsleeprecords, totalminutesasleep, totaltimeinbed) %>%
  summary()

```  
  The summary statistics for each data set is as follows,    
* On aveage users take about 7,638 steps per day and the total average walking distance of users is around 5.49 kms. Also, the amount of average calory burn by users is around 2304 calories. This amount is in line with the [recommended amount of calory burn for women](https://www.healthline.com/health/fitness-exercise/how-many-calories-do-i-burn-a-day) which falls between 1,600-2,200 calories per day. However, the recommended daily steps to be walked is below the [recomended levels](https://www.medicalnewstoday.com/articles/325809) of 10,000. One of the possible reasons for this could be most of the users of Bellabeat having healthy BMI levels (Median:24.39) which is between [recommended range](https://www.healthline.com/nutrition/bmi-for-women) of 18.5-24.9. However, the average BMI value of the users is 25.19 which falls on the overweight range. With the assumption of all the data are from adults, the average number of hours slept by users per day is calculated as 6.98 hours which is close to the [recommended sleep hour range](https://www.mayoclinic.org/healthy-lifestyle/adult-health/expert-answers/how-many-hours-of-sleep-are-enough/faq-20057898) for adults.

2. Relationship between variables
To identify the correlations between variables, following plots were obtained from R.  
* Daily steps vs Calories  
 ```
ggplot(merged_activity_sleep,aes(totalsteps,calories))+geom_jitter(alpha=.5)+
    geom_rug(position="jitter", size=.08)+geom_smooth(size =.6)+
    labs(title= "Daily steps vs. Calories", x= "daily steps", y="calories")+
    theme_minimal()
```  
![Daily steps Vs Calories](https://github.com/anushanga2/Bellabeat-Fitness-Tracker-Case-Study/assets/142766981/c85a65c1-9a8d-49e8-8fb9-24e01390a229)  

The above graph provides a correlation coefficient of 0.6 between Total steps and Calories, which represents a moderate positive relationship between the variables. 
* Daily steps vs Sleep
```
ggplot(data=subset(merged_activity_sleep,!is.na(totalminutesasleep)),aes(totalsteps,totalminutesasleep))+
    geom_rug(position="jitter", size=.08)+geom_jitter(alpha=0.5)+geom_smooth(color= "blue", size =.6)+
    labs(title= "Daily steps vs. Sleep", x= "daily steps", y="minutes asleep")+
    theme_minimal()
```
![Daily steps Vs Sleep](https://github.com/anushanga2/Bellabeat-Fitness-Tracker-Case-Study/assets/142766981/545d169e-fe7c-4ec8-bcd2-652866d0edac)  
The above graph provides a correlation coefficient of -0.2 between Total steps and Minutes asleep, which represents a low negative relationship between the variables. 
* Sedentary minutues vs Sleep
```
ggplot(data=subset(merged_activity_sleep,!is.na(sedentaryminutes)),aes(sedentaryminutes,totalminutesasleep))+
    geom_rug(position="jitter", size=.08)+geom_jitter(alpha=0.5)+geom_smooth(color= "blue", size =.6)+
    labs(title= "Sedentary minutes vs. Sleep", x= "Sedentary minutes", y="minutes asleep")+
    theme_minimal()
```  
The above graph provides a correlation coefficient of -0.6 between Sedentary minutes and Total minutes asleep, which represents a moderate negative relationship between the variables.  
* Percentage of time segments

```
perc_veryactive<-sum(activity$veryactiveminutes)/(sum(activity$veryactiveminutes)+sum(activity$fairlyactiveminutes)+sum(activity$lightlyactiveminutes)+sum(activity$sedentaryminutes))
perc_lightlyactive<sum(activity$lightlyactiveminutes)/(sum(activity$veryactiveminutes)+sum(activity$fairlyactiveminutes)+sum(activity$lightlyactiveminutes)+sum(activity$sedentaryminutes))
perc_fairlyactive<-sum(activity$sedentaryminutes)/(sum(activity$veryactiveminutes)+sum(activity$fairlyactiveminutes)+sum(activity$lightlyactiveminutes)+sum(activity$sedentaryminutes))
perc_sedentary<-sum(activity$sedentaryminutes)/(sum(activity$veryactiveminutes)+sum(activity$fairlyactiveminutes)+sum(activity$lightlyactiveminutes)+sum(activity$sedentaryminutes))
percentage <- data.frame(
    level=c("Sedentary", "Lightly", "Fairly", "Very Active"),
    minutes=c(perc_sedentary,perc_lightlyactive,perc_fairlyactive,perc_veryactive))
plot_ly(percentage, labels = ~level, values = ~minutes, type = 'pie',textposition = 'outside',textinfo = 'label+percent') %>%
  layout(title = 'Activity Level Minutes',
         xaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
         yaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE))
plot_ly(percentage, labels = ~level, values = ~minutes, type = 'pie',textposition = 'outside',textinfo = 'label+percent') %>%
  layout(title = 'Percentage activity levels',
         xaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
         yaxis = list(showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE))

```





