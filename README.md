# Case Study: Bellabeat Fitness Data Analysis
 Bellabeat is a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market. This project analyzes smart device fitness data to unlock new growth opportunities for the company.

## Business Task  
Urška Sršen who is one of the founders of Bellabeat, would like an anlysis of Bellabeat's consumer data to identify opportunities for growth. This data includes data on physical activity, heart rate and sleep monitoring. She would like to gain insights about how people are using smart devices, and recommendations for how these trends can be utilised to make decisions on the marketign strategy for Bellabeat. The primary stakeholders in this case study are Urška Sršen and Sando Mur, executive team members. The major business task statement that will be explored in this case study is as follows,  
*  _Development of a comprehensive market analysis and strategy to identify and capitalize on new growth opportunities for Bellabeat._ 

##About the data  
The data for this case study is from [Fitbit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit), a public domain dataset on Kaggle. This dataset has been generated by respondents to a survery via Amazon Mechanical Turk between 2016/12/03 and 2016/12/05. The limitations of this data set is as follows,
* The number of users in the data set is 30. However, the users across sleep, weight, and daily activity varies across the data set. The n_distinct() function provides 33 distinct usuers for daily activity, 8 users for weight and 24 users for sleep. This amount of data will not follow the central limit throrem.
* The dataset is also limited and lacks information such as demographic data, and user information such as gender, age, or location.  
* For the 8 users for weight, 5 users have entered the data manulally and 3 via connected wifi device.

## Data Preparation    
The initial data cleaning and preparation was conducted using the Bigquery SQL and Google Sheets. These steps involve checking for inconsistencies of data sets with respect to blank spaces, duplicates, etc. For the analysis in Bigquerry SQL, the datetime columns were processed to gain time specified insights. The Bigquerry SQL querries used for the analysis are attached as a seperate file in the Bellabeat Case Study folder.  For the stastical and graphical visualization in R, the following methods were followed.   
1. Load Packages  







