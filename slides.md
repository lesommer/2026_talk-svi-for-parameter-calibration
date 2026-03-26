---
theme : academic
#theme : apple-basic
title: From optimal point estimates to posterior sampling with differentiable models
layout : cover
coverAuthor: Julien Le Sommer
coverAuthorUrl: https://github.com/lesommer
author : J. Le Sommer
#transition: fade-out
transition : slide-left | slide-right
date: 25/03/2026
---

# Bayesian estimation with differentiable solvers 

## From optimal point estimates to full posterior sampling with DP



---
layout: center
class: "text-center"
---

# **1. Calibrating (differentiable) physics based solvers**


---

#  Calibrating physics based solvers : setting the problem   


We are interested in the dynamics of a **state vector** $X_t \in \mathbb{R}^m$.

We assume that we have a physics-based **numerical model** $\mathcal M_{\theta}$ that describes the dynamics of $X_t$ according to


$$dt\, X_t = \mathcal M_{\theta} (X_t,F(t))$$ 

where 
  - $F$ refers to the external forcing, 
  - $\theta \in \mathbb{R}^p$ is a **set of scalar parameters** controlling the behavior of a numerical model $\mathcal M_{\theta}$. 



<div v-click> 

 *Examples* : single-column ocean model describing ocean surface boundary layer dynamics, ice-sheet model with parameterized based friction law, streamflow models...

</div>

<div v-click> 

 We assume that $\mathcal M_{\theta}$ is a **differentiable program** so that $X_t$ can readily be differentiated with respect to $\theta$.  

</div>


---

#  Calibrating physics based solvers : metrics

 We would like to know how to optimally **choose a set of parameters $\theta$** based on some knownledge of $X_t$ (observations, hi-fidelity simulations...).

 We assume that we know how to measure how good is a choice of parameters $\theta$, with an **evaluation fonction $J(\theta)$**. 

<div v-click> 

  - $J$ may be designed in order to compare simulated trajectories with some **target reference** data that we would like the model $\mathcal M_{\theta}$ to match (observations, hi-fidelity simulations...). 
</div>
<div v-click> 

  - A simple choice for $J(\theta)$ can be the **mean squared error (MSE)** between predicted and target data, 
$$J(\theta)= \sum\limits_{k \text{ in } {obs}} \frac{1}{\sigma_k}||X_k^{\theta}-X_k^{obs}||$$
 
$$X^{\theta}_k  = \Phi_{\mathcal M} (\theta, F, X^{t=0}_{obs})= X^{t=0}_{obs} + \int_{t=0}^{t = t_k} \mathcal M_{\theta} (X_t(t'),F(t'))\, dt'$$

 - or more advances metrics based on higher level summary statistics of model simulations. 
</div>


---
layout: image-right

# la source de l'image
image: ./figures/631731_P7z2BKhd0R-9uyn9ThDasA.png
backgroundSize: 30em 50%
# un nom de classe customisé du contenu
class: my-cool-content-on-the-left
---

# A first approach 

<br></br>

 Seeking the **optimal point estimate $\widehat{\theta}$** :  

$$\widehat{\theta} = \arg\min_{\theta} J(\theta) $$


with gradient based optimization by formulating a series $(\theta_{k})_k$ such that 

$$\theta_{k+1} = \mathcal F(\theta_{k}, \nabla_\theta \,J(\theta_{k}) )$$

where $\mathcal F$ is an appropriately chosen **minimizer** adapted to the topology and regularity of $J(\theta)$.

<!--  adapted to the topology and regularity of $J(\theta)$.  -->



---
layout: image-right
image: ./figures/image-44-1024x867.png
backgroundSize:  80%
---

# In practice, gradient based optimization fails...

 - Naive gradient based optimization is prone to overfitting and local minima.

 - This approach can fail when the optimization landscape is too complex or highly non-convex. 
 
 - In such case, the limit of the series $(\theta_{k})_k$ can depend on the choice of initial condition $\theta_{0}$. 
 
 <div v-click> 

 - Another drawback of this approach is that is does not provide uncertainty estimates on the parameters $\widehat{\theta}$.

 </div>

---

# Model calibration with bayesian inference (1/2)

<br></br>
### **Bayesian inference** : 
a general framework for estimating the **distribution of plausible model parameters** $\theta$ given our target reference data,  explicitely taking into account **pre-existing knowledge** about the parameters.   


