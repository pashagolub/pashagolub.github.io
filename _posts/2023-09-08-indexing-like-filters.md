---
layout: post
title:  Indexing `LIKE` Filters
date:   2023-09-08 13:40:16
tags: 
- index
- FTS
- tips and tricks
- postgresql
- personal
---
[Markus Winand](https://postgresql.life/post/markus_winand/) wrote a great article about why and how users should use `LIKE` operator in their workloads. It covers different approaches like Full-Text Search, Partial Index, Trigram Index, Materialized View, and query optimization. In summary, the talk primarily addresses challenges related to optimizing queries with leading wildcard LIKE searches and provides various strategies and techniques to overcome these challenges and improve database query performance.
