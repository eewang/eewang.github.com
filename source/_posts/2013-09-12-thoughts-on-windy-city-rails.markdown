---
layout: post
title: "Thoughts on Windy City Rails, Day 1"
date: 2013-09-12 20:06
comments: true
categories: 
---

I have the good fortune to be in Chicago today and tomorrow for the <a href="http://www.windycityrails.org/" target="_blank">Windy City Rails</a> conference. The NYT was kind enough to pay for me to fly here and attend the conferece, and I'm sure it helps that I can crash with my brother in the south Loop rather than get a hotel room. Whenever I can get the chance to come back home and see my family and friends, I try to jump at the opportunity.

Here are some thoughts on the most interesting talks I heard on WCR Day 1.

<!--more-->

#### "Functional Principles for Ruby Developers" by <a href="https://twitter.com/jessitron" target="_blank">Jessica Kerr</a>

Functional programming is pretty much a black box to me. I've heard a lot about it and know about the languages that implement it, but don't quite understand how it works or what the tradeoffs are compared to the more familiar object-oriented programming paradigm. As a Scala and Ruby developer, Jessica spoke about the core tenets of functional programming, and how they can be applied to Ruby and Rails. Her talk was the one I found most insightful and helpful for me personally on Day 1, and I liked how she used a simple, self-contained example (a Ruby script to calculate the total price of a set of data on books in .csv format) that demonstrated in code the broad themes she touched on.

<em>Data In, Data Out</em>

This seems to be the fundamental aspect of <a href="http://en.wikipedia.org/wiki/Functional_programming" target="_blank">functional programming</a>. Functions are intended to be black boxes that take in data, do some awesome calculations, then return some other data. Personally, I need to see more code examples / translations into functional programming in order to fully grasp what's going on, but I get how functional programming can help improve the predictability of an application's behavior by removing or minimizing state dependency and the impact within a function on the outside environment. Pat Shaughnessy gave a <a href="http://www.youtube.com/watch?v=5ZjwEPupybw" target="_blank">great talk</a> on functional programming and Ruby using Haskell as a foil at GORUCO 2013. Jessica's talk helps solidify some of my understanding of functional programming after hearing about it (and having it fly over my head) at GORUCO.

<em>Functions are data</em>

Lambdas in Ruby serve as first-class functions, not blocks or procs. Functions that are first-class can be stored as variables, passed around as parameters and set as the return value from other functions. An example of this is in how lambdas handle return or break statements. Return in a lambda exits the immediately executing scope to the parent environment, whereas blocks and procs exit out of the scope above them. Functions in a functional programming context are intended to be complete black boxes - data comes in, something happens, and data comes out. Lambdas are more accurate reflections of this functional programming construct than the other pseudo-function elements like blocks or procs. 

<em>Errors are data too</em>

This relates with the item below - basically, that the more information you have about why your program isn't working, the more likely you'll be able to get your program to work. Standard error messages include stack traces and specific indications of why your code is broken (NoMethodError, for example, pretty succinctly tells you that you're calling a method that doesn't apply). Coders should try and add in their own custom error messages to describe why their program doesn't work. In Jessica's example, instead of trusting the program to sum up all the prices of the books, adding custom error messages helps in debugging and indicating whether the program didn't work properly because the book didn't have an ISBN number or that it didn't have a price.

<em>Nil is not data</em>

Nil means a lot of things in Ruby - it's pretty much the catch-all return value for whenever something doesn't go according to plan. You search for a non-existent key in a hash (nil!). You have an if-statement without an explicit else block (nil!). You try to access an array index greater than the size of the array itself (nil!). All of these use cases demonstrate the ubiquity of nil so much so that if nil means everything, it also means nothing. 

Jessica demonstrated a way to convert nil values into meaningful messages that specify what exactly happened. For example, adding custom error messages and returing those as the script is running is more informative than just returning nil. This way, you know where your data is dirty and how you can further address it. She goes into more detail in her Github repo with the presentation (which I defintely recommend checking out).

<em>Functional composition</em>

Composition in functional programming refers to a 'fits together with' type whereas composition in object-oriented programming is a 'has a relationship with' type. For example, in order to print useful tracing messages, try and wrap functions in a helper method that prints out an error message and then calls the function itself. This is easier and DRYer than going into each of your methods and writing out messages, since if you want to change how you print the message, you only have to modify the body of one function rather than several.

<em>Be Lazy</em>

This goes without saying in general, but in this example speciically, the #lazy method in the Enumerable class helps evaluate one book. Without laziness, the process goes - import all book lists, check all prices, get all validly formatted books, then summing all the prices. With laziness, each book is processed end-to-end at a time. This way, the memory imprint of the process is minimized, and the sum builds up incrementally rather than being calculated all at once at the very end of the process. I liken the laziness aspect to scaling vertically rather than horizontally - its generally smarter (and much easier) to do the former than the latter. 

