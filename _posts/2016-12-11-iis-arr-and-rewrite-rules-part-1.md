---
layout: post
published: false
title: 'IIS, ARR & Rewrite Rules (Part 1)'
subtitle: ARR is hard...
date: '2016-12-11'
tags:
  - dotnet
  - iis
  - arr
  - rewrite
  - setup
bigimg: /img/dotnet-core-structuremap.jpg
---
The project I have been working on at TJG is to add a routing layer into out Recruiter website to enable the 
the platform to become more agile.

We have previously accomplished this by creating a reverse proxy with ARR and Rewrite rules to allow us to send certain requests to independant apps - these can then be released and evolve on their own. The problem I have been facing is dealing with a legacy/aging platform that has evolved of the years. This approach is still correct because:
  - The routing is an additive process (we shouldn't have to modify existing code - however this might be a little naive)
  - We can test the routing layer for specific brands (single site of our portfolio) without affecting the other sites
  - We should be able to easily flip back/undo the routing at the load balancer level (super safe)
  
Dealing with rewrite rules in IIS can be super painful due to the lack of debugging that is available - however I will try and address this in later post.
