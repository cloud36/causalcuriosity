---
title: "Causality Overview"
permalink: /causality_overview/
categories: causality machine_learning statistics
---

# Causality from observational data

### Correlation != Causation

But, why?

I'd say there are three layers of why this isn't true:

1) Obvious rejections where two variables are highly correlated but one doesn't cause the other.  
2) [Simpson's paradox](https://en.wikipedia.org/wiki/Simpson%27s_paradox) This mistake is than much easier to make and set's the stage for why we'd need to the need for extreme care when looking at what seems to be obvious 
3) When we are trying to take action in an environment and want to undertand the impact (causal effect) of that action. 

### Frameworks

Two major, and complimentory, frameworks for studying causality have emerged over several decades. The Rubin Causal Framework and Pearl's causal graph framework. In a nutshell, the Rubin method treats causality as a missing data problem i.e. estaimting the counterfactual. 

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
