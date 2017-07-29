---
title: "Test"
date: 2017-07-28T22:35:30+01:00
#thumbnailImage: //example.com/image.jpg
---

There is something

<!--more-->

{{< tabbed-codeblock "this-is-the-filename.ex" "http://github.com/felipesere" >}}
 <!-- tab elixir -->
defmodule Question do
  defstruct [:callback]

  def new(callback) do
    %Question{callback: callback}
  end

  def about(subject) do
    %{subject: subject}
  end

  def answer(%{subject: subject}, question) do
    question.callback.(subject)
  end
  def answer(subject, question) do
    question.callback.(subject)
  end
<!-- endtab -->
<!-- tab test -->
defmodule QuestionTest do
  use ExUnit.Case

  test "questions can be answered" do
    question = Question.new(fn(x) -> x + 2 end)
    assert 4
           |> Question.about()
           |> Question.answer(question) == 6
  end

  test "questions can be refined" do
    first = Question.new(fn(x) -> x + 2 end)
    other = Question.new(fn(x) -> x * 2 end)

    chain = first |> Question.refined_with(other)

    assert 4 |> Question.about()
             |> Question.answer(chain) == 12
  end
end
 <!-- endtab -->
{{< /tabbed-codeblock >}}
