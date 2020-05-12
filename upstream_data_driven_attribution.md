---
title: "Toward Improving Digital Attribution Model Accuracy"
permalink: /udda/
categories: mmm causality mta
---

# Paper Review / Summary

## Toward Improving Digital Attribution Model Accuracy
**Paper Link**: https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45766.pdf

(in-progress)

This paper addresses the issue of multi-touch attribution (MTA). MTA is slightly different than media mix modeling (MMM) in that it aims to model ROAS based on user level click/impression data versus channel level spend data. 

Historically, two main forms of attribution have existed in MTA. 

1. Position Based Attribution (PBA)
2. Data-Driven Attribution (DDA)

PBA has been around the longest and is the simplest. The most widely used forms include first-click attribution and last-click attribution. To understand how this works, imagine all the ad-clicks a user takes before making a purchase 

* For example imagine the user path: paid search -> organic seo -> direct load)

In the above example, first-click attribution would assign 100% of the credit to paid search whereas last-click attribution would assign 100% to direct load. Of course both of these methods would yield incorrect results as every click along a path has some impact/value to a potential customer. More advanced forms of attribution exist as well i.e. linear and time-decay where linear just divides correct evenly amount touched media channels and time-decay imposes a time-decay. 

Data-driven attribution came about in the following iteration of attribution methods. To me these methods seem very similar to matching methods in causal inference. For example take the following two paths:

* Path 1: paid search -> social media -> direct load
* Path 2: paid search ->              -> direct load

Many data driven attribution methods may examine this as (paid search, direct load) as attributes describing the clickstrem and (social media) as the treatment. Therefore, comparing aggregate conversion rates of the two variations. Now, this has many problems:

1.
2. Time inbetween



Position Based Attribution (PBA): last-click, first-click, linear, time-decay. 
Data-Driven Attribution (DDA): Often proprietary, but based on leave-one out modeling.
Paper Solution: UDDA

Uses simulation to measure/stress test. 

Main agrument: impression clicks impact downstreasm actions, which DDA doesn't take into account. 


Issues not addressed in paper:
* Time inbetween. 
* Different products (for multi-product shops)
