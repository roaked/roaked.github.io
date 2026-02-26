---
title: "Bayesian Optimization"
weight: 3
bookFlatSection: true
---

# **Notes on Bayesian Optimization**

{{< hint tip >}}
- **git clone -> cd**
- **pip install -r requirements.txt** (jax.numpy, scipy, botorch, optuna)
{{< /hint >}}

### **Optimization Under Expensive Evaluations**

- Bayesian Optimization (BO) solves black-box optimization problems where each evaluation is expensive:

{{< katex display >}}
x^\star = \arg \max_{x \in \mathcal{X}} f(x)
{{< /katex >}}

- assumptions:
  - gradients are unavailable or too noisy
  - objective evaluations are costly (training loops, simulations, experiments)
  - query budget is limited, so every new point matters

- BO minimizes regret by combining:
  - a probabilistic surrogate of {{< katex >}}f(x){{< /katex >}}
  - an acquisition strategy that controls exploration vs exploitation

### **Surrogate Modelling with Gaussian Processes**

- BO commonly models the unknown function as:

{{< katex display >}}
f(x) \sim \mathcal{GP}(m(x), k(x, x'))
{{< /katex >}}

- after observing data {{< katex >}}\mathcal{D}_t = \{(x_i, y_i)\}_{i=1}^{t}{{< /katex >}}, prediction at {{< katex >}}x{{< /katex >}} is:

{{< katex display >}}
f(x) \mid \mathcal{D}_t \sim \mathcal{N}(\mu_t(x), \sigma_t^2(x))
{{< /katex >}}

- interpretation:
  - {{< katex >}}\mu_t(x){{< /katex >}} is the current estimate of performance
  - {{< katex >}}\sigma_t(x){{< /katex >}} captures epistemic uncertainty

- this posterior is the foundation for deciding where to sample next

### **Acquisition Functions**

- acquisition functions convert posterior mean/uncertainty into an optimization signal:

{{< katex display >}}
x_{t+1} = \arg \max_{x \in \mathcal{X}} \alpha_t(x)
{{< /katex >}}

- common choices:
  - **Upper Confidence Bound (UCB)**
{{< katex display >}}
\alpha_{\text{UCB}}(x) = \mu_t(x) + \kappa \sigma_t(x)
{{< /katex >}}
  - **Probability of Improvement (PI)**
{{< katex display >}}
\alpha_{\text{PI}}(x) = \Phi\left(\frac{\mu_t(x)-f(x^+)-\xi}{\sigma_t(x)}\right)
{{< /katex >}}
  - **Expected Improvement (EI)**
{{< katex display >}}
\alpha_{\text{EI}}(x)= (\mu_t(x)-f(x^+)-\xi)\Phi(z)+\sigma_t(x)\phi(z), \quad z=\frac{\mu_t(x)-f(x^+)-\xi}{\sigma_t(x)}
{{< /katex >}}

{{< hint important >}}
- BO performance depends heavily on sensible kernel choices, robust hyperparameter fitting, and stable optimization of the acquisition objective.
{{< /hint >}}

### **Canonical BO Algorithm**

1. define bounded search space {{< katex >}}\mathcal{X}{{< /katex >}}
2. collect initial points (random or Latin hypercube)
3. fit/update the GP surrogate with current observations
4. maximize acquisition function to propose {{< katex >}}x_{t+1}{{< /katex >}}
5. evaluate expensive objective at {{< katex >}}x_{t+1}{{< /katex >}}
6. append {{< katex >}}(x_{t+1}, y_{t+1}){{< /katex >}} and repeat until budget is exhausted

- practical stop rules:
  - fixed evaluation budget
  - small improvement over last N steps
  - hitting a target metric

### **Practical Engineering Notes**

- normalize inputs to similar scales before fitting the surrogate
- standardize outputs to improve numerical conditioning
- represent known observation noise in the likelihood
- use log-scaled domains for parameters spanning multiple orders of magnitude
- for higher dimensions, combine BO with trust-region methods or structured priors

### **How Optuna Framework Works**

- Optuna provides a flexible orchestration layer for hyperparameter optimization, including Bayesian-style samplers and early stopping

- building blocks:
  - **Study**: full optimization process and trial history
  - **Trial**: one objective evaluation with suggested parameters
  - **Sampler**: chooses parameters (default: TPE; alternatives: random, CMA-ES, NSGA-II)
  - **Pruner**: stops weak trials early based on intermediate reports

- internal loop:
  1. user defines an objective function
  2. `study.optimize` starts repeated trials
  3. sampler proposes parameter values (`trial.suggest_*`)
  4. objective returns metric (or reports intermediate steps)
  5. pruner optionally interrupts underperforming trials
  6. study updates best params and complete trial database

```python
import optuna

def objective(trial):
    learning_rate = trial.suggest_float("learning_rate", 1e-5, 1e-1, log=True)
    depth = trial.suggest_int("depth", 2, 12)
    dropout = trial.suggest_float("dropout", 0.0, 0.5)

    score = train_and_validate_model(
        learning_rate=learning_rate, depth=depth, dropout=dropout
    )
    return score  # maximize here

study = optuna.create_study(direction="maximize")
study.optimize(objective, n_trials=100)

print(study.best_trial.number)
print(study.best_params)
print(study.best_value)
```

{{< hint note >}}
- Optuna can persist studies in SQLite/PostgreSQL and run distributed workers, making it practical for real training pipelines.
{{< /hint >}}
