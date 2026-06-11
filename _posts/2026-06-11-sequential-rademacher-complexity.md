---
title: "Sequential Rademacher Complexities"
date: 2026-06-11
permalink: /blog/sequential-rademacher/
category: research
---

In the spring, I took [a class](https://github.com/stephenbeckr/ML-theory-class) on statistical machine learning theory.
It covered two of the big ideas in passive statistical learning theory, the first of which is VC dimension, a clever way of measuring learnability of classification problems.

The other big idea is the Rademacher complexity, which works for continuous settings.
Basically, the Rademacher complexity tries to measure how well a hypothesis class can adapt to inversions of the loss function over $m$ datapoints drawn from a fixed distribution.
In Rademacher complexity, given a set of points, we randomly choose whether each point has the normal loss function or an inverted loss function, and then pick the best hypothesis matching this new mix of losses.
This effectively measures the ability of the hypothesis class to adapt to arbitrary variations in the labels, and leads to quantitative bounds on generalization error.

On top of Rademacher complexity is built a deep theory of covering numbers (and other ideas like Littlestone dimension or fat-shattering dimension) to help bound Rademacher complexity in terms of properties of the hypothesis class.

My final project for the class focused on [a remarkable extension](https://jmlr.org/papers/volume16/rakhlin15a/rakhlin15a.pdf) of this idea in the online (adversarial) learning setting, called sequential Rademacher complexity.
It turns out that one can build an entire parallel system of Rademacher complexities in the adversarial world, by not just inverting the loss function at each point, but by allowing the adversary to decide how to react to each inversion.
The authors of the paper then show how to get optimal upper bounds on regret for standard algorithms using this complexity (up to constant multiplicative factors).

As presented in the linked paper, sequential Rademacher complexity is defined only for standard external regret.
But, in a companion paper, the authors [show](https://proceedings.mlr.press/v19/rakhlin11a.html) that many other kinds of regret can be accommodated by (a slightly modified) idea of Rademacher complexity, including many kinds of regret which are useful for achieving game theoretic equilibria or other desirable properties.

In the most general of these approaches, the value of the adversarial learning game is roughly of the form:
$$
\inf_{\text{Alg}} \sup_{x_{1:T} \in \mathcal{X}^T} \left(\sum_{t=1}^T \ell(\text{Alg}(x_{1:t-1}), x_t) \right) - \inf_{\phi \in \Phi_T} \sum_{t=1}^T \phi_t(\ell)(\text{Alg}(x_{1:t-1}), x_t)
$$
where $\phi_t$ transforms the loss function at each round.
That is, the comparator involves optimizing over some set of transformations of the learner's choices and the adversary's choices at each round.
(The authors do point out that you can generalize the way you combine the loss function beyond just a sum, but critically, this way of combining losses must be the same for both the main loss term and the comparator term).

In such a case, sequential Rademacher bounds apply, and they are often (but not always) tight.

However, one could do interesting learning (not exactly no-regret learning), by playing a game of the form:
$$
\inf_{\text{Alg}} \sup_{x_{1:T} \in \mathcal{X}^T} \left(\sum_{t=1}^T \ell(\text{Alg}(x_{1:t-1}), x_t) \right) - \phi(x_1, \dots, x_T)
$$
In this world, the restriction which helps us say interesting things about learnability is that the comparator term only depends on environment outcomes.
However, the way this comparator term combines outcomes at different timesteps, has no relation to how individual losses are combined across different timesteps in the first term.
As with a result in binary prediction from Cover (see [Sasha Rakhlin's tutorial](https://www.mit.edu/~rakhlin/papers/tutorial.pdf)), this framing allows one to design an online learning problem by encoding certain prior structural assumptions about which sequences are more likely to occur (or which are more important to ``get right") via the function $\phi$.

This definition of the online learning problem, as well as the situations where the sequential Rademacher bounds don't tightly bound the value of the game, are currently a central theme in my research...
