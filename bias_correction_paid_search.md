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

This paper looks at the problems with endogeneity in media mix modeling and specific paid search. 

**Endogeneity:** Is a term often used and econometrics and refers with an explantory variable is correlated with the error term. 

This can happen in a numbers of ways. For example, in the equation y = mu + x * beta + e there could be an unobserved variable that influences both x and e. Additionally, there x could be determined by y. We see equations for this below.

**Equations**

In paid search, endogeneity arises because both paid search and organic search are correlated as both are controled by an unobserved variable (search queries). We examine the relationship below. 

So, the question boils down to: how do we correct this bias.

In the paper, they employ causal graphical models to correct for this bias. A brief summary of CGM is provided below. 

