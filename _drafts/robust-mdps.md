---
title: 'How Paranoid Is Your Android?'
date: 2025-10-18
permalink: /blog/robust-mdps/
category: research
---

*This post was written as an assignment for ASEN 6519, the Advanced Survey of Sequential Decision Making class, at CU Boulder. AI was used to help with research for this post.*

## A Motivating Example From Class: Robot Soccer

It's no longer a surprise when we learn that AI agents can accomplish superhuman intellectual feats. Computers have long dominated humans at chess. Since then, humans have also lost their superiority at progressively more complex games like Go, poker, and Stratego. The emergence of powerful chatbots with deep implicit knowledge bases and impressive abstract reasoning capabilities has helped rocket NVIDIA to a market cap of over $4 trillion.

But in the physical world, AI lags behind. Although robots can excel at well-defined tasks in controlled environments, more complex robots struggle to actually *learn* to succeed in complex real-world situations. In one recent triumph in the field, a Google DeepMind team trained robots to play soccer in a simulator, and then transferred the simulated policies directly to real robotic hardware[^socc]:

[^socc]: Haarnoja, T., Moran, B., Lever, G., Huang, S. H., Tirumala, D., Humplik, J., ... & Heess, N. (2024). Learning agile soccer skills for a bipedal robot with deep reinforcement learning. Science Robotics, 9(89), eadi8022.

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="en" dir="ltr">We just released our work on robot soccer. I&#39;ve been working on this for quite some time with my amazing colleagues at DeepMind. It&#39;s exciting how deep RL can produce such beautiful behaviors with low-cost robots. Full paper is available at <a href="https://t.co/A65rPLL7UD">https://t.co/A65rPLL7UD</a> Enjoy! <a href="https://t.co/n8j4uELhIL">pic.twitter.com/n8j4uELhIL</a></p>&mdash; Tuomas Haarnoja (@haarnoja) <a href="https://twitter.com/haarnoja/status/1651577815836270596?ref_src=twsrc%5Etfw">April 27, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

You may notice that these robots, while both cute and impressive in their way, seem to fall almost constantly.

The robot soccer project points to multiple key issues in robotic learning:

1. The need to transfer skills from a simulator to real hardware. Even this paper, which declares victory in zero-shot sim-to-real transfer, shows a measurable (although not extreme) drop in performance from sim to real.
1. Even when you have successful sim-to-real transfer, you can end up with learned behaviors which are qualitatively fragile. In a real application, a robot which is constantly falling is likely to need a prohibitive amount of maintenance.

This post aims to discuss theoretical frameworks and approaches to dealing with these important issues.

## Enter Robustness

Addressing problems of fragility and environment shifts falls under the broad category of "robust reinforcement learning (RL)." To go up one level of abstraction, robustness aims to address fragility arising from:

1. **Model misspecification**. Developers of AI systems often don't correctly model the world. Maybe they base their robot on an explicit physics model that makes simplifying assumptions. Even in highly flexible models which are meant to be learned, developers assume that experiences from the world (transitions and rewards) are i.i.d., and that the distribution from which these experiences are drawn is stationary over time. In sim-to-real transfer, it is this second assumption that is almost always violated, since there is a distribution shift when the system is deployed in the real world. A system which is robust to model misspecification can succeed when the real world follows  .
1. **Epistemic uncertainty**. Even when we've correctly chosen the *class* of model for the world (or at least, correctly enough), when we're trying to do reinforcement learning, we need to estimate the parameters of that model. Since we are basing our estimates on data, our estimates will have some error. A system which is robust to epistemic uncertainty will exhibit a kind of pessimism under this uncertainty. Notice though, we've changed the game a little -- whereas typically in RL, we're aiming to maximize expected discounted reward, when we factor in robustness, it's because we're asymmetrically averse to "unpleasant surprises". This is what's typically called "Safe RL". Approaches to mitigate model misspecification often can also be applied to mitigate epistemic uncertainty and make RL safer.
1. **Aleatoric uncertainty**. Once we're in the land of "Safe RL", and we've decided that we're asymmetrically risk-averse, there's no reason to stop at epistemic uncertainty. Robustness can also encompass a preference for avoiding known risks by taking actions which have only a narrow range of possible outcomes, all of which are acceptable.

