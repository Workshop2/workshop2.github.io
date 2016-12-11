---
layout: post
published: false
title: 'IIS, ARR & Rewrite Rules (Part 1 - Cookies and Legacy System)'
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

We have previously accomplished this by creating a [Reverse Proxy](https://en.wikipedia.org/wiki/Reverse_proxy) with [ARR](https://www.iis.net/downloads/microsoft/application-request-routing) and [Rewrite Rules](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module) to allow us to send certain requests to independant apps - these can then be released and evolve on their own. The problem I have been facing is dealing with a legacy/aging platform that has evolved of the years. This approach is still correct because:
  - The routing is an additive process (we shouldn't have to modify existing code - however this might be a little naive)
  - We can test the routing layer for specific brands (single site of our portfolio) without affecting the other sites
  - We should be able to easily flip back/undo the routing at the load balancer level (super safe)
  
Dealing with rewrite rules in IIS can be super painful due to the lack of debugging that is available - however I will try and address this in later post.

Today I want to talk about **Cookies and Legacy Systems**.

# ARR + Cookies + Legacy System = Mess
To give some background as to why I am having to mess with cookies I am going to have to point to the past and say _"I genuinely don't know why or how this happened, I am just having to deal with it"_. 

The recruiter side of TJG stores it's cookies against **totaljobs.com** instead of **recruiter.totaljobs.com** - but why does this matter?

## Domains matter
On my first attempt of trying to get my routing layer deployed we started recieving many bugs and errors on the website - we were sensible and turned off the routing layer to ensure no other customer was being hurt. I had tested my routing layer over and over locally, in INT and in PAT - WHO DID THIS HAPPEN?

Well, when testing internally I always started with a fresh browser with no cookies and everything worked :thumbsup:. In the real world many customers currently have their account cookies all set and when we flipped they found they were getting logged out with the error **"Someone else is already logged in"** - my routing layer was for some reason setting all the cookies with to the sub-domain instead of the TLD.

Our customers now had duplicate cookies under both **totaljobs.com** and **recruiter.totaljobs.com** - eek. Luckily they just had to clode their bowsers, clear their cookies or wait a few hour for the cookies to expire and everything was ok.

## Solving the problem
There are years of code and legacy behind how domains and stored and read - I am not here to fix all the problems. I thought about it and talked to many people and in the end we decided to ensure cookies are set correctly while the new routing layer is in place.

Rewrite Rules allow you to define rules in in-bound traffic as well as out-bound traffic which allows us to modify content and header of HTTP responses.

The problem I needed to solve was: **"To ensure the domain is set for all _Set-Cookie_ headers to the TLD."**

I quickly identified the following **Set-Cookie** situations I needed to solve:
  - No domain is set in the header
  - The wrong domain is set in the header
  
### Solving problemo 1

















