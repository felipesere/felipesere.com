---
title:  "Testing HTML5 components"
date:   2016-01-25 13:04:23
categories: []
tags: ["html5", "testing"]
---

I've recently started working on a front end application and with my pair we decided to give HTML5 components a try.
While my pair had some experience in the JavaScript world, I had done little to no development in this area. My area of expertise
is in backend code. 
As such, I only knew about some of the pains in testing front end code that is tied to the DOM. 

We started writing a very small, simple component that would take a set of options and display them as a list of checkboxes. 
When something is clicked on the component, an event is bubbled up to the parent component to react to the new selection. 
Initially, we deferred testing it as we had no idea about options we had how to build it at all. 
Once we had something that behaved the way we wanted it, we turned back to how to test it. 

Our first instinct was to test mostly on the controller level (yes this is on a slightly dated version of Angular). 

        "How do get to the controller?"
        "Why do need to the controller?"
        "Well, I want to test the isExpanded variable changes when the checkboxClicked callback is called.."

There was something about trying to "...get the controller" from underneath the component.
From a few quick searches, we knew we'd have access to the finished HTML of the component and with _.click()_ we could _emulate_ user interaction. 
So we reconsidered our testing strategy.
All our components should somehow enable some interaction, and that interacting should result in some kind of change in the HTML. With that assumption, we wrote our tests from the perspective of a user:
  * Interactions triggered by _clicking_ on things
  * Assert changes on the HTML
  * Assert that a callback propagates data out of the components 
  
As such we were tying our tests to the DOM, but were not creating artificial seams by "only" testing the controllers. 

## Why is this a viable approach with components?
Our premise is that components entirely driven by input data and don't hold their own state. 
They are given data that they display and propagate events when things happen within it. 
Also, the size of the components also plays are role. They are tiny. We made a conscious effort to decompose our application into many small components. 
There is little "cognitive distance" between the test titles, how the tests are implemented, and the implementation of the component.  


## Outlook...

The future will whether our assumptions about testing components hold. The application will keep growing so we'll keep growing our Test suite and knowledge. 
