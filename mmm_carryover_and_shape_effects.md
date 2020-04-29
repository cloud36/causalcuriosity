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

This paper is mainly concerned with two common issues in MMM: lagged effects (carryoer) and diminishing returns (shape effects). It offers a few functions to model this phenomenon as well as bayesian model specification. Additionally, it provides a way to simulate data as a means to compare model results to the ground truth. Finally, it provides an overview of how to calculate ROAS, mROAS and optimize future media spend.

### Carryover Effect (Lagged Impact)

Caryover affect, often called lagged effect, is modeled through what is called an adstock function. 

* Adstock was coined by Simon Broadbent and attempts to measure how an advertiser's media spend accumulates and decays overtime. 

The paper models this two ways: 

**Geometric Function**

* This is a weighted average going back L days, where L can vary by media channel. 
* Here the effect has the largest impact on the day of spend/impression and decays after. 
* I imagine the more transactional a channel is the quicker the decay. Additionally, the lead time a consumer takes to make a purchase decision e.g. (buying a house takes months so the L is longer and decay is slower) impacts the functions parameters. 

**Delayed Adstock (radial kernel basis function)**  

* The effect of media spend spikes several days after media spend.
* I imagine this would be a better function to model channels that are upper funnel i.e. brining the need and/or product to the consumers mind. 

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
 
Functionl Forms

* Geometric
    * alpha: decay parameter
