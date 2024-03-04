---
title: "Bayesian Inference"
weight: 2
bookFlatSection: true
---

# **Notes on Bayesian Inference**

{{< hint tip >}}
**git clone -> cd**
**pip install -r requirements.txt** (scipy, jax.numpy)
{{< /hint >}}

### **Understanding Uncertainty via Probabilities**

- sum rule: P(X) = P(X,Y) + P(X, {{< katex >}}\neg{{< /katex >}}Y)
- product rule: P(X,Y) = P(X)  {{< katex >}}\cdot{{< /katex >}} P(Y | X)
- bayes theorem on data D:

{{< katex display >}}
\underbrace{P(X | D)}_{\text{posterior of X given D}} \hspace{.1cm} = \frac{\overbrace{P(D | X)}^{\text{likelihood of X under D}} \hspace{.1cm} \cdot \hspace{.1cm} \overbrace{P(X)}^{\text{prior of X}}}{\underbrace{P(D)}_{\text{marginalization or evidence of the model}}}
{{< /katex >}}

- *"discrete domain is just a subset of the continuous domain"*
- this extends deductive reasoning (statistics) to plausible reasoning (probabilities)


### **Exponential Families** 

- random variable X taking x values {{< katex >}}\subset \R^n{{< /katex >}}

- probability distribution for X with pdf of the following form:

{{< katex display >}}
p_{w}(x) = \underbrace{h(x)}_{\text{base measure}} \hspace{.05cm} \text{exp} \hspace{.05cm} \left() \overbrace{\phi(x)}^{\text{sufficient statistics}}^T \hspace{.05cm} \underbrace{w}_{\text{natural parameters}} - \hspace{.05cm} \text{log} \hspace{.05cm} \overbrace{Z(w)}^{\text{partition function}} \right) = \frac{h(x)}{Z(w)} e^{\phi(x)^T w} \hspace{.05cm} = \hspace{.05cm} p(x | w)
{{< /katex >}}

- for notational convenience, reparametrize natural parameters w := {{< katex >}}\eta(\theta){{< /katex >}} in terms of canonical parameters {{< katex >}}\theta{{< /katex >}}

- exponential families  {{< katex >}}(h(x), \phi(x)){{< /katex >}} as the model for some data **x** guarantee automatic existence of conjugate priors, although not always tractable

- conjugate priors allow analytic Bayesian inference of probabilistic models, if we can compute the partition function Z(w) of the likelihood and the one for the conjugate prior F({{< katex >}}\alpha, \nu{{< /katex >}})

- biggest challenge is finding the normalization constant

- reduce Bayesian inference to:
    - modelling: computing sufficient statistics {{< katex >}}\phi(x){{< /katex >}} and partition function Z(w)
    - evaluating posterior: assessing log partition function F of the conjugate prior

- if F is not tractable {{< katex >}}\Longrightarrow{{< /katex >}} use Laplace approximations:
    - find the mode {{< katex >}}ŵ{{< /katex >}} of the posterior, by solving root-finding problems

     {{< katex display >}}
     \nabla_{w} \hspace{.05cm} \text{log} \hspace{.05cm} p (w | x) = \frac{\alpha + \sum_{i=1}^n \phi(x_{i})}{\nu + n}
     {{< /katex >}}

    - evaluate the Hessian {{< katex >}}\Psi = \nabla_w \nabla_w^T \hspace{.05cm} \text{log} \hspace{.05cm} p(w|x){{< /katex >}} at the mode ŵ

    - approximate posterior as {{< katex >}}\mathcal{N}(w;ŵ, -\Psi^{-1}) {{< /katex >}} and the conjugate log partition function as:

      {{< katex display >}}
     F(\alpha', \nu') \approx \sqrt{(2\pi)^d \hspace{.05cm} \text{det}(-\Psi^{-1})} \cdot \text{exp}[ ŵ^T \cdot \alpha' - \hspace{.01cm} \text{log} \hspace{.01cm} Z(ŵ)^T \hspace{.01cm} \nu' ]
     {{< /katex >}}


{{< hint important >}}
Laplace approximations reveal that Bayesian inference prioritizes capturing the geometry of the likelihood function around its peak (mode), rather than solely focusing on the prior distribution. In this context, uncertainty is better understood as encompassing the multitude of potential solutions simultaneously, rather than fixating on a single point estimate. It's about monitoring the breadth of plausible solutions. This means it is about observing the volume of possibilities rather than pinpointing individual points
{{< /hint >}}