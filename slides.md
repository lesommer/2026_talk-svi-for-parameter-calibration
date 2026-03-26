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

<div v-click> 

with gradient based optimization by formulating a series $(\theta_{k})_k$ such that 

$$\theta_{k+1} = \mathcal F(\theta_{k}, \nabla_\theta \,J(\theta_{k}) )$$

</div>
<div v-click> 

where $\mathcal F$ is an appropriately chosen **minimizer** adapted to the topology and regularity of $J(\theta)$.

</div>

<!--  adapted to the topology and regularity of $J(\theta)$.  -->



---
layout: image-right
image: ./figures/image-44-1024x867.png
backgroundSize:  80%
---

# But, in practice, 
**Gradient based optimization often fails...**

 - Naive gradient based optimization is prone to **overfitting and local minima**.

 - This approach can fail when the optimization **landscape is too complex** or highly non-convex. 
 
 - In such case, the limit of the series $(\theta_{k})_k$ can **depend on the choice of initial condition $\theta_{0}$**. 
 
 <div v-click> 

 - Another drawback of this approach is that is does not provide **uncertainty estimates** on the parameters $\widehat{\theta}$.

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

# Bayesian interpretation of gradient-based optimization : MLE 

In the Bayesian formalism, the naive gradient-based optimization relates to the so-called **Maximum Likelihood Estimation (MLE)**. 


<div v-click> 

MLE aims at finding the parameters that make the reference target data $x_0$ as probable as possible under the model $\mathcal M_{\theta}$, namely finding the parameters $\theta_{MLE}$ that **maximize the log-likelihood of the observed data $x_0$**: 

$$\theta_{MLE}= \arg\max_\theta \, \log p(x_0 | \theta)$$

</div>

<div v-click> 


- For gaussian likelyhoods,  **maximizing the log-likelihood is equivalent to minimizing the Mean Square Error (MSE)** of model prediction vs the reference target, assuming all the observation in the target reference are independent. 

</div>

<div v-click> 

- As MLE does not incorporate any prior knowledge about $\theta$ it can lead to physically unrealistic estimates, and it is also prone to overfitting.

</div>


---

# Bayesian interpretation of gradient-based optimization : MAP

Naive gradient-based optimization also relates to **Maximum A Posteriori (MAP)** estimation, which aims at providing a point estimate of the parameters $\theta_{\text{MAP}}$ which maximize the log-posterior

$$\theta_{\text{MAP}} = \arg\max_\theta \, \log p(\theta | x_0)$$ 

<div v-click> 

Using Bayes' theorem, one can show that MAP is essentially **a regularization of MLE**
$$\theta_{\text{MAP}} = \arg\max_\theta \, \log p(\theta | x_0) = \arg\max_\theta \, \log p(x_0 | \theta) + \log p(\theta)$$

<!--   Note that MAP reduces to MLE if the prior $p(\theta)$ is uniform (i.e., $\log p(\theta)$ is constant and can be ignored).  -->

</div>

<div v-click> 

MAP can be readily implemented from a pre-existing gradient-based optimization pipeline, 

</div>

<div v-click> 
 
- **assuming a uniform prior** (but bounded between max and min values) : MAP is equivalent to imposing a clipping on parameters $\theta_k$ after each iteration of the minimizer.  
- **assuming a gaussian prior** : MAP is equivalent to imposing the so-called *background* term used in variational data assimilation $\sum\limits_{j}^{p}\frac{1}{\sigma_j}|\theta^j-\theta_{0}^j|^2$

</div>

---

# Bayesian inference with differentiable solvers

**Bayesian inference** = estimating the full posterior distribution $p(\theta | x_0)$.

<div v-click>

Can we use the solver **differentiability**  to explore the parameter space and estimate $p(\theta | x_0)$ ? 

</div>

<!--  We would like to use the fact that  $\mathcal M_{\theta}$ is differentiable and leverage gradient-based methods to  **estimate the full posterior distribution $p(\theta | x_0)$**. -->

