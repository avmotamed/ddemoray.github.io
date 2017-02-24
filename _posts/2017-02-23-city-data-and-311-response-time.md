---
layout: post
title:  "City Data and 311 Response Time"
date:   2017-02-23
categories: bio
---


Overview

I have long been intrigued by New York City's data transparency.  I set out to evaluate the city's non-emergency support services with the end goal of predicting the length of time to resolve any specific inquiry in Brooklyn using data from 2015.  My intent is to share this data with NY residents so they will have the proper expectations up front about how long it will take for their issue tobe addressed and also so that NYC agencies have more transparency into priotizing resource allocation across the city.


Findings

I was able to make a highly accurate predictions. I have some promising early results with models built using 2015 Brooklyn Data!


ETL, Cleaning & EDA

The city's 311 database is huge!  It comprised of over 8 Million rows, with 52 distinct columns of data ranging from government agency, complaints, to zip code and more!  In order to work with the data I went through a number of steps:
1.  I downloaded the data to my local computer from the city.
2.  Created a local MySQL database.
3.  Filtered out data by year (2015), location (NYC Metro Area) and resolved calls (I did not want to look at calls that were still in process of being resolved)
4. Specified columns that I thought were important for analysis.
5.  Pulled that into Python and sliced data to exclude values that did not have a borough specified, and then filtered out further by Brooklyn specific inquiries.

And then I did some feature engineering!

Feature Engineering

Using the existing CreatedDate feature I created four additional features: Month, Day of Week, Day of Month, and length (measured in days).  With these new features I was able to start looking for any interesting trends in the data.  

First I looked at the monthly volume of calls by Complaint Type:

(Insert Graphs Here)

At a high level this illustrates the monthly volume of calls for the top 4 most common complaints in Brooklyn during 2015.


Next I looked at the correlation between amount of time to resolve a complaint with the day of the week in which a call was originally placed:

(Insert Graphs Here)

Interestingly - calls logged earlier in the week tend to be resolved more quickly on average.  Also it's important to note that further analysis showed a high percentage of calls marked as being made on Monday have been input into the database with a 00:00 timestamp.  This leads me to believe that complaints made over the weekend may not get logged into the database until Monday when the office presumably has more staff on hand.

Next, I decided it would be interesting to look at response time by specific agencies across Brooklyn. Armed with this data government officials would be able to evaluate agencies that are taking a longer period of time to respond to complaints.  Below represents the average response time across agencies in Brooklyn:

(insert graph here)

**We can see there is a high variance in response time across these agencies. Initially I took a deeper look at DOITT which has the longest response time of around 16.5 days.  It turns out that this agency is responsible for maintaining payphone complaints - which are hardly a essential city service at this point.  I decided to pivot and focus on the response time of the top 10 most common complaints:

(insert graph here)

- We can see here that Unsanitary Conditions take the longest time to be resolved at around 13 days.  Let's dive deeper into the different descriptors that make up Unsanitary Conditions.

(insert graph)

Unsurpisingly, complaints categorized by the descriptor 'Pests' are the most common.

(insert graph)

AND unfortunately they also take the longest to be resolved.



(insert giph)

So this basically tells us that the dataset quantifies everything New Yorkers know about living in the city, down to latitude and longitude.  Moving on - let's continue our goal of predicting the amount of time it takes for a complaint to be marked as resolved.

Modeling

First up, feature selection.  I decided to limit my data to five features (descriptor, zip, day of week, month and day of month) with the target being number of days it takes for a specific complaint to be resolved (range of values anywhere from 1-31)

Given our dataset is not normally distributed and I'm using the scikit learn package, I decided to use a Random Forest classification model.  In order to use this model I dummified all my features and performed cross validation on the data I'm working with to avoid overfitting.  I instantiated my initial object with 100 trees.  

Initially my results were not too impressive.  I had an average accuracy of 53% - in other words my model predicted the proper resolution time (anwhere from 1-31 days) only 53% of the time.  If my data was normally distributed I presume that a regression model could work fairly well without needed to re-evaluate my target.  Given there was high variance across the target buckets I decided to group the target variable in buckets.  Using quantiles I was able to split out the target column based on the distribution of the data.

(insert graph)

This chart illustrates the distribution of the target data.  We can see that splitting up according to this distribution across three buckets works nicely.  

We can easily bucket all cases as being resolved in :
1.  1 Day or less
2.  Between 2-6 Days
3.  Between 7 - 31 Days

After encoding the target variables to the proper bucket I ran my model a second time and saw a huge improvement in accuracy.  This time I was able to predict the length of time for a complaint to be resolved with 80% accuracy.  For comparisons sake: if you were to randomly guess which of the three time windows a complaint would be resolved, you could guess correctly 43% of the time.  This model is almost 2x as accurate!

(Insert confident chart)


(consider uploading feature importance chart)

Conclusion:

The Data confirms the general intuition of what it means to live in NY.  From a resident lens, it quantifies the data in a way that allows more visibility into the short term realisty of how long a 311 complaint will be resolved.  From a public service perspective, this allows officials to get a zoomed out version of a large amount of data, and will allow those individuals to make important decisions about resource allocation.

These results are only the beginning!

Next Steps:
- I'd like to expand the model to other boroughs and make the response time visible to the public to proactively impact city services.