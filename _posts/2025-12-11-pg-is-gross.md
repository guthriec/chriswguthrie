---
title: "Policy Gradient Can Solve the Putnam Exam, but Ben Recht Thinks It's Gross. I Might Agree with Dr. Recht."
date: 2025-12-11
permalink: /blog/pg-is-gross/
category: research
---

This past semester, I finished up a 2-course sequence on sequential decision-making and reinforcement learning through the CU Aerospace department.

The second course in the sequence involved reading through landmark papers in RL and games. In the past 5 years or so, researchers have used general-purpose RL to master board games like [go and chess](https://deepmind.google/blog/muzero-mastering-go-chess-shogi-and-atari-without-rules/) and [Stratego](https://arxiv.org/abs/2206.15378), video games including [Minecraft](https://arxiv.org/abs/2301.04104) and [Starcraft](https://www.nature.com/articles/s41586-019-1724-z), and some physical robotics games like [drone racing](https://arxiv.org/abs/2310.10943).

And although this course didn't dive too deeply into RL for LLM reasoning, that too has been a recent success of the field ([Stanford CS's RL Course](https://cs224r.stanford.edu/slides/10_cs224r-rl_for_reasoning_lecture.pdf) has some material available about this). It's critical to LLM's ability now to [solve](https://arxiv.org/html/2511.22570v1) some of the hardest math tests in the world.

A consistent theme throughout the course has been the ability of RL (given enough computation) to achieve superhuman ability in all kinds of complex domains.

Another consistent theme has been that these algorithms are insanely finnicky.
Even the successful papers usually involve a long list of little tricks to get things to work.
It's conventional wisdom that RL is sample inefficient.
It's also becoming conventional wisdom that the outer loop in RL, the one where practitioners search across hyperparameters and variants on algorithms, is itself quite inefficient.

Even standard algorithms like PPO have insane numbers of hyperparameters that require careful tuning.
In a final project for this class, I found that simply switching the reward scheme on a maze problem from "negative distance from the goal" at each point to "change in distance from the goal" disrupted the model's tuning and caused it to fail to learn consistently, even after rescaling the rewards, and *even though* the "change in distance" scheme is the [theoretically grounded one](https://people.eecs.berkeley.edu/~russell/papers/icml99-shaping.pdf) and is supposed to be more stable.
Getting stuff to work in RL is just a pain.

What to do about this situation?
Ben Recht has had a few recent blog posts about RL.
He seems pretty willing to [entirely throw out policy gradient methods](https://www.argmin.net/p/theres-got-to-be-a-better-way) in favor of optimization over learned world models. As Recht puts it, the formula is simple:

> 1. Build a model of the environment using standard predictive tools.
> 1. Optimize as if the model were true.

I have a few responses to this idea.

First, optimizing over models of the environment might work for small action spaces, but when the agent has enough choices and the environment has enough inherent complexity (so small changes in the agent's choices can have big impacts on the environment), then the direct optimization problem likely will just take too long, and the agent won't be able to make decisions fast enough. Policy gradient with neural network approximations can enable agents to make good reactive decisions that are approximately optimal.

Second, developments like Dreamer, in which policy gradient agents train themselves against a learned world model, promise to resolve some of Recht's concerns. Who cares if policy gradient is sample-inefficient, if an agent can be continually trained on an infinite supply of unbiased samples.

I also think Recht's emphasis on certainty equivalence ignores one of the primary motivations for RL, namely, reward-directed exploration.
An agent should have some principled way of accounting for the information value of its actions, which shouldn't just be bolted onto a classical control approach.
That said, even in modern policy gradient algorithms, exploration sometimes feels like an afterthought...

Ultimately, even if Recht's point of view is too radical or too dismissive of policy gradient, the RL community has very much opened itself up to this kind of criticism by promising general-purpose control algorithms and instead mostly delivering game-playing bots which usually take weeks (on large and expensive clusters) to train, and an order of magnitude longer to develop.
At some level, Recht is right -- there must be a better way.
What's less clear to me is whether this better way involves finding more adaptive policy gradient approaches which can regulate their own hyperparameters during training without manual tuning, or whether (as Recht suggests) this better way means replacing policy gradient with classical planning, over environment models.
I must admit though, after wrestling for days with an uncooperative actor-critic algorithm on a task that seemed like it "should be" easy, I might be leaning towards Recht's view.
