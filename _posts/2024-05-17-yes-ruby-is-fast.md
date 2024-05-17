---
layout: post
title: "Yes, Ruby is fast, but..."
author: beta-ziliani
date: 2024-05-17
tags: ruby crystal programming performance
---

_This post appeared first in [dev.to](https://dev.to/betaziliani/yes-ruby-is-fast-but-1l49). I decided to bring it to my page and to cleanly incorporate the edits._

John Hawthorn wrote [a nice post](https://www.johnhawthorn.com/2024/ruby-might-be-faster-than-you-think/) discussing a recent tool to [incorporate Crystal into your Ruby app](https://github.com/wouterken/crystalruby). While JH brings an important point, it overlooks certain aspects that are worth consideration. I'll discuss Crystal's _real_ performance and benefits, highlighting why such Ruby/Crystal integration is an indispensable tool to have on the bench.

This is also a structured presentation of some comments made on [the Hacker News post](https://news.ycombinator.com/item?id=40152029).

## tl;dr

* JH makes the case that Ruby has a just-in-time compiler, and that optimizing the Ruby version of the code has a great performance improvement.
* Crystal code doesn't need wrestling to be optimal.
* The comparison is performed within Ruby, that is, incorporating the cost of calling Crystal within Ruby.
* Pure Crystal shows something radically different!

## Yes, Ruby is fast

The first point I want to make is that JH is right: we need to be fair to Ruby's JIT compiler (`--yjit`), and only consider benchmarks that include it. And, indeed, with it, Ruby gets very nice performance.

And let me be blunt here: **I love Ruby!** Ruby is one of my top 5 languages of choice. And a great community with many big companies agree, so I expect Ruby's performance will only increase with time as more improvements gets incorporated into it.

🔴 **First point:** Ruby's YJIT is fast!

## The real performance of JITs and Crystal

Let's compare the execution of Ruby's YJIT, Python PyPy (another JIT compiler), and pure Crystal (that is, without the integration).

**Ruby:** On my computer, the numbers for Ruby's YJIT goes on par with those in the post. Each line corresponds to each of the optimizations proposed:

```shell
> ruby --yjit fib.rb
       user     system      total        real
   3.464166   0.022979   3.487145 (  3.491493)
   1.705869   0.002169   1.708038 (  1.710117)
   0.187083   0.000318   0.187401 (  0.187578)
```

**Python:** My Python-foo is limited, so I only ported the last problem (a simple while loop) and ran it with [PyPy](https://pypy.org). It takes a bit less of time:

```shell
>  pypy fib.py
0.12447810173
```

**Crystal:** When we compile the code with `--release`, numbers are insignificant! Not only that, I've added some extra code to make sure the optimizations weren't throwing away important code. So not only I calculate the Fibonacci number of 45 (using an UInt128, to even stretch this further), but I also print the sum of the million runs!

```shell
> crystal build --release fib.cr; ./fib
        user     system      total        real
  1134903170000000
  0.000002   0.000004   0.000006 (  0.000004)
  1134903170000000
  0.000001   0.000002   0.000003 (  0.000003)
  1134903170000000
  0.000002   0.000002   0.000004 (  0.000003)
```

⚫ **Second point:** Pure Crystal is _really, really_ fast in this benchmark!

_Reference:_ The code I'm using for the benchmarks is listed in this [gist](https://gist.github.com/beta-ziliani/0f815f205e7f5789fbb35653ec17d1a7).

## Crystal compilation optimizes your code

As mentioned, the Crystal version uses a primitive number type (`UInt128`). That might explain the performance difference... But, seriously, just a couple of micro-seconds?

Crystal uses the [LLVM](https://llvm.org) backend, which generates very optimized binaries. In this case, LLVM is cheating: it notices that we're calling the function with a fixed number, so it replaces the code with the hardcoded value!

What can we do to make sure it doesn't optimizes it that much? I tried adding a random number: `fib(45 + rand(1))`, and it still was fast (1ms). Upon inspection of the generated code, I realized that it was still performing a heavy optimization, by calculate first `fib(45)`!

As suggested by GitHub's user `@petr-fischer`, I changed the code to take the argument (45) from the environment. The same with the number of iterations. I also decided to use the more stable `Benchmark.ips` method (iteration per second), that performs several runs and calculates the average and mean time. Now we see _the very real_ time it takes: a bit less than 23ms. That's still less than a quarter from PyPy or Ruby YJIT!

These time can now be explained with the use of a primitive type (`UInt128`).

```shell
> ./fib 45 1000000              
  44.70  ( 22.37ms) (± 3.10%)  0.0B/op        fastest
  43.82  ( 22.82ms) (± 2.00%)  0.0B/op   1.02× slower
  43.66  ( 22.90ms) (± 2.38%)  0.0B/op   1.02× slower
```

Another point to make is that any of the variations bring changes to the timings (don't mind the "slower" tag; it's within error). In this regard, I'm puzzled as to why Ruby's YJIT doesn't optimize this as well. Perhaps it will get there with time (I tested Ruby 3.3.1).

⚫ **Third point:** Crystal code is fast, even without tweaks.

**Reference:** Check [this post by Ary](https://crystal-lang.org/2016/07/15/fibonacci-benchmark/) that George Dietrich recommended in the [forum](https://forum.crystal-lang.org/t/the-crystalruby-gem-through-the-crystal-lens/6832/1)).

## Maybe it's the plumbing that's slow?

Doesn't seem so. But to understand why, we need to discuss an important point: by default, the integration compiles the Crystal code without the `--release` flag. This makes sense: during development, you don't want the compilation to take a lot of time. Compiling in release mode makes efficient binaries, but at the cost of significantly increasing the compilation time.

When I tested the Prime Counting from the README file of the [crystalruby page](https://github.com/wouterken/crystalruby), using release mode, the time it takes to run the Crystal code is the same as the one from pure Crystal. For that, one needs to add the following code:

```rb
CrystalRuby.configure do |config|
  config.debug = false
end
```

So perhaps the timings from the Fibonacci example would look the same as with pure Crystal. I say _perhaps_ because I stumbled across an [issue](https://github.com/wouterken/crystalruby/issues/11) that turned the integration unusable on that particular example.

🔴⚫ **Fourth point:** The integration doesn't produce efficient Crystal code by default.

## Crystal/Ruby integration revisited

Crystal and Ruby are two wonderful languages, each with their pros and cons. Crystal's performance and low memory footprint is hardly contested, and can further be studied in [the benchmarks of language and compilers](https://programming-language-benchmarks.vercel.app/) (but be critical about benchmarks!).

Performance is not the only advantage of Crystal: its typechecker is another benefit that teams might want to use for safety-critical parts of an application. Or maybe there is an interesting shard to call from a gem… Whatever the reason, integrating Crystal code into Ruby is a very appealing tool to have in the dev toolbox.

It is common to call C functions from Ruby or Crystal. It's interesting to know that there are alternatives to bridge these two languages that share the same goal of writing beautiful programs, using a similar syntax. The mentioned [crystalruby](https://github.com/wouterken/crystalruby) gem allows interfacing Ruby programs with Crystal, and the shard [anyolite](https://github.com/Anyolite/anyolite) allows calling Ruby programs from Crystal.

🔴⚫ **Fifth point:** Ruby + Crystal FTW! ❤️

![A generated image of a red polyhedron with some black tints](/assets/img/posts/RubyCrystal.jpeg)
