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

1. Host_since and property_type is an important data for comparison, any missing value will be drop.
```
DELETE FROM airbnb.rating
WHERE host_since IS NULL;
```
```
DELETE FROM airbnb.rating
WHERE Property_Type IS NULL;
```
2. Missing value on review rating should be remove in order for us to do analysis
```
DELETE FROM airbnb.rating
WHERE Review_Scores_Rating IS NULL;
```
3. Missing  value for beds will be replace with 0 (impute)
```
update airbnb.rating
set beds = 0
where beds is null
```
4. Missing values for zipcode will be deleted to prevent data being unable to compared
```
DELETE FROM airbnb.rating
WHERE zipcode IS NULL;
```

##### C – Correct data formats
Change run_date to date time data type, others no issue

##### D – Drop irrelevant columns
No irrelevant column

##### E – Fix inconsistent data entry
No inconsistent data entry

##### F – Trim whitespaces
Trimming some white spaces using google clean-up suggestion

##### G – Correct spelling errors
no wrong spelling

##### H – Correct numerical errors
no numerical errors






