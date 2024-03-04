---
title: "Bayesian Inference"
weight: 2
bookFlatSection: true
---

# **Notes on Bayesian Inference**

## **pip install -r requirements.txt**
## **scipy, jax.numpy**

### **Understanding Uncertainty via Probabilities**

- sum rule: P(X) = P(X,Y) + P(X, {{< katex >}}\neg{{< /katex >}}Y)
- product rule: P(X,Y) = P(X)  {{< katex >}}\cdot{{< /katex >}} P(Y | X)
- bayes theorem on data D:

{{< katex display >}}
\underbrace{P(X | D)}_{\text{posterior of X given D}} \hspace{.1cm} = {  \overbrace{P(D | X)}^{\text{likelihood of X under D}} \hspace{.1cm} \cdot \hspace{.1cm} \overbrace{P(X)}^{\text{prior of X}}}{ \underbrace{P(D)}_{\text{marginalization or evidence of the model}}}
{{< /katex >}}


### **Exponential Families** 

- reduce Bayesian inference to:
    - modelling: computing sufficient statistics {{< katex >}}\phi(x){{< /katex >}} and partition function Z(w)
    - evaluating posterior: assessing log partition function F of the conjugate prior

- if F is not tractable {{< katex >}}\Longrightarrow{{< /katex >}} use Laplace approximations:
    - find the mode {{< katex >}}\^{w}{{< /katex >}} of the posterior, by solving root-finding problems

     {{< katex display >}}
     \nabla_{w} log p (w | x) = \frac{\alpha + \sum_{i=1}^n \phi(x_{i})}{\nu + n}
     {{< /katex >}}

    - evaluate the Hessian {{< katex >}}\Psi = \nabla_w \nabla_w^T log p(w|x){{< /katex >}} at the mode {{< katex >}}\^{w}{{< /katex >}}

    - approximate posterior as {{< katex >}}\mathcal{N}(w;\^{w}, --\Psi^{-1}) {{< /katex >}} and the conjugate log partition function as:

      {{< katex display >}}
     F(\alpha', \nu') \approx \sqrt{(2\pi)^d |(--\Psi^{-1})|} \cdot exp[\^{w}^T \cdot \alpha' -- log Z(^{w})^T \nu' ]
     {{< /katex >}}


{{< hint important >}}
Laplace approximations reveal that Bayesian inference prioritizes capturing the geometry of the likelihood function around its peak (mode), rather than solely focusing on the prior distribution. In this context, uncertainty is better understood as encompassing the multitude of potential solutions simultaneously, rather than fixating on a single point estimate. It's about monitoring the breadth of plausible solutions. This means it is about observing the volume of possibilities rather than pinpointing individual points.
{{< /hint >}}