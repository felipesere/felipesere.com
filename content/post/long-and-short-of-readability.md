---
title: "Long and Short of Readability"
date: 2017-09-04T18:29:09+01:00
categories: []
tags: [design]
keywords: []
#thumbnailImage: //example.com/image.jpg
coverImage: /images/lego_depth.jpg
coverSize: partial
coverMeta: out
draft: false
---
Today we talked through a rewrite of a largish chunk of legacy code.
This code had been written over the past few years, with a fair amount of hard-codings, rules, and decisions baked in.
Precisely those hard-codings, implicit rules, divergent decisions were the reason we proceeded with a rewrite.
Our preferred approach was radically different making a straight refactoring uneconomical.
Plus, our use-case was isolated enough that we could afford testing our ideas without impacting current users.

<!--more-->

So much for the context.
An interesting observation made during our show and tell was that the code was hard to read and consequently to debug.
That struck me as odd.
My reaction to the old code was exactly the same: _"Hmm. I am not sure I know what I’m looking at here… where would the… hmmm?"_
I believed our code to be obviously superior (“obvious” is a risky assumption for another blog-post day!), as we had cleanly separated the core algorithm (transforming a large data-structure) from specific hard-codings, rules, and decisions.
We made all of these explicit in their own well-named, little class.
I’ve come to realize that I and the other developers have a vastly different understanding of what legible code means.

# Abstraction skeptics and LEGO builders

The title refers to the two extremes along an axis of readability: On the one side, abstraction skeptics that prefer details not be hidden and LEGO builders that prefer to spread a problem over multiple files.
First things first: neither of these positions is strictly better than the other. It's a matter of experience and preference where one lands.

## Abstraction skeptics

These tend to be developers that come from a low-level, systems-programming background where every detail mattered - because the language afforded it or because the tooling didn’t allow any better.
They are skeptical of abstractions put in place to hide such details.
Maybe they were burnt in the past by an abstraction hard-coding something that it shouldn’t have, or forced to rewrite a chunk of their code due to the evolution of an abstraction that didn’t cater to them.
Or, they might be so detail oriented that they dislike the notion of not "seeing all the details" at once.
They are likely to be good at blocking unnecessary details (like branching and unrelated variables) as they scan through code.
These developers will favor the ability to immediately answer detailed questions about the code over having to skip across multiple files or being denied an answer "because it doesn’t matter at this level".

## LEGO builders
These tend to be developers coming from high-level languages with a strong background in testing, like Ruby, JavaScript or a functional language like Haskell or Clojure.
They enjoy decomposing a problem into many small sub-problems that get composed back to solve the whole.
“Reuse” and “abstraction” is their creed.
The goal is two-fold: avoid restating the same concept twice in the codebase and maximise the signal-to-noise ratio in a file by pushing unnecessary details away.
They will enjoy formulating abstractions upon abstractions, putting a significant amount of effort into module, class, method and variable names.
To cope with the multitude of files, they are very quick to navigate a hierarchy of types or files, often jumping between multiple files to solve a single problem.
The degree of decomposition is often brought in to test the smaller unit code.


## Meeting in the middle
For both ends of the readability spectrum it's hard to the others code and it is even harder to understand the reasoning behind it.
The key is not to give in to either extreme, but to understand where the balance lies.
Just enough to accept the code and not resist it irrationally.
If navigating a LEGO code base is hard, leave some detailed READMEs in place to help skeptics find the right place for a class.
Maybe reference the test cases that show the strengths (and drawbacks?) of the abstractions.
Show them how to find files in their IDE of choice.
If you are in a code base with few large files, resist the urge to refactor everything.
There is a good chance that you’ll need the detailed understanding of a skeptic to abstract away very targeted pieces of code.
