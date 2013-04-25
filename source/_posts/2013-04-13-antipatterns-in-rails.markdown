---
layout: post
title: "Antipatterns and Metaprogramming"
date: 2013-04-13 15:03
comments: true
categories: 
published: false
---

I recently finished reading Rails Antipatterns, which is a great book that uses commonly abused patterns in Rails to illustrate refactoring opportunities. I recommend the book as a way to improve your "code smell"

Rails Antipatterns

Using view helpers

Use callback macros

Respond_to vs. respond_with

Managing 3rd party service calls (e.g., APIs)
  - Use error logging and rescue unexpected errors
  - Use background job processing services like Sidekiq
  - Use exception notification services like Exception Notifier









Metaprogramming Ruby

Procs vs. Lambdas
- Procs are more flexible when evaluating arguments vs. lambdas. E.g., if you define 2 arguments for a proc and only call the proc with 1, the other will be considered nil, whereas for a lambda, the call will return an ArgumentError
- The return value for a proc comes from the scope in which the proc itself was defined, whereas the return value for a lambda comes from the lambda block.

Lambdas tend to be the first choice for Rubyists because they're more similar to methods in that they are strict about the number of methods they accept (arity) and exit when return is called. Both lambdas and Procs are part of the Proc class.

There is also a Kernal#proc object

Kernel.proc or Kernel.lambda can both be used to create a new proc/lambda. Also, Proc.new works to define a proc.

"Different callable objects exhibit subtly different behaviors. In methods and lambdas, return returns from the callable object, while in procs and blocks, return returns from the callable object’s original context. Different callable objects also react differently to calls with the wrong arity. Methods are stricter, lambdas are almost as strict (save for some corner cases), and procs and blocks are more tolerant.

These differences notwithstanding, you can still convert from one call- able object to another, such as by using Proc.new(), Method#to_proc(), or the & operator." - p. 115

Creating a DSL - use procs


