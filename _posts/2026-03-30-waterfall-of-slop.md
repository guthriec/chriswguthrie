---
title: "The Waterfall of Slop"
date: 2026-03-30
permalink: /hack-blog/waterfall-of-slop/
category: hack-blog
---

Progress on both [Kitpicks](https://kitpicks.com) and my academic research work has slowed a bit in the past few months, in part due to (happy) distractions in my personal life. But also, both projects are in this kind of bogged-down phase of being probably not-quite-ready for publication. I don't have much control over that call on the research front, but at least with Kitpicks, the decision to release the app is fully in my court. And I think it's in a place where there are no glaring bugs, although there are some pretty key missing features, and quality for both UI design and for the recommendations is much lower than I'd like.

Getting the app to a place of being a minimally decent proof-of-concept took some iterating, especially around app mechanics and information architecture. I like design, but I'm not a professional designer. I'm also probably too much of a "first principles" designer, in that, for example, I kind of had to reinvent the idea of bookmarks for myself to really be sure it was elegant and consistent with what I was going for.

Anyway, for this kind of routine iterative feature-work, I decided to try to see how hands-off I could be with my AI agents, in a system I've decided to call "The Waterfall of Slop." It's not really a waterfall methodology, more like test-driven development, but it's a fun name anyway.

Basically I have different Opencode agents for different phases of feature development (plan, test, implement, refactor), which are orchestrated on a self-hosted Github Actions runner, so I can trigger them via Github comments and PR approvals. I have it set up so each phase is its own PR, stacked on top of the previous phase. It results in a nice clean Git history, but I am amazed that Github doesn't have built-in support for stacked PRs. Big difference from Google work.

I chose to split up the different agents not so much to try to prime the model to do "test"-y or "implement"-y thinking (although they all do have their own prompts), but more so so I could constrain them to only operate on appropriate files. It has very much helped with keeping agents from "going rogue" and changing requirements or weakening tests without checking in first.

I try to do a quick sanity-check of the agents' PRs before approving. Even with just a surface-level pass, I do often find that there are glaring issues. Maybe I could get better one-shot reliability with more powerful models (I'm going for cheap and open-source right now), but also, I don't want to get addicted to a $200 / month subscription that has the potential to get much higher as the VC subsidies... subside.

Mostly I can intervene before things go very wrong, but a couple times, gaps in testing have led to a feature going off the rails and needing to be restarted.
This is the most waterfall-y aspect of my workflow, I think.
Small omissions in planning and design, if not caught, can cascade and blow up in later phases.
Models still lack common sense and, like, basic honesty.
[What I wrote a few months ago](/hack-blog/still-need-swes/) absolutely still holds -- models require (really pretty close) supervision by SWEs to produce reliable output.
