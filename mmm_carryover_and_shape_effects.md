---
title: "Carryover"
permalink: /carryover_and_shape_effects/
categories: mmm causality marketing
---

# Paper Review
## Bayesian Methods for Media Mix Modeling with Carryover and Shape Effects 

* Paper Authors: Yuxue Jin, Yueqing Wang, Yunting Sun, David Chan, Jim Koehler
* [Paper Link](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/46001.pdf)

### Overview

This paper is mainly concerned with two common issues in MMM: lagged effects (caryoer) and diminishing returns (shape effects). It offers a few functions to model this phenomenon as well as bayesian model specification. Finally, it provides a way to simulate data as a means to compare model results to the ground truth as well as a overview of how to calculate ROAS, mROAS and optimize future media spend.

### Carryover Effect (Lagged Impact)

Caryover affect, often called lagged effect, is modeled through what is called an adstock function. 

* Adstock function was coined by Simon Broadbent and attempts to measure how an advertiser's media spend accumulates and decays overtime. 

The paper models this two ways: 

**Geometric Function**

* This is basically a weighted average going back L days, where L can vary by media channel. 
* Here the effect has the largest impact on the day of spend/impression and decays after. 
* I imagine the more transactional a channel is the quicker the decay. Additionally, the lead time a consumer takes to make a purchase decision e.g. (buying a house takes months so the L is longer and decay is slower) impacts the function parameters. 

**Delayed Adstock (radial kernel basis function)**  

* The effect of media spend spikes several days after media spend.
* I imagine this would be a better function to channels that are upper funnel i.e. brining the need and/or product to the consumers mind. 

**Other Functions** 

* Negative binomial density function has also been used. 

#### Code Implementation

```python
def geoDecay(alpha, L):
    '''
    weighted average with geometric decay
    
    weight_T = alpha ^ T-1 
    
    returns: weights of length L to calculate weighted averages with. 
    '''
    return alpha**(np.ones(L).cumsum()-1)

def delayed_adstock(alpha, theta, L):
        '''
    weighted average with dealyed adstock function
    
    weight_T = 
    
    returns: weights of length L to calculate weighted averages with. 
    '''
    return alpha**((np.ones(L).cumsum()-1)-theta)**2

def carryover(x, alpha, L, theta = None, func='geo'):
    '''
    1. x is a vector of media spend going back L timeslots, so it should be len(x) == L
    
    2. Weights is a vector of length L showing how much previous time periods spend has on current period. 
    
    3. L is max length of Lag.
    
    returns transformed vector of spend
    
    # update with numpy average 
    # np.average(x[:2], weights=[1,.9])
    '''
    transformed_x = []
    if func=='geo':
        weights = geoDecay(alpha, L)
        
    elif func=='delayed':
        weights = delayed_adstock(alpha, theta, L)
    
    for t in range(x.shape[0]):
        upper_window = t+1
        lower_window = max(0,upper_window-L)
        current_window_x = x[:upper_window]
        t_in_window = len(current_window_x)
        if t < L:
            new_x = (current_window_x*np.flip(weights[:t_in_window], axis=0)).sum()
            transformed_x.append(new_x/weights[:t_in_window].sum())
        elif t >= L:
            current_window_x = x[upper_window-L:upper_window]
            ext_weights = np.flip(weights, axis=0) # np.concatenate([np.zeros(1+t-L), np.flip(weights, axis=0)])
            new_x = (current_window_x*ext_weights).sum()
            transformed_x.append(new_x/ext_weights.sum())
            
    return np.array(transformed_x)
 ```
 