![geo](https://i.imgur.com/msLAT36.png)

* Delayed
    * alpha: decay parameter
    * theta: delayed effect parameter
![delayed](https://i.imgur.com/DydmaeB.png)

### Transformed Timeseries with Geometric and Delayed Adstock functions 
 
 ![geometric](https://i.imgur.com/IHiELKx.png)
 ![delayed](https://i.imgur.com/SckKLoj.png)
 
Above, we can see the actual and transformed media spend value for the geometric and delayed adstock functions. As expected, each looks like a moving average with the delayed adstock function show peaks a few days after large media spend spikes. 
 
### Shape Effect / Diminishing Returns

Next, the paper moves onto modeling the phenomenon of diminishing returns, referred to as the shape effect in the paper. The Hill Function is used to model this, however other functions are possible and the paper cites the following: 

**Other Functional Forms** 

* Sigmoid function (also referred to as the logistic function),
* or the integral of other probability distributions such as the normal distribution.
* Another alternative is monotonic regression splines.

Interestingly, the [Hill function](https://en.wikipedia.org/wiki/Hill_equation_(biochemistry)) has it's roots in biochemistry. 

Parameters: bHill

![hill](https://i.imgur.com/00CijBt.png)

* S = Slope ; greater than 0
* K = Half Saturation Point ; greater than 0
* beta = Coefficient for channel ; greater than 0
* x = media spend 

```python
def beta_hill(x, S, K, beta):
    return beta - (K**S*beta)/(x**S+K**S)
```

The equation above is a modified version of the original. In the original Hill function as X approaches infinity the function approaches 1, therefore we multiple times a beta coefficient to account for various strengths of marketing channels. 

The paper does point out that the function has poor identifiability. For example, when K (the half saturation) point is outside of the observed media spend. We can see this in the image below. If we examine the blue line, we see that we don't observe the half-satuation point i.e. the blue line hasn't started to flatten due to diminishing returns, therefore it is impossible for our model to pick this up. 

![hill transformations](https://i.imgur.com/v30CyLy.png)

To account for poor identifiability and therefore difficulty estimating the paper referenced a similar function descibed by Jin, Shobowale, Koehler and Case (2012), shown below. 

![grp](https://i.imgur.com/pp8h2qY.png)

* R = Reach
* G = Gross Rating Points / Impressions 
* a = beta (channel strength)
* b = K * beta


### Combining: Carryover and Shape / Lag and Diminishing Returns

There are two possible approaches to combining the shape and carryover affects. 

1. Apply adstock first and then shape.
2. Apply shape first and then adstock. 

The paper recommends route 1 if there is small spend for any given time period. This makes sense as the shape affect is likely not to be activated by small spend. Alternatively, for large sustained spend it makes sense to first apply the shape (diminishing return) affect and then apply the adstock function. 

### Model

Final, we can specify the function form of our (the papers) MMM model: 

![form](https://i.imgur.com/o0v8rxM.png)

where Xt has been transformed through the Adstock function and Z represents the control variables. 

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

alpha_media_1 = .6
theta_media_1 = 5
k_media_1     = .2
s_media_1     = 1
beta_media_1  = .8

alpha_media_2 = .8
theta_media_2 = 3
k_media_2     = .2
s_media_2     = 2
beta_media_2  = .6
 
alpha_media_3 = .8
theta_media_3 = 4
k_media_3     = .2
s_media_3     = 2
beta_media_3  = .3

L=13
ru=4
lamb = -.5
ep = .05**2

m1 = [beta_hill(x, s_media_1, k_media_1, beta_media_1) for x in carryover(media_1, alpha_media_1, L, theta = theta_media_1, func='delayed')]
m2 = [beta_hill(x, s_media_2, k_media_2, beta_media_2) for x in carryover(media_2, alpha_media_2, L, theta = theta_media_2, func='delayed')]
m3 = [beta_hill(x, s_media_3, k_media_3, beta_media_3) for x in carryover(media_3, alpha_media_3, L, theta = theta_media_3, func='delayed')]
 
y = np.repeat(ru, N) + m1 + m2 + m3 + (lamb*price_variable) + np.random.normal(0, ep, N)
```

![media](https://i.imgur.com/pd0BU7M.png)

![price](https://i.imgur.com/8eGRAzj.png)

![sales](https://i.imgur.com/eL3sVhU.png)


### Fitting the Model.

Now that the dataset has been simulated it is time to fit the model. The paper uses STAN, however I still with Python and use PyMC3.

#### Results

![In-Sample Prediction](https://i.imgur.com/IzkCkEY.png)

* MAPE: 0.12
* MAE:  0.026 

I expect this the in-sample fit to be pretty good. 1) becuase it's in-sample and 2) because we generated the data with the same functional form we are modeling. So, it boils down to how well the MCMC approimation works. 

### True Parameters versus Approximated Parameters. 

Below, we can see the posterior distribution of model parameters versus the true model parameter (blue line). 

![k](https://i.imgur.com/i02JOzb.png)
![beta](https://i.imgur.com/p2vRz8t.png)
![slope](https://i.imgur.com/Oq4P7o4.png
![alpha](https://i.imgur.com/5cEp0nO.png)
![theta](https://i.imgur.com/vWP1K8B.png)
![intercept](https://i.imgur.com/UjSvcYt.png)

### ROAS / mROAS Calculation

Next, we can calculate ROAS and mROAS with the following equations.

![ROAS](https://i.imgur.com/ZGnBCPn.png)
![mROAS](https://i.imgur.com/fKlOCAb.png)

Basically what this boils down to is a predicted sales with all media channels minus predicted sales with all but the Mth media channel dived by spend. The only catch is that we have to factor in the post-period effect. So, instead of just calculating for the change period we have to include the post period as well. 

![p6](https://i.imgur.com/xs3gH3W.png)

```
# simulate ROAS 

media_1_roas = []
media_2_roas = []
media_3_roas = []


burnin=500
for i in range(1000):
    burnin=5
    s = np.random.randint(1,1000-burnin)
    intercept = t['intercept'][s]
    
    s_sample1, s_sample2, s_sample3 = t['s'][:,0][burnin:][s],     t['s'][:,1][burnin:][s],  t['s'][:,2][burnin:][s]
    k_sample1, k_sample2, k_sample3 = t['k'][:,0][burnin:][s],     t['k'][:,1][burnin:][s],  t['k'][:,2][burnin:][s]
    b_sample1, b_sample2, b_sample3 = t['beta'][:,0][burnin:][s],  t['beta'][:,1][burnin:][s],  t['beta'][:,2][burnin:][s]
   
    a_sample1, a_sample2, a_sample3 = t['alpha'][:,0][burnin:][s], t['alpha'][:,1][burnin:][s],  t['alpha'][:,2][burnin:][s]
    t_sample1, t_sample2, t_sample3 = t['theta'][:,0][burnin:][s], t['theta'][:,1][burnin:][s],  t['theta'][:,2][burnin:][s]
   
    fitted_m1 = [beta_hill(x, s_sample1, k_sample1, b_sample1) for x in carryover(media_1, a_sample1, L, theta = t_sample1, func='delayed')]
    fitted_m2 = [beta_hill(x, s_sample2, k_sample2, b_sample2) for x in carryover(media_2, a_sample2, L, theta = t_sample2, func='delayed')]
    fitted_m3 = [beta_hill(x, s_sample3, k_sample3, b_sample3) for x in carryover(media_3, a_sample3, L, theta = t_sample3, func='delayed')]
 
    y_hat    = intercept + fitted_m1 + fitted_m2 + fitted_m3 + t['lamb'][burnin:][s] * price_variable
     
    y_hat_m1 = intercept + fitted_m2 + fitted_m3 + t['lamb'][burnin:][s] * price_variable
    y_hat_m2 = intercept + fitted_m1 + fitted_m3 + t['lamb'][burnin:][s] * price_variable
    y_hat_m3 = intercept + fitted_m1 + fitted_m2 + t['lamb'][burnin:][s] * price_variable

    media_1_roas.append(sum(y_hat[L:]-y_hat_m1[L:]) / media_1[L:len(media_1)-L].sum())
    media_2_roas.append(sum(y_hat[L:]-y_hat_m2[L:]) / media_2[L:len(media_1)-L].sum())
    media_3_roas.append(sum(y_hat[L:]-y_hat_m3[L:]) / media_3[L:len(media_1)-L].sum())
```

### Optimizing the Marketing Budget

Finally, we move onto optimizing a budget. To do this we take the modeled ROAS numbers and translate this into a contrained optimization problem. 

![optimal_mix](https://i.imgur.com/ol4BiD1.png)

CODE 

Notice how in the code above I used the median as a numerical summary for the posterior. If we wanted to be a bit more bayesian, we could maximize the average return across the entire poterior distribution.

### Online Optimization

(coming soon)
Finally, and outside of the paper, I want to explore what an online optimization looks like. In this scneario we 1) generate two years of historical data 2) generate the price variable for the next year and break into quarters 3) fit the model with the two years of historical data 4) set media spend allocations each quarter and then re-run the model. 

Questions that will arise from this study:

1) How much regret sum(true potential - realized potential) we have.
2) Do we improve each quarter? 
3) Can / how likely are we to get stuck in a local optimum. 

### Summary

In this review, we looked at how the paper models:

1) Shape.
2) Carryover.
3) Combining Shape/Carryover.
4) Full Form of MMM Model.
5) Simulated data and comparing to ground truth. 
6) Calculating ROAS / mROAS.
7) Optimizaing marketing budget. 
8) Extra: Online Optimization

The paper goes on to discuss a few more interesting points 8) effect of priors 9) effect of sample size 10) application to real dataset. 