<div v-click> 

<br></br>

 - Let's call $x_0\in \mathbb{R}^n$ our target reference and $x\in\mathbb{R}^n$ the predicted values computed from our numerical model $\mathcal M_{\theta}$. 

<!--  In practice $x$ can be computed from an ensemble of model trajectories $\{X^j_t\}_j$ obtained with the same set of parameters $\theta$.  -->

 - Our aim is to be estimate the **probability for a given  $\theta$ to be an optimal set of parameters** given our knowledge of the target reference $x_0$. 
 
 - We are therefore interested in estimating the conditional probability $p(\theta | x_0)$. 

<!--  This problem can be tackled with *Bayesian inference*, a general class of methods that updates the probability of hypotheses (parameters) as evidence (data) is observed, using Bayes' theorem to combine prior beliefs with new information. … -->

</div>


---

# Model calibration with bayesian inference (2/2)


 Our entry point, **Bayes theorem** : 

<div v-click> 


$$p(\theta|x) = \frac{p(x|\theta)\,p(\theta)}{p(x)}$$
</div>

<div v-click> 

where, by convention, 
  - $p(\theta|x)$ is refered to as the **posterior** &rarr; *This is the quantity we are trying to estimate*.
  - $p(x|\theta)$ is refered to as the **likelyhood** &rarr; *This will be given by the physics-based model*.
  - $p(x)$ is refered to as the **evidence** &rarr; *This is where our reference target comes into play*. 
  - $p(\theta)$ is refered to as the **prior** &rarr; *This holds all our preexisting knowledge about $\theta$*.

</div>

<div v-click> 

**Bayesian inference** = estimating $p(\theta | x_0)$.

A large number of methods are available for performing **Bayesian inference** :  Approximate Bayesian computing (**ABC**), Markov Chain Monte Carlo (**MCMC**),  Bayesian Optimization (**BO**),  Simulation Based Inference methods (**SBI**). 

</div>


---
layout: center
class: "text-center"
---

# **2. From point estimates to posterior sampling**


---

# Bayesian interpretation of gradient-based optimization

In the Bayesian formalism, the naive gradient-based optimization  relates to the so-called **Maximum Likelihood Estimation (MLE)**. 

MLE aims at finding the parameters that make the reference target data $x_0$ as probable as possible under the model $\mathcal M_{\theta}$, namely finding the parameters $\theta_{MLE}$ that maximize the log-likelihood of the observed data $x_0$: 

$$\theta_{MLE}= \arg\max_\theta \, \log p(x_0 | \theta)$$

For gaussian likelyhoods, it can easily be shown that maximizing the log-likelihood is equivalent to minimizing the Mean Square Error (MSE) of model prediction vs the reference target, assuming all the observation in the target reference are independent. 

Interestingly, as MLE does not incorporate any prior knowledge about $\theta$ it can lead to physically unrealistic estimates, and it is also prone to overfitting.

---

# Bayesian inference with differentiable solvers



---

# Stochastic Variational inference (SVI)


---

# Evidence Lower Bound (ELBO)


---
layout: center
class: "text-center"
---

# **3. Practical steps for calibrating parameters with SVI**



---

# Approximating the expected log-likelyhood term $T_{d}$


---

# Proposal distribution and the renormalization trick

---

# KL divergence term of the ELBO loss $T_{r} \,(\phi)$


---

# In practice 

pseudo-code

---
layout: center
class: "text-center"
---

# **4. Wrap-up and references**



---

# Wrappping-up

- ...



---

# List of references



 - A good **introduction to SVI** and its implementation **in Jax** : https://juanitorduz.github.io/html/intro_svi.html
 
 - **NumPyro** provides an implementation of SVI and HMC compatible with Jax gradients : https://num.pyro.ai/ https://github.com/pyro-ppl/numpyro
 
 - **GPJax** provides high level **API for variational families** and variational inference in Jax : https://docs.jaxgaussianprocesses.com
 
 - **JaxSCMC** also provides an implementation of HMC in Jax: https://arxiv.org/abs/2505.11190v1#S2
 
 - The specific flavor of SVI proposed above (with SGD/mini-bacthes and the reparametrization trick) is also refered to as the **Stochastic Gradient Variational Bayes (SGVB)** estimator. It has been used recently for calibrating optical models for marine applications, see  https://doi.org/10.5194/gmd-18-7575-2025


