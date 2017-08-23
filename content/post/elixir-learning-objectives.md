---
comments: false
showTags: true
showPagination: true
showSocial: true
showDate: true
tags: [fp,oop, elixir]

title: "Elixir Learning Objectives"
date: 2017-08-23T22:44:19+01:00
draft: false
---

Over the last week I've picked up a project I had sidelined for a while.
Since I hadn't looked at the code in while and I knew was part way into a particularly
tricky pull request (changing the way a core structure is persisted to disk and read back)
I looked around a bit.

I was surprised that the code did not _feel_ in as good a shape as I thought I had left it.
Worse, it wasn't as good as I had set out to keep it. This is no client code, so there was no pressure to
release any faster than I felt comfortable. Yet it was there, that nagging "Hmm.. This looks... odd".

My hunch is that there are three lessons I still need spend thought-cycles on for Elixir:
* _Smells and Refactoring_: develop a better taste what good code looks like and what are safe operations to get there
* _Data structures and modelling_: I feel like I spend too much time shaping the data rather than coming up with a good structure once that I can leverage effectively. I probably still think too much _Object-Oriented Design_, too little _Functional Programming_. I also don't have a good gage of when to use `List`, `KeywordList`, a `Map` or a `Struct`.
* _Size of modules and functions_: My modules tend to get bloated. They are few in number and long in lines. My hunch is that I need to much more liberal with what gets extracted into its own module. This brings about a naming issue, that I also need to tackle.

As I come up with thoughts, ideas, approaches or articles that prove helpful in becoming better at these topcis, I'll share them here. Watch this space!
