---
title: The ELM Confusion
date: 2017-02-12 18:00:22
categories: []
tags: [elm]
published: true
---

Today I think I may (possibly) have understood something about ELM. Before we look at some code, here is the 5sec intro into ELM: ELM is essentially a Haskell that runs in the browser. It makes an enormous effort to have a friendly and helpful compiler. And it succeeds at it! It promises zero runtime errors, has a strong type system and comes with a batteries-included architecture that is supposed to result in easy to maintain web apps.

### The setup

I am currently experimenting with porting a few Angular components over to ELM to see how it compares. One interesting aspect is that I get data from a remote system. From a previous experience, I had a hunch that 'async' code in ELM quickly got me confused with `Task`, `Cmd` and `Msg` (the last one is an app-specific custom type).

But this time all went smooth. Here is the code to read some data from an endpoint and parsing the resulting JSON:

{{< highlight haskell >}}
getData : Cmd Msg
getData =
  let
      url = "http://localhost:3001/people"
      request = Http.get url Data.decode
  in
      Http.send NewData request
{{< / highlight >}}

The focus point of this post will be that function and the inconspicuous NewData.NewData is a custom union type that represents what messages my application will react to:

{{< highlight haskell >}}
type Msg
  = MorePeople
  | NewData (Result Http.Error Response)

{{< / highlight >}}

The above code was just happily living in the initial **App.elm** file, which was fine for the beginning.

The challenge I set myself was to separate the concern _"getting data from a remote system"_ from the general flow of my application, which is encoded in an _update_ function and the corresponding _Msg_ type.

### Naive approach

Naive as I was, I just moved the `getData` function over to the **Data.elm** file (_a poor name in hindsight_) and fixed some of the imports. Sadly, the compiler was not happy about this:

{{< highlight sh >}}
Error in ./src/Main.elm
Module build failed: Error: Compiler process exited with error Compilation failed
-- NAMING ERROR ------------------------------------------------- ./src/Data.elm

Cannot find variable `NewData`

22|       Http.send NewData request
                    ^^^^^^^
Maybe you want one of the following?

    getData

-- NAMING ERROR ------------------------------------------------- ./src/Data.elm

Cannot find type `Msg`

16| getData : Cmd Msg
                  ^^^
{{< / highlight >}}

OK. I can understand that. I've obviously not imported `Msg` and by extension `NewData`. Hmm.. That type lives in the **App** module, which imports the **Data** module, which would import the **App** module, which imports… argh. Circular dependency.

Still on the naive path, I thought I could just extract `Msg` into a parameter of the function. That move made sense to me:  the **Data** module should just retrieve the data and not worry about how the main **App** will continue to process it. So this is what I thought I'd need:

{{< highlight haskell >}}
getData : msg -> Cmd msg
getData msg =
  let
      url = "http://localhost:3001/people"
      request = Http.get url decode
  in
      Http.send msg request
{{< / highlight >}}

As you can see, the function now takes a parameter and should be invoked as `Data.getData NewData` and produce a `Cmd Msg` . Oh, how nice that would have been. The compiler and I had a slight disagreement about the correctness of this code:

{{< highlight sh >}}
Module build failed: Error: Compiler process exited with error Compilation failed
-- TYPE MISMATCH ------------------------------------------------ ./src/Data.elm

The 1st argument to function `send` is causing a mismatch.

22|       Http.send msg request
                    ^^^
Function `send` is expecting the 1st argument to be:

    Result Http.Error a -> msg

But it is:

    msg

Hint: It looks like a function needs 1 more argument.
{{< / highlight >}}

Hmm? `send` expects a function that takes a `Result` with an `Http.Error` and something (that is the '**a**' ) over to `msg`?  That's odd. I had to double check that I had not deleted more than I wanted since all I had really done was introducing a new parameter.

### Understanding and reasoning

It took me a while to grok what was going on here. I kept staring at it and thinking _"where is that function coming from?"_. My first step was to just take what the compiler was saying for granted and changing the signature to match the error.

{{< highlight haskell >}}
getData : (Result Http.Error a -> msg) -> Cmd msg
getData msg =
  let
      url = "http://localhost:3001/people"
      request = Http.get url decode
  in
      Http.send msg request
{{< / highlight >}}

Which lead to an error about send not really liking a `Result Http.Erro`r a and preferring `Result Http.Error Response`. Replacing that `a` with a `Response` made everything snap into motion. Bare with me.

The `msg` parameter of the above function definition is supposed to be the message type that I wanted to abstract away from. The one variant that I had there initially is `NewData (Result Http.Error Response)`. That contains all the elements - though slightly rearranged - as the function that is passed as the first parameter to `getData`. And that is it! `NewData` is not an enum variant like in Java or Rust, it's a **type constructor**! It is a function that takes a `Result Http.Error Response` and produces a new instance of `Msg`. Spelling that out in Elm is `(Result Http.Error Response -> Msg)`. To not depend on the type itself, it is extracted both in the function and the resulting`Cmd msg`.

This post might some long-winded followed by a very quick conclusion. Keep in mind, it took me a few hours and attempts to get to the point where it all `clicked`. Then it was super easy. In hindsight, if I had just followed the compiler maybe the pieces would have fallen into place faster. Live and learn!