<div v-click>

Existing methods for Bayesian inference with differentiable solvers : 

- **Stochastic Variational Inference (SVI)**: uses gradient descent to approximate the posterior by optimizing a variational distribution $q(\theta)$ to minimize the KL divergence from the true posterior.
- **Hamiltonian Monte Carlo (HMC):** uses gradients to propose samples in a way that explores the parameter space more efficiently than traditional MCMC methods.

</div>

<div v-click>

Here, we will adopt SVI as its practical implementation can be seen as **an extension of gradient-based point estimation pipelines**. 

</div>

<div v-click> 

SVI is also known to **scale efficiently to large problems** (high dimensionallity $\theta$), at the cost of only providing an approximate estimation of uncertainty.   

</div>


---

# Stochastic Variational inference (SVI)

<br></br>

<div v-click>

-  **SVI** is a scalable approximate Bayesian inference method that transforms the problem of posterior inference into an **optimization problem**. 

</div>

<div v-click>

- Instead of sampling from the posterior distribution (like MCMC), SVI aims at **approximating the true posterior distribution** $p(\theta | x_0)$ 
- with a **tractable distribution $q_{\phi}(\theta)$**, typically from a family like Gaussian distributions. 

</div>

<div v-click>

In what follows, we will start by defining $q_{\phi}(\theta)$, a variational **approximation of the posterior**, where $\phi$ is the set of variational parameters to be optimized. 

</div>

<div v-click> 

The optimization procedure will then aim at **minimizing the Kullback-Leibler (KL)** divergence between $q_{\phi}(\theta)$ and $p(\theta | x_0)$, 

</div>

<div v-click> 

...which is equivalent to maximizing the **Evidence Lower Bound (ELBO)**.

</div>


---

# Evidence Lower Bound (ELBO)

The objective of SVI is to maximize the **Evidence Lower Bound (ELBO)**    

$$ELBO(\phi) = \mathbb{E}_{q_{\phi}(\theta)}[\,\log \,p(x_0|\theta)\,] - KL\,(\,q_{\phi}(\theta)\,||\,p(\theta)\,)$$

<div v-click>


Using Bayes' theorem, one can show that maximizing the ELBO with respect to $q_{\phi}(\theta)$ is **equivalent to minimizing the KL divergence** between $q_{\phi}(\theta)$ and the true posterior $p(\theta | x_0)$.

</div>

<div v-click>

The ELBO consists of two terms : 
 - the **expected log-Likelihood** :  encourage $q_{\phi}(\theta)$ to match the reference data $x_0$ 
 $$T_{d} \,(\phi)= \mathbb{E}_{q_{\phi}(\theta)}[\,\log \,p( x_0 | \theta )\,]$$
 - the **KL divergence** will encourage $q_{\phi}(\theta)$ to stay close to the prior $p(\theta)$ therefore acting as a regularizer.
$$T_{r} \,(\phi) =  KL\,(\,q_{\phi}(\theta)\,||\,p(\theta)\,)$$

</div>

<div v-click>

**SVI** = a probabilistic generalisation of MAP for a prescribed variational family $q_{\phi}(\theta)$

</div>



---
layout: center
class: "text-center"
---

# **3. Practical steps for calibrating parameters with SVI**


---

#  Steps for calibrating parameters with SVI

<div v-click>

**1. Choosing a family of variational distributions $q_{\phi}(\theta)$**

 - We will start by defining $q_{\phi}(\theta)$, a variational **approximation of the posterior**, where $\phi$ is the set of variational parameters to be optimized.

 - In what follows, as a simple starting point, we will choose $q_{\phi}(\theta)$ to be a **multivariate Gaussian distribution**.

</div>

<div v-click>

**2. Minimizing the ELBO loss**

 - Because we target simulators  $\mathcal M_{\theta}$ that are relatively expensive to run, we will use **mini-batches and stochastic gradient descent** for the minimization. 

</div>

<div v-click>

