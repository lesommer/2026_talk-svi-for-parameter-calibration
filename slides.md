---
theme : academic
title: From optimal point estimates to posterior sampling with differentiable models
layout : cover
coverAuthor: Julien Le Sommer
coverAuthorUrl: https://github.com/lesommer
author : J. Le Sommer
#transition: fade-out
---

# Bayesian estimation with differentiable solvers

## From optimal point estimates to posterior sampling 


---
layout: center
class: "text-center"
---

# Calibrating physics based solvers


---

#  Problem setting  


We are interested in choosing optimally a set of scalar parameters $\theta \in \mathbb{R}^p$ controlling the behavior of a numerical model $\mathcal M_{\theta}$. The numerical model $\mathcal M_{\theta}$ is supposed to describe the dynamics of a state vector $X_t \in \mathbb{R}^m$, according to 

$$dt\, X_t = \mathcal M_{\theta} (X_t,F(t))$$ 

where $F$ refers to the external forcing. In what follows we assume that $\mathcal M_{\theta}$ is a differentiable program so that $X_t$ can readily be differentiated with respect to $\theta$.  

---

#  Evaluation function 

We further assume that we know how to measure how good is a choice of parameters $\theta$, through an evaluation fonction $J(\theta)$. In practice, $J$ may be designed in order to compare simulated trajectories of the state vector $X_t$ with some target reference data that we would like the model $\mathcal M_{\theta}$ to match (observations, hi-fidelity simulations...). 

A simple choice for $J(\theta)$ can be the mean squared error (MSE) between predicted and target data, or more advances metrics based on higher level summary statistics of model simulations. 

---

# Optimal point estimate with gradient-based optimization

This approach seeks to find an optimal point estimate for the parameters $\theta$ by minimizing $J(\theta)$. The optimal point estimate $\widehat{\theta}$ is here defined as 
$$\widehat{\theta} = \arg\min_{\theta} J(\theta) $$

Since $\mathcal M_{\theta}$ is differentiable, the gradient $\nabla_\theta J(\theta)$ is computationally tractable, so that the optimal point estimate  $\widehat{\theta}$ can be sought with gradient-based optimization, by formulating a series $(\theta_{k})_k$ such that 

$$\theta_{k+1} = \mathcal F(\theta_{k}, \nabla_\theta \,J(\theta_{k}) )$$

where $\mathcal F$ is an appropriately chosen *minimizer*, adapted to the topology and regularity of $J(\theta)$. 


---

# Why gradient based optimization can fail


- This approach is simple but can fail when the optimization landscape is too complex or highly non-convex. 
- In such case, the limit of the series $(\theta_{k})_k$ can depend on the choice of initial condition $\theta_{0}$. 
 - Another drawback of this approach is that is does not provide uncertainty estimates on the parameters $\widehat{\theta}$.


---

# Calibrating parameters with bayesian inference

Bayesian inference provides a general framework for estimating the distribution of plausible model parameters $\theta$ given our target reference data. This framework allows to take explicitely into account pre-existing knowledge about the parameters.   

Let's call $x_0\in \mathbb{R}^n$ our target reference and $x\in\mathbb{R}^n$ the predicted values computed from our numerical model $\mathcal M_{\theta}$. In practice $x$ can be computed from an ensemble of model trajectories $\{X^j_t\}_j$ obtained with the same set of parameters $\theta$. 

Our aim is to be estimate the probability for a given  $\theta$ to be an optimal set of parameters given our knowledge of the target reference $x_0$. We are therefore after the conditional probability $p(\theta | x_0)$. 

This problem can be tackled with *Bayesian inference*, a general class of methods that updates the probability of hypotheses (parameters) as evidence (data) is observed, using Bayes' theorem to combine prior beliefs with new information. 

---

# Leveraging bayes theorem for parameter calibration

The entry point to these methods for estimating $p(\theta | x_0)$ is the Bayes theorem which writes 
$$p(\theta|x) = \frac{p(x|\theta)\,p(\theta)}{p(x)}$$

where, by convention, 
  - $p(\theta|x)$ is refered to as the *posterior*. This is the quantity we are trying to estimate.
  - $p(x|\theta)$ is refered to as the *likelyhood*. This will be given by the physics-based model.
  - $p(x)$ is refered to as the *evidence*. This is where our reference target comes into play. 
  - $p(\theta)$ is refered to as the *prior*. This holds all our preexisting knowledge about $\theta$.

A large number of methods are available for performing *Bayesian inference* : 
  - Approximate Bayesian computing (ABC), 
  - Markov Chain Monte Carlo (MCMC), 
  - Bayesian Optimization (BO)  
  - Simulation Based Inference methods (SBI). 