So, robustness can refer to strategies for managing almost every kind of uncertainty we have in automated decision-making. The unifying characteristic of robustness is a focus on limiting the damage from worst-case, or nearly worst-case, scenarios. In short: paranoia!

## Robust Planning

The cornerstone of sequential decision-making, including RL, is planning ahead. Robust planning means planning for worst-case contingencies. We can formalize this with the robust MDP.

### Robust MDPs

If RL begins with the Markov Decision Process (MDP), robust RL begins with the... RMDP, or robust MDP. (See Suilen et al.[^rmdp] for an overview of RMDPs -- much of the content of this section summarizes that article). Robust MDPs focus on managing model misspecification, although in the learning context, they can also be useful for managing epistemic uncertainty, if we view the estimation error as a kind of model misspecification.

[^rmdp]: Suilen, M., Badings, T., Bovy, E. M., Parker, D., & Jansen, N. (2024). Robust markov decision processes: A place where AI and formal methods meet. In Principles of Verification: Cycling the Probabilistic Landscape: Essays Dedicated to Joost-Pieter Katoen on the Occasion of His 60th Birthday, Part III (pp. 126-154). Cham: Springer Nature Switzerland.

We recall that an MDP is defined by a tuple $$(S, A, T, R, \gamma)$$, where $$S$$ is the set of states, $$A$$ is the set of actions, $$T$$ encodes stochastic transitions $$T: S \times A \to \Delta(S)$$ and $$R$$ encodes (we'll say) deterministic rewards $$R: S \times A \to \mathbb{R}$$, where $$R$$ is frequently bounded on one or both sides.

A **robust MDP** is defined similarly by a tuple $$(S, A, \mathcal{T}, R, \gamma)$$. The difference is that instead of a single transition function $$T$$, you have a map $$\mathcal{T}: \mathcal{U} \to (S \times A \to \Delta(S))$$ to represent our uncertainty about what the transitions might be.

$$\mathcal{U} \subseteq \mathbb{R}^X$$ for some set of variables $$X$$, is called our uncertainty set. We can think of the uncertainty set as representing our set of possible variable assignments to the actual transitions. Typically, we'd take $$X = \{x_{sas'} \mid (s, a, s') \in S \times A \times S \}$$. So, for a given assignment $$f$$ from $$\mathcal{U}$$, and then get a transition probability $$T(s, a, s') = f(x_{sas'})$$. This diagram might help with understanding:

<img src='/images/RMDP-example.png' alt="RMDP Example" width=600px>

We can define uncertainty sets to indicate that the variable transition probabilities must obey certain constraints:

<img src='/images/uncertainty-sets.png' alt="Uncertainty Sets" width=600px>

(Images from Suilen et al.)

In a robust MDP, the planner must maximize expected reward under the **worst-case** realization allowable by $$\mathcal{U}$$.

### A Technical Detour

We notice that uncertainty sets can encode all kinds of dependencies between the unknown transition probabilities. These dependencies can make analyzing RMDPs difficult. So, we often make a simplifying assumption of *rectangularity*, or independence. We can consider either independence between states (*$$s$$-rectangularity*) or the stronger condition of independence between states and actions, called *$$(s, a)$$-rectangularity*. In the example screenshotted above, $$\mathcal{U}_1$$ is $$(s, a)$$-rectangular, but $$\mathcal{U}_2$$ is only $$s$$-rectangular. These conditions have implications for the difficulty of policy evaluation. For convex $$\mathcal{U}$$, rectangularity allows us to evaluate policies in polynomial time.

### Solving MDPs and RMDPs

If we have an MDP, the standard "value iteration" approach to reward maximization involves computing the *value* $$V(s)$$ of each state -- that is, the reward you can expect to get from that state if you behave optimally. We identify the value via an iterative computation called the Bellman operator, which provably converges:

$$
V^{(n+1)}(s) = \max_{a \in A}\left\{ R(s,a) + \gamma\sum_{s' \in S}T(s, a, s')V^{(n)}(s') \right\}
$$

In an RMDP, our goal again is typically to find a worst-case value across all possible transitions $$T \in \mathcal{T}$$. In other words,

$$
\underline{V}^{(n+1)}(s) = \max_{a \in A}\left\{ R(s,a) + \gamma \inf_{T \in \mathcal{T}} \left\{ \sum_{s' \in S}T(s, a, s')\underline{V}^{(n)}(s') \right\}\right\}
$$

With this seemingly small change to the Bellman operator, we've formalized the worst-case planning problem!

#### Why Rectangularity Helps

Notice that if we have an $$(s, a)$$-rectangular uncertainty set, we don't need to optimize over the entire uncertainty set -- only the possible values for the current transition:

$$
\underline{V}^{(n+1)}(s) = \max_{a \in A}\left\{ R(s,a) + \gamma \inf_{T(s,a) \in \mathcal{T}(s, a)} \left\{ \sum_{s' \in S}T(s, a, s')\underline{V}^{(n)}(s') \right\}\right\}
$$

which is a much easier optimization problem.

## Learning Robustly

Ok! So we have a formalism for worst-case thinking. But this post didn't just promise to talk about robust planning -- it started by talking about learning! When it comes to robust learning, there are a lot of very different approaches. Some of them are based on the RMDP formalism, some of them are more just practical techniques for robust learning. But I will try to touch on most of the major approaches to robust RL. As my reference, I'm using a recent survey of Robust RL.[^survey]

[^survey]: Moos, J., Hansel, K., Abdulsamad, H., Stark, S., Clever, D., & Peters, J. (2022). Robust reinforcement learning: A review of foundations and recent advances. Machine Learning and Knowledge Extraction, 4(1), 276-315.

### Learning Robust MDPs Directly by Learning Uncertainty Sets

One theoretically-grounded approach to robust learning involves *adaptive uncertainty sets* which become narrower as more data is collected. This comes back to the idea of viewing epistemic uncertainty as a kind of model misspecification. Robust value functions and robust policies, which are pessimistic under epistemic uncertainty, can provide high-probability guarantees of performance.

Uncertainty estimation can use confidence intervals from concentration bounds like Hoeffding's to build uncertainty sets.

Some research takes a Bayesian approach to learning RMDPs. In particular Derman et al. 2020[^bayes] propose a Bayesian DQN algorithm (along with some theoretical analysis) which promises both robustness and adaptiveness to non-stationary dynamics.

[^bayes]: Derman, E., Mankowitz, D., Mann, T., & Mannor, S. (2020, August). A bayesian approach to robust reinforcement learning. In Uncertainty in Artificial Intelligence (pp. 648-658). PMLR.

### Distributionally Robust MDPs

"Distributionally robust RL" takes a slightly different tack, and builds a confidence set based on KL divergence regions around the optimal policy from each state.[^drrl]

[^drrl]: Smirnova, E., Dohmatob, E., & Mary, J. (2019). Distributionally robust reinforcement learning. arXiv preprint arXiv:1902.08708.

In practice, truly robust, worst-case solutions, often lead to unacceptable levels of risk-aversion.

### Risk-Sensitive RL

Some 

- CVaR
- Conservative Q-learning

### Adversarial Training

- Adversarial training

## Connections to Games

Suilen et al. points out that the RMDP formalism can be expressed as a two-player, zero-sum stochastic game (SG) between the agent and an adversarial Nature which tries at every turn to minimize the agent's reward. The agent's move consist of actions in an RMDP, and Nature's moves consist of selections from the uncertainty set.

## My Big Questions

### Taking Paranoia to the Extreme

E.g. plan based on the transitions you've actually observed (maybe with some interpolation) and act greedily. Exploration is built-in. If the world is actually stochastic, then the less you've observed from a state, the higher your worst-case estimate is likely to be.

### Non-Stochastic Control

Can we abandon stochastic assumptions without going full paranoid? Can we continually adapt to the conditions we actually see? Hazan. Bowling.