**3. Extracting the posterior distribution of the parameters**

 - After optimization of the ELBO loss, $\phi$ will contain the **means $\mu_k$** and the log **standard deviation $\Sigma$** of our approximate **posterior distribution** of the parameters $q_{\phi}(\theta)$. 

</div>

---

# Approximating the expected log-likelyhood $T_{d}\,(\phi)$

The first term of the ELBO loss is the **expected log-likelihood**, namely

$$T_{d} \,(\phi)= \mathbb{E}_{q_{\phi}(\theta)}[\,\log \,p(x_0| \theta)\,]$$

<div v-click>

The notation $\mathbb{E}_{q_{\phi}(\theta)}[f(\theta)]$ refers to the **expectation with respect to the distribution $q_{\phi}(\theta)$**, it is simply an average weighted by the distribution $q_{\phi}(\theta)$

$$\mathbb{E}_{q_{\phi}(\theta)}[f(\theta)] = \int f(\theta) \, q_{\phi}(\theta) \;d\theta$$

</div>

<div v-click>
 
The expected log-likelihood can be approximated using **Monte Carlo sampling**:
$$\mathbb{E}_{q(\theta)} \left[ \log p(x_0 | \theta) \right] \approx \frac{1}{S} \sum_{s=1}^S \log p(x_0 | \theta_s), \quad \theta_s \sim q(\theta)$$

where $S$ is the **number of samples** drawn from $q_{\phi}(\theta)$.

</div>

---

# Proposal distribution and the renormalization trick


We parameterize our proposal posterior $q_{\phi}(\theta)$ as a **multivariate gaussian distribution** : 

$$q_{\phi}(\theta)\sim \mathcal N(\mu, \Sigma)$$

where $\mu \in \mathbb{R}^p$ is the mean vector and $\Sigma \in \mathbb{R}^{p\,\times\, p}$ is the covariance matrix of the distribution

<div v-click>

For sampling $\theta_s\sim q(\theta)$, we will use the so-called **renormalization trick**. This is a key ingredient of SVI which will ensure that our approximation of the log likelyhood will be differentiable with respect to $\mu$ and $\Sigma$.

</div>

<div v-click>
 
For given $\mu$ and $\Sigma$, $\theta_s$ will be drawn as  $\theta_s = \mu + L \,\epsilon$, where **$\epsilon$ is a random noise vector** from a standard normal distribution $\epsilon \sim \mathcal{N}(0, I)$, and $L$ is the lower triangular matrix such that $LL^T = \Sigma$, following the Cholesky decomposition of $\Sigma$. 

</div>

<div v-click>

For **diagonal covariance matrix $\Sigma=\sigma$**, the sampling will simply be obtained as $\theta_s = \mu + \sigma \odot \epsilon$, where $\odot$ refers to the element-wise (Hadamard) product. 

</div>

---

# KL divergence term of the ELBO loss $T_{r} \,(\phi)$

The second term of the ELBO loss is the **Kullback-Leibler divergence** between the proposal distribution $q_{\phi}(\theta)$ and the prior distribution $p(\theta)$ :
$$T_{r} \,(\phi) = KL\,(\,q_{\phi}(\theta)\,||\,p(\theta)\,)=\mathbb{E}[\,\log \,\frac{q_{\phi}(\theta)}{p(\theta)}\,]$$

<div v-click>


<!--   
The KL divergence term for a Gaussian variational distribution $q_{\phi}(\theta) = \mathcal{N}(\mu, \sigma^2)$ and a Gaussian prior $p(\theta) = \mathcal{N}(\mu_0, \sigma_0^2)$ is given by:


$$\text{KL}(q(\theta) \| p(\theta)) = \frac{1}{2} \left( \text{tr}(\Sigma_0^{-1} \Sigma) + (\mu - \mu_0)^T \Sigma_0^{-1} (\mu - \mu_0) - \log \det(\Sigma_0^{-1} \Sigma) - d \right)$$

