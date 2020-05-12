---
title: "Bias Correction For Paid Search In Media Mix Modeling"
permalink: /bias_correction_paid_search/
categories: mmm causality
---

# Paper Review / Summary

## Bias Correction For Paid Search In Media Mix Modeling
### Authors: Aiyou Chen, David Chan, Mike Perry, Yuxue Jin, Yunting Sun, Yueqing Wang, Jim Koehler
[Paper Link](https://arxiv.org/pdf/1807.03292.pdf)

## Overview

This paper looks at the problem of endogeneity in media mix modeling (MMM), specifically with paid search. 

**Endogeneity:** This term is used in econometrics and refers to an explanatory variable that is correlated with the error term. Put differently, endogeneity is when the independent variables (assumed to be exogenous in OLS) are actually correlated and impacting each other.  

This paper uses Pearl's back-door criteria to control for the bias that arised from endogeneity.

In MMM, endogeneity is a particular problem because ad-targeting and spend are driven by a common underlying cause e.g. demand. For example, a customer may only visit a website if they are interested in a product and by visiting that website may activate ad retargeting. Therefore, consumer demand is an unobserved variable driving both the sale and retargeting. 

### Pearl's Causal Theory

To continue the paper summarizes Pearl's causal thoery. 

The overarching idea behind Pearl's causal theory is the use of graphical models, which allow us to describe causal relationships and encode the data generating process. Additionally, it allows us to test for conditional independence between variables using d-separation.

Below, are some useful definitions: 

**D-Separation**: A path between two nodes in a graph is considered to be d(directed)-seperated by another set of variables Z if either of the following criteria are meant:

1. The path contains a chain or a fork: x -> z -> y or x <- z -> y.
2. The path contains an inverted fork: x -> zz <- y where z is not in zz nor in any of its descendants. 

**Back-door Criteria**:  Given a causal diagram, a set of variables Z satisfies the
back-door criterion relative to an ordered pair of variables (X, Y ) in the diagram if: 

1. no node in Z is a descendant of X; and 
2. Z “blocks” every path between X and Y that contains an arrow into X.

Below, we look at an example:

![endo](https://i.imgur.com/6TvRq0P.png)

If we fit an OLS regression (Y = Beta_0 + X * Beta_1 + error) using data generated from the causal diagram above, we would have a biased estimate of Beta_1. This is because X and the error term are correlated as the Z variable influences X and the impact on Y is absorbed by the error term as we aren't yet specifically controlling for Z.

## Search Causal Diagram

Moving forward, the paper lays out a simple scenario / causal diagram for search. In this simple scenario, we imagine that sales is impacted by search advertising while all other advertising can be ignored. 

The problem can be modeled as: Sales = baseline + beta_1 * search_spend + error, where beta_2 can be thought of the ROAS (return on ad spend) for search. 

Here endogeneity arises as consumer demand effects both paid search clicks and organic search clicks, so these two variables are correlated, meaning search_spend is correlated with the error term. 

e.g.  sales = baseline + beta_1 * search_spend + error_organic_seasrch + error_all_else; COV(search_spend, error_organic_seasrch) > 0. 

Below, we depict this in a causal diagram. 

[!search_diagram](https://i.imgur.com/OoTJFcs.png)

Above, we can see Search Queries V effects both beta_1 * search_spend and error_1. To control for this, we examine the graph to see if there is a set of variables that satisfies Pearl's back-door criteria. 

As we can see, "Search Queries V" satisfies this as:

1. "Search Queries V" is not a descendant of "search ad x"
2. "Search Queries V" blocks all paths between "search ad x" and "organic search" 

Based on this information and the knowledge that by controlling for "Search Queries V" we can come up with an unbiased estimate for beta_1. 

However, several issues still remain:

1. This is a simple scenario. 
2. How do we observe "search queries V"?
3. How should we estimate the model?

The paper goes on to address each of these issues in more detail, which I'll lead for the enthusiastic reader. However, the core idea relates to the use of causal graphs and back-door criteria for controlling for endogeneity. One interesting aspect as for issue 1 above, the authors wright as if the reader would have historical information for all searches, which is likely not the case, so a practitioner may need to rely on an outside vendor to obtain this information. 