I had not known previously about the <a href="https://github.com/yhara/enumerable-lazy" target="_blank">#lazy</a> Enumerable method, and it looks like a nifty component added in Ruby 2.0, so I'll definitely be sure to try and play around with it wherever I can. 

Here are a few helpful links - Jessica's Github repo about the example she used is pretty descriptive of the benefits of functional programming with Ruby coming from a non-Rubyist perspective.

<a href="https://github.com/jessitron/fp4rd" target="_blank">Why should a Rubyist care about functional programming?</a>

<a href="http://www.confreaks.com/videos/2382-rmw2013-functional-principles-for-oo-development" target="_blank">Functional Principles for OO Development (Ruby Midwest)</a>

#### "Devs and Depression" by <a href="https://twitter.com/greggyb">Greg Baugues</a>

This talk by Greg Baugues was probably the most unique talk I heard all day, largely because it didn't deal directly with code. Greg shared his personal struggle with bipolar disorder and ADD, and spoke about how certain qualities like intense focus and grandiose thoughts displayed by developers can actually reflect a deeper, unhealthy state. As someone with loved ones who have been afflicted by depression, I appreciated the honest look Greg provided into how mental health issues can debilitate one's capacity for creativity and productivity. Go check out <a href="http://www.devsanddepression.com/" target="_blank">this site</a> about his depression.

#### "Keeping Your Massive Rails App From Turning Into a S#!t Show" by <a href="https://twitter.com/benjamin_smith" target="_blank">Benjamin Smith</a>

Service-oriented architecture seems to be all the rage these days, and Ben (of Pivotal Labs) described the process they used to build a complex social networking application using self-contained Rails engines. By breaking out the circular dependencies that are common across Rails (e.g., User has many Posts; Posts belongs to User), they were able to avoid having a monolithic User model and instead bolt-on added functionality (e.g., comments, likes, photos, etc.) in a discrete and loosely coupled manner without adding unwieldly dependencies. 

I haven't used <a href="http://edgeguides.rubyonrails.org/engines.html" target="_blank">Rails engines</a> before, but they seem like nifty structural components to keeping code self-contained and modular so that separate teams can work on sub-sections of an application in relative isolation. One of the drawbacks of this technique, though, was that the initial velocity of the project was low, as they were focusing a lot on ensuring structural efficiency. However, this more than paid off as the project scaled, because they could add features incrementally without having to worry about dependencies across the project.

#### "Hardware Integration with Rails" by <a href="https://twitter.com/too_mitch" target="_blank">Mitch Lloyd</a>

Mitch, who works at a development shop out of Pittsburgh called Gaslight, covered the basics of dealing with hardware using Ruby. He walked the group through converting Ruby messages into binary using basic 'to_i' commands with varying bases (default is base 10) to send the proper messages to the hardware receiver. Having never worked with hardware before, I appreciated his zero-baseline approach to teaching hardware programming, but I frankly got a little lost when he moved on to managing asynchronous tasks between software sender and hardware receiver and multi-threading. These are topics that I need more experience in - don't worry though, they're on my list of technical areas to explore.

Having gone to a grand total of two technical programming conferences (represent GORUCO 2013!), I'm starting to get a feel for what I like and don't like in technical talks. 

<strong>Likes:</strong> Small, self-contained code sample or example application that demonstrates the main themes of the talk. Coherent presentation structure (bulleted 'takeaways' are an easy way to accomplish this). Description of both anti-pattern and pro-pattern (e.g., "this is what y'all are doing... but this is how y'all should be doing it"). Keep presentations to 30 minutes, max. Maybe 45 minutes, but you're really pushing it then.

<strong>Dislikes:</strong> Presenters who are too abstract and don't deal with code implementation details about what they're advocating. Presenters who are too detailed and don't convert the code they're writing to broader abstract themes. Bad jokes that fall flat on the audience. 

I'll see what today brings, but overall I've enjoyed my time at Windy City Rails. The talks have covered a wide swath of topics - from testing, to functional programming, to mental health issues. One downside of the conference, though, was that it held at the South Shore Convention Center, south of the University of Chicago. For those not familiar with Windy City geography, that's like holding GORUCO out in Pelham Bay Park in the Bronx - a beautiful area with natural scenery and fresh air, but a bit away from the bustling city center. I wish Windy City Rails were held right in the Loop, not only for my own commuting convenience but more so for those who haven't really seen Chicago and want to explore the great city I call home (disclaimer: I'm actually from Naperville, a suburb of Chicago, but no one needs to mention that :)).