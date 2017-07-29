---
title: What Does A 4Hour Project Look Like?
date: 2017-04-15 20:04:23
categories: []
tags: []
---

At my company, we get 4h per week (10% of our time) to work on anything we feel like will help us become better at what we do, will help the community of software developers or will help our company be a better place.

I have realized that I rarely work on our internal tools myself. Most of my time goes into exploring new languages or frameworks, preparing blogs or talks, contributing to internal discussions, or working on my current-pet-project-of-the-week. Those projects wind up half-baked in a week or two. And to be honest, I spend more time outside of the 4h on Friday on them.

After a few of these pet project - which I always wished other people picked up to give them the longevity they deserve - I started wondering what would make a successful 4h project.

The scenario I have in mind is the following: You get 4h per week to work on anything. Because you spotted a bug in an internal tool, you decide to take those 4h to debug and fix it. You go into Github, clone the repo… and now what? How long does it take you to get the project up and running? How long until you are familiar enough with the code base to do something meaningful? Can you get the change onto production within those 4hours?

This blog post is the first pass at some questions that I will ask some of my friends and coworkers to figure out what leads to a successful 4h project:

- Language choice: Esoteric or established? Something that has a high likelihood to be used already?
- Frameworks: Is minimalism good? Or rather larger frameworks that cover more of the use cases that will be needed? Where is the line between value and bloat? And again: how does the degree of mainstreamness affect the choice? What impact do conventions (and possibly breaking them) have?
- Should we combine multiple languages/frameworks or stick to a single choice? What is the impact on the build system?
- Build tools setup: plugins or support for the most used development environments  (Vim, Emacs, Atom, Sublime, VSCode?)? Be able to run everything from the command line? Likelihood of reusing something that is already on the host machine? Ship the entire environment as part of a Vagrant box? What is the tradeoff between individual preference and homogenized "blessed" toolset? Does CI matter?
- Communication of design and tradeoffs: What needs to be in the README? How do we communicate previous decisions, assuming that different devs pick up the project? How do we help the next developer discover where the big pieces of code are? How do we establish conventions?
- How to create a shared understanding of the vision of the project? Empathy for the user? Establish a domain language?

All of this should also be balanced with the fact that we can't bombard the next developer with too much information. We have to avoid creating so much forest that we can't spot a tree anymore.

Stay tuned for more refinement on the 4h project and some ideas on how to progress towards that goal.
