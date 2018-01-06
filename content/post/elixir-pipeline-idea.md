---
title: "Transformation Pipeline"
date: 2018-01-06T10:40:06Z
categories: []
tags: [elixir, design, idea, fp]
keywords: []
coverImage: https://www.clariant.com/~/media/Images/Business-Units/Oil-and-Mining-Services/Oil-Services/hero-pipeline.jpg
coverSize: partial
---

For a new project I've been thinking about an idea/abstraction/pattern that is really not new (few things are anyway)
but I thought might be worthwhile writing about before running off and executing on it:  A `Pipeline` in Elixir.

<!--more-->

The current goal we want to achieve with the project is to tap into multiple buckets of data, transform it if needed, apply some validations and then lay the data to rest in a common format.
That is the very high level description from about one week on said project.
What I do know, is that we will tap into multiple sources, that reach of some different channels, such as

 * a Web API with JSON-over-HTTP
 * a zip file getting FTP'ed onto a folder
 * or our system doing a query against a third-party system

The transformations we need to do are likely temporary, as the other systems converge on the format we like the most.
Hence, we might need to add more transformations or remove other throughout the lifetime.

The validations on the other hand will depend on the data we pass through. They probably check the shape of the data, not necessarily the contents.
For example, we will need to check that something is a non-negative currency in either U$D, EUR, GBP, or BRL, but not whether the value is greater than something stored in a DB somewhere.

We are thinking of two places where we want to put data to rest, with different retention characteristics.

The code I had in mind would roughly look something like this:

{{< highlight elixir >}}

defmodule Partner1Job do
  def execute do
    pipeline = Pipeline.new(name: "Partner1 worker")
             |> Pipeline.from(Partner1Source, ["this-cool-table", starting_at: 213, page_size: 50])
             |> Pipeline.through(Partner1Transformations)
             |> Pipeline.validate(GroupOfValidations)
             |> Pipeline.write_into(FileCabinet)


    Scheduler.run!(pipeline)
  end
end

{{< / highlight >}}

In the example above, we create a new `Pipeline` with some parameters like a name (_useful for logging later on?_) and then configure
where the data comes from, what transformations to apply on the way, what validations to perform and finally where to write the data to.

## We have pipelines already `|>`

True, but they are only a syntacitc language construct.
Elevating the _idea_ of the pipeline allows us to have conversation about `Sources`, `Transforamtions`, and `Sinks` of data.
What needs to be common among all partners? What will require a special-casing?

It also means we have a common place to attach behaviour that should apply to all pipelines.
For example, we can add logging to every single stage and use the `name` from the pipeline in the logs.
We could also add a common place to write any kind of error to and thus standardize a common log format.

Just writing this out as a simple `|>` means we'd have to be mindful of this in all places.


## Change what needs changing

I imagine that most `validatios` and `Sinks` will be the same across most pipelines.
The two things I foresee having some variations, are entry points (Web, FTP, timed trigger) and different transformations.

Let's look at what a running a HTTP request from Phoenix through the pipeline could look like:


{{< highlight elixir >}}
defmodule DataApiController do

  def init(...) do
    pipe = Pipeline.new(name: "Data-Ingestion-API")
           |> Pipeline.validate(GroupOfValidations)
           |> Pipeline.write_into(FileCabinet)
    [pipeline: pipe]
  end

  def some_action(conn, params, pipeline) do
    result = pipeline
             |> Pipeline.from(WebAPISource, params: params)
             |> Pipeline.through(PlugEnhancer, plug: plug)
             |> Scheduler.run!()

    json result
  end
end
{{< / highlight >}}

Here I'd maybe split the configuration of the pipeline into sections that are the same across all requests and sections that
require data from the request itself.
I'd still have the notion of a `source`, just that this one comes from the `WebAPISource` and contains the rules on how to convert `params` into whatever flows through the pipeline.
Using a `through(PlugEnhancer, plug: plug)` I'd enhance the pipeline with any details from the `plug` itself, such as values of heaers or hostnames or timestamps.

A `Scheduler` would then take the pipe and run it. This is also a good expansion point to provide different ways of running the pipe.
Maybe we don't care about the result and can do something like `Scheduler.run_later(pipeline)` or maybe `Scheduler.run(pipeline, at: :fullhour)`.
Or even `Scheduler.run(pipeline, every: 15, :minutes)` if we want to launch a background task for a periodic job.


If you know of a library that does this already in Elixir or any other language, please send me a Tweet, so I can use it as a source of inspiration.
Watch this space!
