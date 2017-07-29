---
title: Fast feedback with RSpec and rerun
date: 2017-01-25 20:31:36
categories: []
tags: ["testing"]
---

Recently [this](https://twitter.com/CharlotteBRF/status/824251562428076034) tweet scrolled across my time-line about using `--fail-fast`

<!--more-->

{{< tweet  824251562428076034 >}}

Using `--fail-fast` with RSpec means the very first failure that RSpec encounters will halt the entire suite.
Passing tests give a sense achievement, but failing tests show where more work is needed.
Furthermore, getting many cascading failures (indicative of a potential design issue!) are distracting and overwhelming.
Using `--fail-fast` limits the amount of failures we need to concentrate at any given time.

But there is more.
Switching between your editor and terminal, followed by running `rspec --fail-fast` (potentailly hitting up-arrow multiple times) adds
an additional context switch and is distracting. I catch myself "over-tabbing" to my twitter timeline in Chrome every once in a while, just saying.
What if we didn't have to manually run our tests?
Enter [rerun](https://github.com/alexch/rerun).
Install it with

```sh
   gem install rerun
```


and then run

```sh
  rerun -cx "rspec --fail-fast
```

Let's decompose that:

  * -c will clear the screen, so you are not distracted by previous successes/failures
  * -x expect rspec to 'exit', meaning we know its a one-shot command and not a server (that needs to halted)

There are a couple of ways to improve this even further.
For example, if you install [terminal-notifier](https://github.com/julienXX/terminal-notifier) then you can have notifications
pop up in the notification center. This is nice if you work in Sublime or Atom and you feel like you'd loose too much screen space
by having a command line open in parallel.

Another cool parameter is `-p "**/*.something"` which lets you watch for certain files.
Rerun is setup to watch for common files (Ruby, SCSS, JS, and some others),
but it is happy to work with any pattern you want.

So there you go, have `rerun` run your tests for you!







