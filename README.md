# Airbnb-Rating-Analysis
Airbnb review ratings are important as they foster trust and transparency within the community, enabling guests to make informed decisions and holding hosts accountable for providing quality experiences. They offer valuable feedback for hosts, encourage improvements, and help establish credibility and reputation. Ratings influence pricing and search ranking, impacting hosts' bookings and revenue, while ultimately ensuring guest satisfaction, which is fundamental to the platform's success.

## Goal: Analysis on Airbnb stats to find out what are the factors that affect the rating of the Airbnb

![rainbow](https://github.com/Winxent/portfolio/assets/146320825/5dc438d2-e138-4db0-97a0-e5ae8c3473e8)

# Introduction
Review score ratings on platforms like Airbnb are important for several reasons:

1. Trust and Credibility: Reviews and ratings help establish trust between hosts and guests. When potential guests see positive reviews, they are more likely to trust the host and feel confident about booking a property. Conversely, negative reviews or low ratings can deter potential guests, raising concerns about the quality of the listing.

2. Informed Decision-Making: Reviews provide valuable information for potential guests. They can learn about the experiences of previous guests, including the cleanliness of the property, the accuracy of the listing description, the host's responsiveness, and more. This information allows guests to make informed decisions when choosing accommodations.

3. Quality Assurance: High ratings and positive reviews can serve as indicators of quality. Hosts who consistently receive positive feedback are more likely to attract a larger pool of potential guests. It also encourages hosts to maintain and improve their properties and services to maintain their good reputation.

5. Continuous Improvement: Reviews offer constructive feedback for hosts. They can learn from guests' comments and suggestions to enhance their listings and provide better experiences. This feedback loop can lead to a continuous improvement in the quality of accommodations.

9. Community Building: Airbnb and similar platforms foster a sense of community among hosts and guests. Reviews and ratings are a way for community members to communicate and share their experiences, contributing to a sense of trust and shared responsibility.

In summary, review scores and ratings on platforms like Airbnb are important because they build trust, provide valuable information for decision-making, ensure quality, hold hosts accountable, encourage improvement, and enhance the overall experience for both hosts and guests. They play a central role in the success and credibility of the platform.

![rainbow](https://github.com/Winxent/portfolio/assets/146320825/5dc438d2-e138-4db0-97a0-e5ae8c3473e8)

# Datasets

![image2](https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/0d6e5dc3-38c6-425f-b1ec-428630369aa8)

There are 30321 rows, 11 columns. There are Column types of both categorical and numerical and they provide us the information about the Airbnb details such as their Hosted period, neighborhood, property type, room type, price, and most importantly the review scores rating. by using these data we can use data analysis to comeout with insights and understanding. But first of all we need to clean the raw data.

Raw Datasets:https://docs.google.com/spreadsheets/d/1O0iTVeiRXVc0ocy3vAIMzvwDGt5vVODN/edit?usp=sharing&ouid=107402225492318840480&rtpof=true&sd=true

![rainbow](https://github.com/Winxent/portfolio/assets/146320825/5dc438d2-e138-4db0-97a0-e5ae8c3473e8)

# Data Cleaning using Google BigQuery
The below checklist is done for data cleaning using google BigQuery:
* Dataset: airbnb
* Naming our table: rating

##### A – Remove duplicate rows
1. By typing the below quote in the query statement we can find how many rows we have.
```
select count(*) from airbnb.rating 
```
There are total or 30478 rows

2. In order to find duplicates value:

 ```  
with host_idcte as
(
select *, row_number() over(partition by host_id,name,Host_Since,Neighbourhood_,Property_Type,Zipcode,Room_Type,beds,price,Room_Type,
Number_of_Records,Number_Of_Reviews,Review_Scores_Rating,Review_Scores_Rating__bin_ order by host_id) as row_num
from airbnb.rating
)
select * from host_idcte where row_num >1
```
17  duplicate row found

3. Count distinct rows: 
```
select count(*)
from
(select distinct * from airbnb.rating)
```
30461  distinct rows

4. Drop duplicate by creating a new table with only distinct rows
```   
CREATE OR REPLACE TABLE airbnb.rating1
AS
SELECT
distinct*
FROM airbnb.rating;
drop table airbnb.rating;
ALTER TABLE airbnb.rating1 RENAME TO rating;
```

##### B – Handle missing values
1. check null values:
```   
select * from airbnb.rating
where host_since is null
or property_type is null
or Review_Scores_Rating__bin_ is null
or Zipcode is null
or beds is null
or Review_Scores_Rating is null
```
8454 rows have null values
Other columns have no null values

2. Host_since and property_type is an important data for comparison, any missing value will be drop.
```
DELETE FROM airbnb.rating
WHERE host_since IS NULL;
```
```
DELETE FROM airbnb.rating
WHERE Property_Type IS NULL;
```
3. Missing value on review rating should be remove in order for us to do analysis
```
DELETE FROM airbnb.rating
WHERE Review_Scores_Rating IS NULL;
```
4. Missing  value for beds will be replace with 0 (impute)
```
update airbnb.rating
set beds = 0
where beds is null
```
5. Missing values for zipcode will be deleted to prevent data being unable to compared
```
DELETE FROM airbnb.rating
WHERE zipcode IS NULL;
```

##### C – Correct data formats
no issue on the data type

<img width="956" alt="image4" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/4150fd1b-be08-4732-88b9-0ba43381d246">

##### D – Drop irrelevant columns
Since there has already one rating column, rating_bin can be removed
```
ALTER TABLE airbnb.rating
DROP COLUMN review_scores_rating_bin;
```

##### E – Fix inconsistent data entry
There is no inconsistent data entry

##### F – Trim whitespaces
Trimming some white spaces trim function in google bigquery
```
update airbnb.rating
set name = trim(name)
where true
```

##### G – Correct spelling errors
no wrong spelling

##### H – Correct numerical errors
no numerical errors

after data cleaning:
![image8](https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/8f117987-9494-4d40-8013-703366f014b2)
https://drive.google.com/file/d/1DQq4wXDu7o6ElNkNMDfGRbXdoFE3BBm8/view?usp=sharing

![rainbow](https://github.com/Winxent/portfolio/assets/146320825/5dc438d2-e138-4db0-97a0-e5ae8c3473e8)

# Data Analysis (google Sheet)
Data descriptive analysis strategies are used to analyse the dataset provided: 

## 1. Data Aggregation:
It helps describe the data, and generate insight from the characteristic of the data, 
A customer might want to look into the hospitality of the airbnb in terms of review scoring, price, number of beds  and data should aggregate based on host.

## 2. Summary Statistic:
Summarized the large datasets into insightful numbers and gist of information about the data,
We can understand the general situation, make decisions and monitor the changes.
there are 3 types of summary statistics:

### I. Measures of location:
Mean (Average of a data set), Median (middle value of the data set), Mode (most repeated number)

### II. Measures of spread:
To understand the spread and distribution of data. and to find outliers.
### III. Graphics and charts:
Dash board.

# Below are the SQL queries and their Solutions for the Data Analysis:
## Data Description:
In order to have a better data description we usually check the Shape and Size of our dataset along with the general description of datasets such as count, unique values etc.

### 1. Check the Size of dataset
```
SELECT count (column_name)
FROM portfolio-401502.airbnb.INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'rating';
```
This showed we had 9 columns
```
SELECT count (host_id)
FROM airbnb.rating; 
```
22050 Rows in the dataset

### 2. Check the metadata / Description of dataset
```
SELECT table_name, column_name, is_nullable, data_type, is_partitioning_column
FROM portfolio-401502.airbnb.INFORMATION_SCHEMA.COLUMNS
WHERE table_name = 'rating';
```
<img width="956" alt="image4" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/1b6b2193-f82b-48be-a1fb-83e72074b18d">

### 3. Check the subset of observations recorded in the dataset 
```
select * from airbnb.rating limit 10;
```
![image5](https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/9e50eaf7-7c7d-42a2-abf1-fc021e65f2ee)

##  Data Aggregation 
It helps us understand the data trends and values based on the compact display of values

### 1. Check the unique values  alongwith the count of values for categorical columns

#### Neighbourhood:
```
select Neighbourhood,count(Neighbourhood)as count
from airbnb.rating
group by rollup (Neighbourhood)
order by count desc ;
```
<img width="383" alt="image15" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/fdafd6e0-eab4-4429-a436-24c932f73f18">

#### Property:
```
select Property_Type,count(Property_Type)as count
from airbnb.rating
group by rollup (Property_Type)
order by count desc ;
```
<img width="292" alt="image13" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/daae8ff9-311f-4edf-b00c-a21af9c9a13e">

#### Room Type:
```
select Room_Type,count(Room_Type)as count
from airbnb.rating3
group by rollup (Room_Type)
order by count desc ;
```
<img width="353" alt="image26" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/92ad4973-64b3-4ff5-8064-8bc5d2a87388">

### 2. check the min and max of numerical columns 
```
SELECT min(beds) as beds_min, max(beds) as beds_max,
min(Number_Of_Reviews) as Number_of_reviews_min, max(Number_Of_Reviews) as Number_of_reviews_max,
min(Price) as price_min, max(Price) as price_max,
min(Review_Scores_Rating) as Review_Scores_Rating_min, max(Review_Scores_Rating) as Review_Scores_Rating_max
FROM airbnb.rating;
```
![image11](https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/910fb52f-879e-4308-b36d-dcfbf015e99a)

## Data Summary 
Summaries of data help us understand the detailed trends followed in datasets based on concise information using measures of location and spread 

### 1. Summarize by mean values
```
select avg(beds) as Mean_Beds, avg(number_of_Reviews) as Mean_number_of_reviews, avg(price) as Mean_price,
avg(review_scores_rating) as Mean_review_scores_Rating from airbnb.rating3;
```
<img width="683" alt="image25" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/9608cc93-fb31-40db-b143-b670d683b805">
Average price $155
Average rating 92*

### 2. Summarize by median
```
select PERCENTILE_CONT(beds,0.5) OVER() AS median_beds , PERCENTILE_CONT(Number_Of_Reviews,0.5) OVER() AS median_number_of_reviews,
PERCENTILE_CONT(Price,0.5) OVER() AS median_price, PERCENTILE_CONT(Review_Scores_Rating,0.5) OVER() AS median_review_scores_rating
from airbnb.rating3 limit 1;
```
<img width="683" alt="image25" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/555dabb4-3bc6-4c72-a949-45fa7c3b97aa">
Median price $125
Median rating 94*

### 3. Summarize by mode 
```
SELECT
APPROX_TOP_COUNT(beds,1) AS mode_beds, APPROX_TOP_COUNT(number_of_reviews,1) AS mode_number_of_reviews,
APPROX_TOP_COUNT(price,1) AS mode_price, APPROX_TOP_COUNT(Review_Scores_Rating,1) AS mode_review_scores_rating
from airbnb.rating3 limit 1
```
<img width="970" alt="image10" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/b079d84a-9d27-45b9-855e-cf78069fd448">
Most of the Airbnb has only 1 bed with only 1 review.
Majority of Airbnb the price is $150.
Majority of Airbnb the rating is 100 perfect score.

### 4. Stddev 
```
SELECT
STDDEV(review_scores_rating) AS standard_deviation_review_scores_rating
FROM airbnb.rating3;
```
<img width="328" alt="image6" src="https://github.com/Winxent/Airbnb-Rating-Analysis/assets/146320825/211307e7-816a-4029-b19d-71a0cbd4473c">
Low deviation







