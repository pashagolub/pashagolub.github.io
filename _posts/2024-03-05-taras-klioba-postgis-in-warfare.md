---
layout: post
title: How to use PostgreSQL for (military) geoanalytics tasks by Taras Klioba
description: |
    During the PGConf.EU 2023, my friend, colleague, and PostgreSQL Ukraine co-founder Taras Klioba delved into the fascinating intersection of geospatial analysis and modern warfare.
    He decided not to publish a video record of his talk for obvious reasons. But now, he has shared the essential details of his talk in the form of a blog article. I want to boost this blog post for all Planet Postgres readers; that's the primary purpose of the text you're reading.
date: 2024-03-05 13:00:00
tags:
  - postgis
  - pgconf
  - postgresql
  - personal
---

During the [PGConf.EU 2023](https://www.postgresql.eu/events/pgconfeu2023/schedule/session/4605-from-map-to-reality-using-postgis-in-warfare/), my friend, colleague, and PostgreSQL Ukraine co-founder [Taras Klioba](https://www.linkedin.com/in/kloba/) delved into the fascinating intersection of geospatial analysis and modern warfare.

He decided not to publish a video record of his talk for obvious reasons. But now, he has shared the essential details of his talk in the form of a [blog article](https://klioba.com/how-to-use-postgresql-for-military-geoanalytics-tasks). 
I want to boost this blog post for all Planet Postgres readers; that's the primary purpose of the text you're reading.

Taras's article discusses the importance of geoanalytics in military operations due to the prevalence of geospatial data, focusing on utilizing PostgreSQL to process such data. 
It addresses everyday geoanalytical tasks such as finding K-nearest objects, distance calculations, and point within a polygon determination. 
It aims to provide practical examples and tips. Open-source data, specifically russian military facility data from [OpenStreetMap](https://www.openstreetmap.org/), is used as a primary dataset. 
It is then imported into PostgreSQL using the [osm2pgsql](https://osm2pgsql.org/) tool for analysis and optimization.

Additionally, fire data from NASA satellites is introduced as a second data source, sourced from the [Fire Information for Resource Management System](https://www.earthdata.nasa.gov/learn/find-data/near-real-time/firms/vj114imgtdlnrt) (FIRMS). 
FIRMS allows real-time monitoring of active fires worldwide, including within russian military facilities' territory since 2022. 
The process involves downloading fire data using a specific script and importing it into a PostgreSQL database for further analysis.

For example, the text outlines a task of finding the K-nearest neighbors, precisely ten fires near the Shahed production plant in russia, known for manufacturing Iranian Shahed drones. 
It refers to previous [research by the Molfar team](https://molfar.com/blog/alabuga-deanon) for detailed information about the factory, located in the special economic zone Alabuga in Tatarstan. 
The text highlights the shift in factory priorities towards drone production following sanctions. 
It proposes a method involving creating a buffer around the target area and recursively expanding it until the desired results are obtained. 

Please, read and share!
Glory to Ukraine! 💙💛