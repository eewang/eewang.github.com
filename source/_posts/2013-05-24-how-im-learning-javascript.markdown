---
layout: post
title: "How I'm Learning Javascript"
date: 2013-05-24 07:45
comments: true
categories: 
---

For my job at the NYT that I'll be starting in July, I'll likely be working a lot in Ruby and Javascript. At the Flatiron School, the focus was on becoming a back-end Ruby developer, and while we learned our fair share of JavaScript and jQuery, it was always in the context of a Ruby or Rails application. Most of the front-end focus gravitated toward DOM manipulation and traversal using jQuery and less on the functionality of Javascript as a programming language (e.g., objects, closures, inheritance / composition, etc.).

<!--more-->

Given that I expect that Javascript will be a substantial component of my role at the NYT, I'm planning to spend much of the next 5 weeks or so until I start refining and building my Javascript skillset. I've been cruising the Web accumulating JS learning resources, and I think I put together a solid syllabus. 

I'm thinking of learning Javascript from three perspectives. First, Javascript is a full-fledged programming language and it should be learned as such. This means exploring data structures, iteration, objects, etc. in Javascript much the same way we did for Ruby. Second, Javascript is the language of the Web browser, which means learning DOM manipulation and front-end user interaction. Finally, Javascript has seen an explosion of front-end MVC frameworks in the past few years, the most well-known of which include <a href="http://backbonejs.org/" target="_blank">Backbone.js</a>, <a href="http://emberjs.com/" target="_blank">Ember.js</a> and <a href="http://angularjs.org/" target="_blank">Angular.js</a>. These frameworks help you structure and organize your Javascript, and generally improve the user's experience as they can decrease the server communication overhead involved in any modern, database-driven application.

Here are a few of the ways I'm planning to practice writing and understanding Javascript over the next few weeks:

<strong>Build Ruby Methods</strong>

One of the benefits of coding in Ruby is that it provides a lot of the Enumerable methods like #collect and #inject that abstract away the need to write a basic iterator like a for loop. While these abstract methods provide a considerable amount of functionality in a terse, human-readable syntax, its easy to take them for granted. Thus, I'm planning to practice Javascript by writing the JS equivalent of the Ruby methods I use most frequently but do not have parallels in core JS (not considering jQuery or other libraries). Hopefully, this will force me to dive deep into both the Ruby and Javascript documentation and build up my Javascript muscle memory.

<strong>Solve Project Euler</strong>

Completing <a href="http://projecteuler.net" target="_blank">Project Euler</a> problems using Ruby has been a great way for me to strengthen not only my Ruby scripting skills but also my software design understanding, as I always try to properly encapsulate my Project Euler solutions and make the solution object-oriented. I plan to use the same approach to learning Javascript. First, I'll translate the Project Euler problems that I've already solved from Ruby into Javascript, if only as a way to get in the habit of building JS objects using the constructor and prototype patterns. Then, I'll work on new Project Euler problems and do the reverse translation - Javascript to Ruby. This way, I'll become better at moving seamlessly between JS and Ruby, which will undoubtedly help me in my role at the intersection of the front-end and back-end.

<strong>Read Books</strong>

I've solicited advice from a number of developers on what are the best Javascript books to read, and here's the list that I'm planning to go through to learn JS:

* "Professional Javascript for Web Developers" by Nicholas Zakas
* "Developing Backbone.js Applications" by Addy Osmani
* "Javascript Patterns" by Stoyan Stefanov
* "Object Oriented Javascript" by Stoyan Stefanov

These four books seem to be relatively accessible to developers first exploring Javascript, yet also are challenging enough to keep me engaged and constantly learning. I think these books focus more on software design and code patterns in Javascript, which I like, rather than just DOM manipulation. I learn best by understanding the core of a language so that I can use libraries like jQuery knowing full well how its implemented under the hood. Much like how the Flatiron School teaches Rails only after teaching Ruby, SQL, ORMs, etc., I'd like to be proficient in core Javascript as a prerequisite to becoming an expert in libraries/frameworks like jQuery or Backbone.

<strong>Complete Online Tutorials (Codecademy, Code School)</strong>

When I first started to learn how to code, I learned primarily through in Codecademy and Code School tutorials. Those were helpful in providing a general overview to Javascript, but with a lot of hand-holding. Also, neither Codecademy and Code School have perfect interpreters, so there are times when you write perfectly valid code, but you can't pass the challenge because of some quirk or bug in the in-browser interpreter. This led to much frustration when I was first learning how to code, and the content I learned via Codecademy, while useful, felt somewhat disjointed as I was chained to working in the browser interpreter they provided. 

Now that I know how to code though, going back through Codecademy and Codeschool has been a great way to solidify core concepts. I still feel like they hold your hand too much when completing code challenges, but I can get more out of the tutorials now that I have a stronger foundation in code. Codecademy has a pretty solid Javascript course, and Code School offers tutorials in Backbone and jQuery that I've found useful.

<strong>Work on a Project</strong>

This is a bit of no-brainer as a learning technique. Completing a project will help me most with learning front-end frameworks. I am familiar with Angular, having used that in project, but I'm planning to turn my focus to Backbone, given that the NYT uses Backbone pretty heavily for its front-end code (perhaps because Jeremy Ashkenas, the creator of Backbone, used to be a developer on the NYT's Interactive News team). I'm still tossing around a few ideas of what I could build, but this Backbone tutorial for an <a href="http://coenraets.org/blog/2011/12/backbone-js-wine-cellar-tutorial-part-1-getting-started/" target="_blank">interactive wine cellar</a> seems like a good project that I could translate to another domain.

