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

This paper looks at the problems with endogeneity in media mix modeling and specifically with paid search. 

**Endogeneity:** Is a term often used and econometrics and refers to with an explantory variable is correlated with the error term. Put differently, endogeneity is when the independent variables (assumed to be exogenous in OLS) as actually correlated and impacting each other. 

This paper uses Pearl back-door criteria to control for bias. 

In MMM, endogeneity is a particular problem becuase ad targeting and spend are driven by a common underlying cause i.e. demand. For example, a customer may only visit a website if they are interested in a product and visitng that website may activate ad retargeting. Therefore, underlying customer demand is a confounding variable if not accounted for when measuring ad effectivness. 

### Pearl's Causal Theory

A brief overview of Pearl's causal theory is provided. 

The overarching idea behind Pearl's causal theory is the use of graphical models. The use of graphical models has several benefits:

1. Use domain knowledge to encode causal effects.
2. Allows us to test for conditionl independence between variables using d-seperation.

**D-Seperation**: A path between two nodes in a graph is considered to be d(directed)-seperated by another set of variables Z if either of the following criteria are meant:

1. The path contains a chain or a fork: x -> z -> y or x <- z -> y.
2. The contains an inverted fork: x -> zz <- y where z is not in zz nor in any of its descendants. 

**Back-door Criteria**:  Given a causal diagram, a set of variables Z satisfies the
back-door criterion relative to an ordered pair of variables (X, Y ) in the diagram if: 

1. no node in Z is a descendant of X; and 
2. Z “blocks” every path between X and Y that contains an arrow into X.

Below, we look at an example:

![endo](https://i.imgur.com/6TvRq0P.png)

If we fit an OLS regression (Y = Beta0 + X Beta1 + error) using data generated from the causal diagram above, we would have a biased estimated of beta1. This is because X and the error term are correlated as the Z variable influences both. 

Rewriting the equations:

Y = B0 + X B1 + error
error = lambda * X + N
N = error - lambda * X 

Y = B0 + (lambda + B1) * X + N 

Quite a few equations above, so let's run some code to solidify: 

```
from causalgraphicalmodels import StructuralCausalModel
from causalgraphicalmodels import CausalGraphicalModel
import numpy as np

endo = StructuralCausalModel({
    "z": lambda     n_samples  : np.random.normal(0,1,size=n_samples),
    "x": lambda z, n_samples   : z + np.random.normal(0,1,size=n_samples)* np.random.normal(1.5,.1,size=n_samples),
    "y": lambda z, x, n_samples: ((z*np.random.normal(.5, .1) + x*np.random.normal(1.5, .1) + np.random.normal(0,.1,n_samples))>.7)+0,
})

data=endo.sample()

```
From the structural equation above, we know that X's unbiased effect on y is N(1.5,.1). 

Let's see what OLS gets us:

```

```


**Equations**

In paid search, endogeneity arises because both paid search and organic search are correlated as both are controled by an unobserved variable (search queries). We examine the relationship below. 


** Appendix


Historically, three main areas of research have been proposed to handle the problem within marketing:

* Propensity Scoring: Leveraging user-level data. 
* Counterfactual Prediction: Leveraging aggregate level campaign data. 
* Pseudo-randomness: Leveraging pseudorandomness in ad serving mechanisms. 


So, the question boils down to: how do we correct this bias.

In the paper, they employ causal graphical models to correct for this bias. A brief summary of CGM is provided below. 



We can see that x, y, z
