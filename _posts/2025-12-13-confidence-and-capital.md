---
title: "Confidence via Capital Growth"
date: 2025-12-13
permalink: /blog/confidence-and-capital/
category: research
---

This semester, I took a "special topics" graduate class through CU Computer Science on game-theoretic probability and statistics. "GTP" is a funky emerging branch of math. In its purest form, it argues that we don't need measure theory to understand probability. Instead of starting with the assignment of probabilities to events (and their unions and intersections) via a measure, GTP starts with betting games, where a player expresses their belief about the probability of an event by betting for or against the event at a given price. If gambling strategies exist which guarantee the gambler a certain gain, then we can define probabilities in terms of (the inverse of) gambler's certain gain. Theorems about probability (for example, theorems like the law of large numbers which show that some events have 0 probability) can be proven via general gambling strategies.

It's confusing stuff, and even after a semester of it, I feel like a novice.
But one thing that's really very curious about this area, is that the conceptual shift into betting and measuring probabilities via imaginary money, seems to yield real advances in statistics and machine learning, even if you still want to view the world in measure theoretic terms.
["Testing by Betting"](https://glennshafer.com/assets/downloads/articles/article104_jrss_shafer-testingbybetting-with-discussion-response.pdf) and e-values allow for [anytime-valid statistical inference](https://stat.cmu.edu/~aramdas/icml25/ramdas1.pdf), so experimenters can run multiple sequential independent experiments and make claims about confidence based on the value of an imaginary capital process. Basically, you pretend you're betting that the data will be more consistent with the alternative hypothesis than the null hypothesis.
This also means that if you have streaming data, you can estimate confidence intervals of the data in an anytime-valid way.

The idea of using capital as an estimate of confidence has a strange kind of circular relationship with online machine learning. On the one hand, regret-minimization algorithms (e.g. for sequential investing) [give us betting strategies](https://arxiv.org/pdf/2504.02818) which are useful for testing-by-betting. On the other hand, you can construct general [online learning algorithms](https://papers.nips.cc/paper_files/paper/2016/hash/320722549d1751cf3f247855f937b982-Abstract.html) if you view the prediction problem as a betting problem. Larger bets correspond to larger learning rates, so, in effect, you naturally adapt your learning rate to your confidence level. This whole circular relationship, it seems, has been under-explored.

I hope to continue investigating these questions. But even if I end up not specializing in the analysis of betting games (as I said earlier, this stuff is tough!), I think there is a broad lesson to be learned about the power of multiplicative weight updates to robustly track confidence in competing hypotheses as data is received online. [Arora et al.](https://theoryofcomputing.org/articles/v008a006/) may have been really understating the case when they wrote in 2012:
>We feel that [multiplicative weights] and its analysis are simple and useful enough that they should be viewed as a basic tool taught to all algorithms students together with divide-and-conquer, dynamic programming, random sampling, and the like.

Maybe, in our era of DraftKings and Polymarket, all multiplicative weights needed was a rebranding in gambling terms.

But maybe this is much deeper than a rebranding or reinvigoration of a style of algorithm. Maybe eventually, this line of thinking will deliver a hard blow to measure-theoretic probability and the view, held implicitly or explicitly in so many domains, that Nature "plays dice" with the universe.
