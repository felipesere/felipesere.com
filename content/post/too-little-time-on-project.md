---
title: "Too Little Time on a Project"
date: 2017-08-30T21:50:48+01:00
categories: []
tags:       [personal design project]
keywords:   []
coverImage: /images/dunes-264616_1920.jpg
coverSize: partial
---

A while ago I wrote about what I thought it would take to have a code base be maintainable [within a 4h window]({{< ref  "post/2017-04-15-what-does-a-4hour-project-look-like.markdown" >}}).
I only got as far as wondering about certain aspects: What languages should be used? What frameworks? What about build tools?
In experimenting on my code base, I have come up with what I believe to be good reasons to spend _more rather than less_ time with your pet project.

<!--more-->

# Stagnant code base

Because I was the only person working on the project for a limited amount of time per week, there was only so much code-movement (refactorings, renaming, file movements, deletions,...) that was happening.
As a result, the modules and functions were hard and tedious to refactor in the first place. It almost sounds paradoxical, but...

 > _…to refactor, one must first refactor_.

Consistently adding changes to the code base means that over time the system will become more receptive to those kind changes.
As we separate some responsibilities and decouple some modules, it will be less of a chore to rename functions as they are not used all over the system.
As such, it will become easier to find more accurate (or generic!) names for elements in the code and pull through with that change.


# Shallow abstractions

I found that as I was more focused on getting features done, I spent less time improving the underlying data structures and abstractions.
As such, there is currently too much code creating different perspectives of the same data to be used in different places.
There is so much effort spent in extracting, mapping, inverting, sorting the same data.
This highlights to me that my code is very _mechanical_. The abstractions are so shallow - revealing loads of `Strings` and `Atoms` and `keys` and `maps` - that they could almost be absent.
Good abstractions should let the reading flow through the application, giving them the right information at the right time.
If the reader is interested in some specific detail, they should should be able breeze into the module, skipping all the other high-level stuff.

# Naive, not simple

In a rush to get features done in as little time as possible, I stuck to the things I knew in Elixir. That essentially boils down to functions and modules.
While I know of some of the more prominent libraries and frameworks out there, I did not feel I had the time to explore them properly and see how they could solve my problems.
As such, I used the tools I had very naively. I did not setup proper `associations` in Ecto and as such have to keep loading, pre-loading, and reloading different structures based on their id.
Not investing time in leveraging the tools at my fingertips meant I had to spend more time adding code  for trivialities (or missing out on features I now wish I had...).

# Smallest coding-unit?

So going into this pet project I thought I'd explore different ways in which optimising for short development stints would make my code more approachable, maintainable, and evolvable.
As it turns out, getting approachable, maintainable, and evolvable code takes quite a bit of time and a substantial amount of experience.
Granted, experience and comfort in the language will off-set some of the time.
While I still believe there is a way and a technique to write a system in such a way to make it less time-consuming to contribute, I haven't found it. 
What I have found is that I need to spend _more, not less_ time on that project to learn the tools and tricks that will speed me and fellow contributors up.

> PS: The reasons above also line up with my observations on the code base of some of the client projects. They feel like they are optimising to able to write, not to write well and cleanly.
> While they are still working on their project at an acceptable pace, my believe is that they could get away with much less _effort_ if they invested in simplifying their code and finding better structures.

