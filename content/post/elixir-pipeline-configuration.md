---
title: "Elixir Pipeline - Configuration"
date: 2018-01-14T21:40:25Z
categories: []
tags: [elixir, design]
keywords: []
#thumbnailImage: //example.com/image.jpg
coverImage: https://www.portent.com/images/2016/03/Old-tools-on-a-wooden-table-000046154920_Full.jpg
coverSize: partial
---

<!--more-->


In the previous post I outlined the kind of pipline I wanted to achieve. Here is a sample from my unit tests:

{{< highlight elixir >}}

test "can apply a transforamtion throught a module" do
  container =
    Pipeline.new()
    |> Pipeline.from(TestSource)
    |> Pipeline.transform(AddAllKeys)
    |> Pipeline.transform(fn(data) -> {:ok ,"The value is #{data[:sum]}"} end)
    |> Pipeline.write_into(TestSink)
    |> Scheduler.run()

  assert container.data == "The value is 7"
end

{{< / highlight >}}

What I particularly like is that I can plug in entire modules as `source`, `sink`, and `transforamtion`.
This means the pipline definitions can stay really high-level, while the details are explained in the lower-level module implementations.

One part that I thought was interesting is how configuration can float about with only having function and data-structures at our disposal.
Because you may not be able to see if from the sample above, each `source`, `sink`, and `transforamtion` can configure itself _and_ take external configuration from the call-site.

Below we can the `Pipeline.Source` module and its `__using__(params)` macro:

```
defmodule Pipeline.Source do
  @callback init(any()) :: any()
  @callback fetch_data(any()) :: {:ok, any()} | {:error, any() }

  defmacro __using__(_) do
    quote do
      @behaviour Pipeline.Source

      def init(params), do: {:ok, params}

      def fetch_data(_) do
        raise "fetch_data/1 was not implemented for #{inspect(__MODULE__)}"
      end

      defoverridable [init: 1, fetch_data: 1]
    end
  end
end
```

We can see that it defines two functions that any source must have: `init/1` and `fetch_data/1`.
Both functions take a `params` keyword list argument and the relationship between these two is particularly interesting.

The lifecycle of this module is that the pipline will first call `init/1` with whatever parameters were giveen to the pipline. For example...

{{< highlight elixir >}}
Pipeline.new()
|> Pipeline.from(TestSource, retries: 12, back_off: :linear)
{{< / highlight >}}

...would mean `init/1` gets called with `[retries: 12, back_off: :linear]`. In here is where the first bit of niftinyess comes in, inspired by Erlang and Elixir: _init/1_ can use those parameters, interpret them, expand on them or even load entirely new ones form the environment. The only mandate is to return a tuple `{:ok, params}` back to the caller.

Those params in turn, are passed right back in to `fetch_data/1`, _when the caller decides its time to call it_.
It almost feels like a stateful object (after all, we are holding on to _state_ of _someting_) but is build entirely on function and immutable data structures.

Here you can the see the bit of code that calls `init/1` and then holds on to the parameters for later use:

{{< highlight elixir >}}

@spec from(Pipeline.t, module(), keyword()) :: Pipeline.t
def from(pipeline, module, params \\ []) do
  %Pipeline{pipeline | source: initialize(module, params) }
end

defp initialize(module, params) do
  case module.init(params) do
    {:ok, new_params} -> {module, new_params}
    e -> raise "Error when initializing #{inspect(module)} with params #{inspect(params)} resulted in #{inspect(e)}"
  end
end

{{< / highlight >}}

What we store in the `%Pipeline{}` struct is precisely the tuple of _what module we called_ and _what params we got back from its init_.

When we then decide to execute the source, we see that both elements are brought together and some metadata from the pipline itself is merged in too:

{{< highlight elixir >}}

defp call_source(%Pipeline{source: {module, params}, container: container} = pipeline) do
  Logger.log(:info, fn -> "Reading data from source (#{inspect(module)})" end)

  data = module.fetch_data(merge(params, pipeline))

  %{ pipeline | container: Container.update(container, data, :source) }
end

{{< / highlight >}}

And here is the key takeaway from me:

> I can lift all commonly used configuration into the init/1 method, yet for testing I can pass any bespoke parameters directly to fetch_data/1 without having to mess with any _dependency injection_ system.

By cleanly separating configuration from execution, I got testability for free,

Let's say my `RemoteHTTPSource` uses an HTTP library underneath. For my testing, it would be annoying if I had to spin up a remote server (or use [bypass](https://github.com/PSPDFKit-labs/bypass)) just to test how it reacts to different responses.
If I lift the depdency into a parameter it can be swapped in tests, like so:

{{< highlight elixir >}}
defmodule RemoteHTTPSource do
  use Pipeline.Source

  def init(params) do
    client = Keyword.get(params, :client) || Application.get_env(:my_app, :http_client)
    {:ok, [client: client]}
  end

  def fetch_data(params) do
    client = Keyword.fetch!(params, :client)
    ...
  end
end
{{< / highlight >}}

My unit test can then pass in a `:client` that has the same behaviour as a keyword in the `params` of `fetch_data/1`.

This pattern is not really new as its widely used in Erlang and Elixir.
Rediscovering it and understanding what _I_ can get from it (testability, predictablity) has been a blast!
