---
title: "Causality Overview"
permalink: /causality_overview/
categories: causality machine_learning statistics
---

# Causality from observational data

### Correlation != Causation

We've all heard the old adage "correlation does not equal causation", but how do erroneous results from this axiom bubble up in real life? 

I'd say we see this in three layers with increasing opaqueness.

1. Obvious instances where p-hacking results in spurious correlations e.g. the number of storks and the [birth rate in Denmark](https://stats.stackexchange.com/questions/36/examples-for-teaching-correlation-does-not-mean-causation)
2. [Simpson's paradox](https://en.wikipedia.org/wiki/Simpson%27s_paradox) This mistake is much easier to make and often the correlation between x -> y can seem causal, however it is not. For example, there may seem like a causal link between a medecine and a positive outcome, however there may be a hidden variable that controls who gets the medicine and the outcome such as socioeconomic status. 
3. Finally, there is do(x) calculus, where we attempt to estimate the value of y if we do(x). Here, in the case of observational data, we must understand all confounding variables and have a solid theory of how to estimate the causal effect.

### Frameworks

Two major, and complimentary, frameworks for studying causality have emerged over the decades. The Rubin Causal Framework and Pearl's causal graph framework. In a nutshell, the Rubin method treats causality as a missing data problem i.e. estimating the counterfactual. Often, in this framework, it is assumed all of the confounding variables are accounted for. However, Pearl's framework is focused on representing the causal/generative structure of the data and examining ways we can control for confounding variables. 

### Methods
**Naive**  
* In the naive approach, we simply compare two averages (i.e. average where treatment = 1 and treatment = 0). When we are dealing with observational data, this will surely lead to biased results.   

**Matching** 
* In this approach, we compare the attributes of two individuals and we find the most similar subjects with different treatment groups and compare the effects of the treatment. 

**Propensity Scoring** 
* Here (if we don't know the assignment mechanism) we build an estimator that predicts treatment assignment, we then match treated/untreated subjects based on propsensity score and estimate teh effect. This method assumes treatment assignment is independent of covariates given the propesity score. 

**Inverse Propensity Scoring**
* This takes the idea of propesnity scoring a bit further. It aims to correct for imbalance between the treated and untreated groups. For example, subjects with the same propensity score likely have the same treatment, so when we see a subject who has the "counterfactual" treatment we weight their outcome higher as to create a pseudo-population. 

**Doubly Robust Propensity Scoring** 
* Here we combine a regression estimate with confounding variables and inverse propensity scoring. This results in a propensity-weighted regression. 

**Instrument Variables** 
* Instrument variables are usefule when we observe a variable X, but an unobserved variable is correlated with both X and the error term. The goal is to find a variable that is correlated with X but not the error term. This allows us to correct for the bias in the coeffecient in X. 

* Mediation

**Machine Learning Advances**
* Double Machine Learning
* Orthongonal Random Forests
* TARnet
* CFR (Counterfactual Regression)


### Concepts
* **Ignorability:** This means that any missing data is independent of the observed data. I imagine the condition of ignorability is only met if the treatment assignment is random. 

* **Identifiability:** is a property which a model must satisfy in order for precise inference to be possible.[](https://en.wikipedia.org/wiki/Identifiability).

**Covariate Imbalance:** This refers to an issue that arrises when the treatment assignment is non-random, therefore there is a covariate imbalance between the treated and untreated. 

**Back-door criteria:** Given a causal graph and variabls Z, the back-door criteria is meat if Z blocks every path from X->Y and Z is not a descendant of X. 

**Front-door adjustment** 

**ATE:** This stands for average treatment effect, it is what A/B tests measure. 

**ITE / CATE:** This stands for individual treatment effect or conditional treatment effect and looks to measure the treatment effect at the individual level. The average of all ITE within a population is the ATE. 


### Additional Resources

#### Blogs
#### Books 
#### Code 
