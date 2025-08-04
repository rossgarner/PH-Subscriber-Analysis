# PH-Subscriber-Analysis

Following on from our TAM Analysis of the Smart subscribers, I want to ask the question if the users Smart is acquiring for NBA League Pass are new, or if they are repeat customers, or if they are somewhat cannibalizing NBA League Pass direct signups

To start we use the SQL query in the attached file

SELECT FAN_ID, LPSUBSFLAG,ORDER_FISCAL_YEAR, ORDER_DATE_EST, ACCOUNT_CREATED_DATE_EST, (ORDER_DATE_EST = ACCOUNT_CREATED_DATE_EST) AS dates_match
FROM DB_DBTDEV.DBT_LKHATRI.SUBSCRIPTION__FCT_ORDERS_BY_OPIN_PARTNERS
WHERE COUNTRY_NAME = 'PHILIPPINES' AND OPIN_PARTNER = 'SMART'
GROUP BY FAN_ID, LPSUBSFLAG, ORDER_FISCAL_YEAR, ORDER_DATE_EST, ACCOUNT_CREATED_DATE_EST
ORDER BY ORDER_FISCAL_YEAR DESC

This allows us to pull the PLSUBSFLAG which determines if the user was a prior paying subscriber, a new subscriber, or a promo code winback subsriber - and also the relevant dates of when they ordered, when they created their account and if those two column dates match

If a user created an account on the same day they "ordered" NBA League Pass, logic dictates they would be assigned a new subscriber flag on LPSUBSFLAG

Here's a preview of the results set

<img width="1257" height="175" alt="image" src="https://github.com/user-attachments/assets/b3e9f7ca-27b6-4ff6-ac98-02187c015cf7" />


We can also run this query to get a quick summary of the issue
SELECT
    LPSUBSFLAG,
    (ORDER_DATE_EST = ACCOUNT_CREATED_DATE_EST) AS dates_match,
    COUNT(*) AS total_count
FROM
    DB_DBTDEV.DBT_LKHATRI.SUBSCRIPTION__FCT_ORDERS_BY_OPIN_PARTNERS
WHERE
    COUNTRY_NAME = 'PHILIPPINES'
    AND OPIN_PARTNER = 'SMART'
GROUP BY
    LPSUBSFLAG,
    dates_match
ORDER BY
    LPSUBSFLAG,
    dates_match DESC;


With this results set

<img width="990" height="204" alt="image" src="https://github.com/user-attachments/assets/7cd73cc0-3878-4031-b476-0a2054927463" />

Now we can already see that most of the subscribers generated from our LP partner Smart is actually from previously paying subscirbers that we acquired on NBA.com or NBA App, so this partnership in its current form is basically cannibalizing our user base, although on a small scale

Next with the data exported, we shall continue our analysis in Pyhton to see what we can determine

After cleaning the data we can start to perform some EDA

<img width="1057" height="643" alt="image" src="https://github.com/user-attachments/assets/43ebf467-8123-4079-b1ac-52f7b9c8a2c2" />

Straight away we can see the issue of prior subs vs new subs being generated from this distribution partnership with around 70% of users being prior subs

<img width="1184" height="409" alt="image" src="https://github.com/user-attachments/assets/49cea11d-977b-4a70-9cf4-2d471b9ef6ab" />

Next we can explore that trend over the three fiscal years we have data for to determine if this sales channel is moving in the right direction or not

<img width="1096" height="559" alt="image" src="https://github.com/user-attachments/assets/9757d6d3-1a36-40ca-bcd4-cf7567e4defa" />

<img width="1145" height="702" alt="image" src="https://github.com/user-attachments/assets/89be80b1-fede-4b1a-99b7-85cca54bd191" />

Probably easier if we visualize this in a line chart to see the trend over time more easily

<img width="932" height="498" alt="image" src="https://github.com/user-attachments/assets/f3f9be57-0204-479a-9e2c-6baecfbc926c" />
<img width="1074" height="722" alt="image" src="https://github.com/user-attachments/assets/bf415669-9732-413b-8b21-7817745e6a5b" />

So we can see that whilst over the last 3 years 71% of subs acquired through this channel are prior subs, that trend is decreasing 

The next step is to figure out purchase frequency over the years by subscribers and each suscriber type

First we can group individual FAN IDs by their LPSUBSFLAG and the value count to take a look at how many subs flag there is per user

<img width="1057" height="430" alt="image" src="https://github.com/user-attachments/assets/5a82b6fa-48cd-462c-9655-44d3138f6d60" />

