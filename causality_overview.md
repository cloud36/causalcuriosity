---
title: "Causality Overview"
permalink: /causality_overview/
categories: causality machine_learning statistics
---

# Causality from observational data

### Correlation != Causation

We've all heard the old adage "correlation does not equal causation", but how do erroneous results from this axiom bubble up in real life? 

I'd say we see this in three layers with increasing opaqueness.

1) Obvious instances where p-hacking results in spruious correlations i.e.  x is correlated with y, but clearly x is not causing y. Example: 
2) [Simpson's paradox](https://en.wikipedia.org/wiki/Simpson%27s_paradox) This mistake is much easier to make and often the correlation between x -> y can seem causal, however it is not. For example, admisssion rates for women ... they were applying to harder colleges. 
3) Finally, there is the do(x) calculus, where we attempt to estimate the value of y if we do(x). Here we must understand all confounding variables and have a solid theory of how to estimate the causal effect.

### Frameworks

Two major, and complimentory, frameworks for studying causality have emerged over the decades. The Rubin Causal Framework and Pearl's causal graph framework. In a nutshell, the Rubin method treats causality as a missing data problem i.e. estaimting the counterfactual. 

In my mind, the Pearlian graph methods are more about encoding domain knowledge as a way to verify Y is independent of X given the data. 

### Methods
* Naive
* Matching
* Propensity Scoring
** Trimming
** Covariat Imbalance
* Inverse Propensity Scoring
* Doubly Robust Propensity Scoring
* Counterfactual Risk Minimization
* Instrument Variables
* Latent Instrument Variables

### Concepts
* Ignorability
* Identifiability
* Covariate Imbalance 
* Back-door criteria
* Front-door adjustment

### Connection to Reinforcement Learning.

Personally, I originally became interesting in causal inference as a direct results of reinforcement learning. I had recently finished Udacity's Deep Reinforcement Learning nanodegree and had completed a few Coursera classes and watch David Silvers excellent Youtube series. Afterall this work, I came to the conclusion that if you didn't have a way to simulate or collect vast amounts of data it would be very hard to leverage RL methods. This lead me down the path of dreaming up ways to simulate complex, real world phenomenon, which lead to research in PGM (probablistic graphical models). After spending some brief time there, I found a few great resources on couterfactual machine learning and causal inference, which seemed to hit the sweet spot.

All that being said, there is quite a bit of overlap, with most being found in bandit problems (mutli-armed bandits, contextual bandits). The paper "Learning from Logged Bandits" is basically a concatenation of IPS and CABs. 


### Additional Resources

#### Blogs
#### Books 
#### Code 
