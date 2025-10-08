---
layout: single
date: 2025-10-07
title:  "Kitpicks Begins!"
permalink: /hack-blog/kitpicks-begins/
category: hack-blog
---
After 5 1/2 years of working at Google, I decided to take a [buyout](https://www.cnbc.com/2025/06/10/google-buyouts-search-ads-unit.html) to pursue full-time graduate school.

And school is great! But "building useful things" is a muscle that can atrophy. I want to be sure that I maintain some practice of product development while I also test my limits academically.

So, I'm publicly committing myself to delivering a side project. I've worked on a lot of side projects in the past, but always stopped short of shipping or publicizing them. Usually, this is because I come to realize that what I've built is hard to explain and/or of dubious market value. In some cases, projects were more a vehicle for technical experimentation than anything else. My goal this time around is to build an app which provides some clear market value, even if it's niche and has a low ceiling in terms of [TAM](https://en.wikipedia.org/wiki/Total_addressable_market). At least I'll use it!

## What will Kitpicks be?

When I was at Google, I worked on the "Discover" feed (which can be found in the Search apps for iOS and Android, as well as the "-1 screen" to the left of the home screen on most Android distributions). This feed shows users articles from around the Internet which Google has scraped and analyzed, and which Google thinks will match the user's interests.

For my "hacker" practice, you might think I'd choose some dramatically different product category -- you know, try something new. But no! My aim is to build a web discovery feed. Specifically, I want to build an[other](https://en.wikipedia.org/wiki/Comparison_of_feed_aggregators) RSS reader.

What unique twist can I put on a technology that has been done many times over? Well, among the vast landscape of RSS readers, I don't know of any app which is both algorithmically ranked *and* fully local (and therefore privacy-first)[^1]. Seems feasible to me!

[^1]: Claude did point me to an open-source solution which is similar in spirit to what I want to do: <https://github.com/permacommons/habitfeed>. However, this system requires technical setup, and the setup isn't seamless (at least on MacOS, it's not... there were some issues with the default sqlite).

## What does success look like?

If I can consistently get one download per day, I'll be happy. That will be when I might start thinking about premium versions and monetization.

## Technical principles

In my work (both side-project work and paid work), I've had a tendency to over-engineer and prematurely generalize things. I'm always trying to make things hyper-portable, anticipating all sorts of future use cases, etc.

I want to take a different tack with this project. I'll try to resist some of my delicate SWE sensibilities and focus on getting things done fast. This means:

- standard tooling
- only one language (Typescript)
- "bi-lithic" architecture (kind of like monolithic... there should be 2 Node.js applications: one Electron client, and one cloud server to host the client binary and handle feedback/crash reports/error logs)
- use Github Actions for all devops automation; no funky self-hosting
- rapid prototyping for UI development; use Github Copilot for this

For all my aversion to interpreted languages and my hesitancy to send money to Github, I'm kind of excited about this approach. I think it will be particularly interesting to implement the recommender system math in Typescript. I'm also excited to prove to myself that it's feasible to use modern AI techniques (particularly LLM embeddings) in client application software.

## What's next?

My plan is to work "outside-in," iterating on the user interface and value proposition before getting into the business logic. (This, by the way, is also different from how I've previously managed my side projects). So next steps are to rig up a landing page and begin rapid prototyping with dummy data. Stay tuned for links and screenshots!
