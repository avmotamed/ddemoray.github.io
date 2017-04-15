---
title:  "Case Study: SQL Queries & Geospatial Visualization"
date:   2017-04-13
categories: sql, python, basemap
---
I recently completed a case study as a second round interview for a startup based in NYC.  The Objective was to confirm my ability to effectively query a SQL database and also show my ability to create a dataframe in Python and plot pertinent population data geospatially.

If you're interested in looking under the hood you can see the code I used to complete the challenge [here](https://github.com/ddemoray/GA-DSI-projects/blob/master/capstone/Technical%20Case%20Study%20-%20SQL%20Queries%2C%20Python%20Dataframe%20%26%20Python%20Geo-Spatial%20Visualization.ipynb).

## First up, SQL queries  

Querying a SQL database and utilizing basic aggregate functions are fairly straightforward tasks.  One could easily learn how to complete these queries through any of a number of free online tutorials. Window Functions aren't as straightforward. A more advanced feature of SQL, Window Functions allow you to perform aggregate functions on a subset of data and often allow you to do so in a way that optimizes computational performance.  One might utilize a Window Function in SQL when looking to calculate a moving average over a specific time frame.

One query in this case study was employed to: "calculate the percentage size of the city relative to the city with the largest population in the state."

<pre>
SELECT states.name, cities.name, cities.population, MAX(population)
    OVER (PARTITION BY state_id) AS max_state_population,
    (CAST(population AS float) / (MAX(population)
    OVER (PARTITION BY state_id))*100)
FROM cities
JOIN states ON states.id = cities.state_id
ORDER BY states.name;
</pre>

## Python Dataframe and geospatial visualization