Ideally all subs should follow the example of FAN ID 10955, at one point they are a new subscriber and in a subsequent year they are identified as a prior subscriber. However, we do have cases eg. FAN ID 158628 where they are classified as a prior subscriber only, and have 2 counts. This is because they were a subscriber at a point in time before this data set starts recording so every subsequent purchase they are a prior subscriber. 

Next we can start be analyzing the purchase frequency of users

<img width="888" height="591" alt="image" src="https://github.com/user-attachments/assets/e4692061-2987-4c8f-9940-257c4bd76847" />

Next let's filter and visualize for 2025 

<img width="1188" height="331" alt="image" src="https://github.com/user-attachments/assets/971fe8a2-cc89-4177-9aad-e6623b085721" />

<img width="1170" height="704" alt="image" src="https://github.com/user-attachments/assets/ff4e50b7-0ea2-43fd-98e5-be1460f07206" />

So far we can see that in 2025 (and all three years) most of the subscribers are prior paying subscribers, and most only purchase once during an NBA season. (we could analyze date of purchase but we know from all of our data that users purchase during NBA Playoffs and here its no different). 

These are the two problems we have for this partnership: (1) lack of scale since we don't have new customers coming in, (2) lack of repeat purchase during a season. Since we don't have scale, we need repeat purchases to grow but we lack that also. 

We we create the same visualization for 2023 we start to notice some odd things

<img width="1084" height="701" alt="image" src="https://github.com/user-attachments/assets/eee34745-a13e-4a17-88c8-9649d1df039b" />

There are users with an unusually high purchase frequency!

<img width="802" height="500" alt="image" src="https://github.com/user-attachments/assets/34b4d66b-5ca9-4793-8393-1249d9be5db9" />

Describing the data we can see the max purchase is 100, which makes no sense

First we can identify the users with purchase frequency more than 10

<img width="710" height="654" alt="image" src="https://github.com/user-attachments/assets/a69718eb-5df3-4d13-8922-d017d4e607ca" />

If these users followed the same behaviour in subsequent years we could think its valid but we already saw from 2025 that there are no outliers
When we check those FAN IDs in 2024 and 2025, only two of those fans continued purchasing and at a smaller frequency

<img width="897" height="291" alt="image" src="https://github.com/user-attachments/assets/ebd7a287-a1c6-4b17-a7e9-6807dbd36b8d" />

Since there are only two, we decide to remove all of the 10 users as outliers and clean the dataset again
<img width="1200" height="486" alt="image" src="https://github.com/user-attachments/assets/2f293bf3-27d2-4330-b8b1-7a43d3e9b7da" />

We rebuild the dataset with the cleaned data

<img width="607" height="585" alt="image" src="https://github.com/user-attachments/assets/33a76cda-5c09-487c-8e28-153d1edca453" />

Next we can visualize the purchase frequency vs the subscriber type

<img width="1288" height="717" alt="image" src="https://github.com/user-attachments/assets/0ac7ecee-ed68-4ce1-bca7-e12e227e9924" />

Taking 2025 as the example year we identify the following trends:

1. Most users purchase once during the season
2. New subscribers purchase once 80%+ (totally makes sense)
3. Prior subscribers from previous years also purchase once during the season (60%+ of the time)
4. Less than 20% of prior subs purchases more than once during the season

The next question - are prior subs making repeat purchases across the seasons? 

<img width="1183" height="668" alt="image" src="https://github.com/user-attachments/assets/80af3480-7d8d-4329-927e-5b73d9ef34d9" />

<img width="1269" height="689" alt="image" src="https://github.com/user-attachments/assets/18595e83-bf18-4f09-b2db-e8f9fb4bfb5a" />

First we define the years active and max purchases
Years active is the unique fiscal year of the order count
Max purchases is the count of the FAN ID 

Toe build the visualization we - 

<img width="1092" height="632" alt="image" src="https://github.com/user-attachments/assets/cf6905a8-f606-49f8-8e73-7fcd5f23ab7f" />

Then - 

<img width="808" height="297" alt="image" src="https://github.com/user-attachments/assets/c45d909c-6965-4235-9f2a-d571d1037e6d" />

<img width="1176" height="584" alt="image" src="https://github.com/user-attachments/assets/c2c96e43-48ee-45d7-af90-17a6a213633a" />

So again here the main takeaway is that repeat users "prior subs" are purchasing only once per year. There are only a few users in that classification that purchase within a year, or make repeat purchases across years (defined as years active > 1)