where:

 - $\mu$ and $\Sigma = \text{diag}(\sigma^2)$ are the mean and covariance of $q_{\phi}(\theta)$.
 - $\mu_0$​ and $\Sigma_0 = \text{diag}(\sigma_0^2)$ are the mean and covariance of the prior $p(\theta)$.
 - $d$ is the dimensionality of $\theta$.

-->
</div>

<div v-click>

This term can be **computed analytically for Gaussian distributions** (and uniform priors).

</div>

<div v-click>

If $q_{\phi}(\theta)$ and $p(\theta)$ are both diagonal Gaussians (i.e., independent dimensions), this simplifies to:

$$\text{KL}(q(\theta) \| p(\theta)) = \sum_{i=1}^d \left( \log \frac{\sigma_0^{(i)}}{\sigma^{(i)}} + \frac{(\sigma^{(i)})^2 + (\mu^{(i)} - \mu_0^{(i)})^2}{2 (\sigma_0^{(i)})^2} - \frac{1}{2} \right)$$

where $\mu^{(i)}$ and $\sigma^{(i)}$ are the mean and standard deviation of the $i$-th dimension of $q_{\phi}(\theta)$, and $\mu_0^{(i)}$ and $\sigma_0^{(i)}$ are the mean and standard deviation of the $i$-th dimension of $p(\theta)$


</div>

---


```python
import optax

# Initialize variational parameters (mu, log_sigma)
d = len(x0)  # Dimensionality of theta
mu = jnp.zeros(d)
log_sigma = jnp.zeros(d)
params = (mu, log_sigma)

# Optimizer
optimizer = optax.adam(learning_rate=0.01)
opt_state = optimizer.init(params)

# Optimization loop
@jax.jit
def update(params, opt_state, x0):
    grads = jax.grad(elbo)(params, x0)
    updates, opt_state = optimizer.update(grads, opt_state)
    params = optax.apply_updates(params, updates)
    return params, opt_state

# Run SVI
for step in range(1000):
    params, opt_state = update(params, opt_state, x0)
    if step % 100 == 0:
        current_elbo = elbo(params, x0)
        print(f"Step {step}, ELBO: {current_elbo}")
```
---
layout: center
class: "text-center"
---

# **4. Wrap-up and references**



---

# Wrappping-up

**Goal**: Calibrate parameters $\theta$ of a differentiable physics-based solver $\mathcal{M}_\theta$​ to match reference data $x_0$​, combining **gradient-based optimization** and **Bayesian inference** to estimate the full posterior distribution of parameters.

<div v-click>

 - Gradient-based optimization (MLE/MAP) provides point estimates but **no uncertainty quantification** and can be **sensitive to initialization**.

 - Full Bayesian inference (e.g., MCMC) is **computationally expensive** for complex models.


</div>

<div v-click>


####  **Stochastic Variational Inference (SVI)**

 - Approximates the posterior $p(\theta|x_0)$ with a tractable distribution $q_\phi(\theta)$ (e.g., Gaussian).

 - Optimizes the Evidence Lower Bound (ELBO) using mini-batches and the reparameterization trick for differentiable computation.

 - Advantages: Scalability, easy integration into existing pipelines (JAX/Optax).


</div>



---

# List of references



 - A good **introduction to SVI** and its implementation **in Jax** : https://juanitorduz.github.io/html/intro_svi.html
 
 - **NumPyro** provides an implementation of SVI and HMC compatible with Jax gradients : https://num.pyro.ai/ https://github.com/pyro-ppl/numpyro
 
 - **GPJax** provides high level **API for variational families** and variational inference in Jax : https://docs.jaxgaussianprocesses.com
 
 - **JaxSCMC** also provides an implementation of HMC in Jax: https://arxiv.org/abs/2505.11190v1#S2
 
 - The specific flavor of SVI proposed above (with SGD/mini-bacthes and the reparametrization trick) is also refered to as the **Stochastic Gradient Variational Bayes (SGVB)** estimator. It has been used recently for calibrating optical models for marine applications, see  https://doi.org/10.5194/gmd-18-7575-2025


