---
title: "Bias Correction For Paid Search In Media Mix Modeling"
permalink: /bias_correction_paid_search/
categories: mmm causality
---

# Paper Review / Summary

## Bias Correction For Paid Search In Media Mix Modeling
### Authors: 
[Paper Link](https://arxiv.org/pdf/1807.03292.pdf)

## Overview

This paper looks at the problems with endogeneity in media mix modeling and specifically with paid search. 

**Endogeneity:** Is a term often used and econometrics and refers to with an explantory variable is correlated with the error term. Put differently, endogeneity is when the independent variables (assumed to be exogenous in OLS) as actually correlated and impacting each other. Take the graph below for example:

![endo](https://i.imgur.com/vbq36FX.png)

We can see that x, y, z

This paper uses Pearl back-door criteria to control for bias. 

In MMM, endogeneity is a particular problem becuase ad targeting and spend is driven by a common underlying cause i.e. demand. We can think of this as Z in the graph above. 


**Equations**

In paid search, endogeneity arises because both paid search and organic search are correlated as both are controled by an unobserved variable (search queries). We examine the relationship below. 

![search_endo]()

So, the question boils down to: how do we correct this bias.

In the paper, they employ causal graphical models to correct for this bias. A brief summary of CGM is provided below. 