### Adstock Functions
 
 ![adstock_functions](https://i.imgur.com/44omR43.png)
 
Parameter Desctiption
* alpha
* theta
* functional forms. 
 
### Transformed Timeseries with Geometric and Delayed Adstock functions 
 
 ![geometric](https://i.imgur.com/IHiELKx.png)
 ![delayed](https://i.imgur.com/SckKLoj.png)
 
 Commentary Here
 
### Shape Effect / Diminishing Returns

To model the shape effect of advertising, the media spend needs to be transformed through a curvature function. A candidate for such a curvature function is the Hill function, which has its origins in pharmacology (Gesztelyi et al., 2012; Hill, 1910), where it was used as an empirical receptor model

Parameters: bHill

* S = Slope ; greater than 0
* K = Half Saturation Point ; greater than 0
* beta = Coefficient for channel ; greater than 0
* Hill = 1 / 1+ (spend / K)^-S

Notes: In original function, ass x goes to infinity the function approaches 1. Therefore a beta coefficient is introduced.

However, we have noticed that the parameters of βHill are essentially unidentifiable in some scenarios

Another scenario is when K is outside the range of observed media spend,(blue) In this case, the observed spend is assumed to be in the range (0, 1), and the two curves are nearly identical within that range, but diverge outside it.

The poor identifiability of the βHill function makes it very challenging to estimate the parameters well with any statistical method. Alternatively, we could fit a more parsimonious functional form, such as the one in (6), referred to as reach transformation hereafter, which is equivalent to fixing S = 1 in estimating the Hill function.

Definition. (Identifiability, Pearl 2013) The causal effect of X on Y is identifiable if the quantity Pr(y | xˇ) can be computed uniquely from any positive probability of the observed variables that is compatible with the diagram.

Identifiability means that, given an arbitrarily large sample from the joint distribution described by the causal diagram, the causal effect Pr(y | xˇ) can be determined.

(Wikipedia) In statistics, identifiability is a property which a model must satisfy in order for precise inference to be possible. A model is identifiable if it is theoretically possible to learn the true values of this model's underlying parameters after obtaining an infinite number of observations from it. Mathematically, this is equivalent to saying that different values of the parameters must generate different probability distributions of the observable variables. Usually the model is identifiable only under certain technical restrictions, in which case the set of these requirements is called the identification conditions.

Other Functional Forms

* Sigmoid function (also referred to as the logistic function),
* or the integral of other probability distributions such as the normal distribution.
* Another alternative is monotonic regression splines.


```python
def hill(x, S, K):
    return (1/1+(x/K)**(-S))

def beta_hill(x, S, K, beta):
    return beta - (K**S*beta)/(x**S+K**S)
```

![hill transformations](https://i.imgur.com/v30CyLy.png)


### Combining: Carryover and Shape / Lag and Diminishing Returns

Two possible approaches:

We could first apply the adstock transformation to the time series of media spend, and then apply the shape transformation.

First apply shape transformation and then adstock.

If media spend in each time period is relatively small compared to the cumulative spend across multiple time periods, the shape effect in each time period is less obvious compared to that of cumulative media spend.

In this case, we would prefer to apply the shape transformation after the adstock transformation
However, if the media spend is heavily concentrated in some single time periods with an on-and-off pattern, the latter choice might be preferable


### Simulation 

Next we move onto simulation. Simulation is always important when when looking to extract causality from observational studies. This way we know the ground truth and how far off our models predictions are.

**Simulated Dataset** 

* Two Years of Sales Data
* Three Marketing Channels 
    * Media variables are generated by adding white noise to a sinusoidal seasonality with one year as a period;
    * Scaled between 0 and 1 for convinence. 
* One Control Variable (Price)
    * Price is generated via an AR(1) series. 


```python
# media channels 
N = 102 # number of data points
t = np.linspace(0, 4*np.pi, N)
data = 3+np.sin(t+0.001) + 0.5 + np.random.randn(N)
media_1 = ((data-min(data))/(max(data)-min(data)) ) 

t = np.linspace(0, 4*np.pi, N)
data = 3+np.sin(t+0.001) + 0.5 + np.random.randn(N)
media_2 = ((data-min(data))/(max(data)-min(data)) )  

t = np.linspace(0, 4*np.pi, N)
data = 3+np.sin(t+0.001) + 0.5 + np.random.randn(N)
media_3 = ((data-min(data))/(max(data)-min(data)) )  

# price 
from statsmodels.tsa import arima_process as arima

arparams = np.array([.7, .6])
maparams = np.array([.1, .02])
ar = np.r_[1, arparams] # add zero-lag and negate
ma = np.r_[1, maparams] 
```

