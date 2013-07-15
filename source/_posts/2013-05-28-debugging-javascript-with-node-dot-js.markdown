---
layout: post
title: "Debugging Javascript with Node.js"
date: 2013-05-28 17:40
comments: true
categories: 
published: false
---

I recently discovered an easy way to debug Javascript code in the terminal using Node.js. Previously, I had been using the browser to load up my Javascript (as part of a skeleton HTML file) and use a lot of "console.log()" statements to get feedback from my code. This is akin to just using "puts" statements in Ruby to debug and determine scope, local variable assignment, etc. Looking for a better way to debug my code, I came across the handy <a href="http://www.nodejs.org/api/debugger.html" target="_blank">Node debugger tool</a>. This tool is part of the core Node API, so if you have Node installed, you have access to the debugger. Using the debugger tool was like discovering the 'pry' equivalent for Javascript. As someone who lives in the pry console when writing Ruby, discovering the Node debugger has greatly enhanced my understanding of how Javascript operates.

<!--more-->

There are a few Javascript debugger tools out there, but so far I've enjoyed the simplicity of the Node debugger.

For example, I've been using the Node debugger to help me build a simple sports application. This application is intended to simulate the process of building a roster of players (for a football team, in my case) by placing bids on them. Think of it as a much-scaled down version of the GM role in Madden NFL 13. With a file called football.js, getting into the debugger is as easy as typing the following in your terminal:

{% codeblock lang:bash %}
node debug football.js
{% endcodeblock %}

Once in the debugging session, the interpreter will pause at the top of your code and wait for your command on what to do next. Here are the list of useful commands that you can use in the debug mode.

* cont, c - Continue execution
* next, n - Step next
* step, s - Step in
* out, o - Step out
* pause - Pause running code (like pause button in Developer Tools)

One thing to note is that when you're in the debug mode (when the terminal prompt reads "debug> "), you can't test the values of local variables or execute Javascript. Think of the debug mode as the view mode in Vim - you can look but you can't touch. If you want to step into the local scope and test variables and execute JS code, you can use the "repl" command (stands for "Read-Eval-Print-Loop"), which will enter an interactive Javascript session at the current breakpoint. Once in "repl" mode, which operates like the "edit" mode in Vim, you can create new variables and functions, explore the local scope and generally do whatever you want as if it were the browser console. Then, once you're done, you can use "ctrl + c" to exit repl mode and return to debug mode to navigate the JS code.

Other helpful commands in the Node debugger include: list and restart. "List" prints out the context of the debugger breakpoint, and you can even pass in a line count value and the debugger will print out that many lines before and after the breakpoint (e.g., "list(10)" will print out a total of 21 lines - 10 lines before and 10 lines after the current breakpoint line). This is very helpful for taking a step back and look at how your structuring your code in the context of the code's execution. Also, I imagine that front-end MVC frameworks like Backbone help with JS code structure so that you don't need to use the "list" command as often to get your bearings.

"Restart" are kind of like the "reload!" command in when using the Rails console. The "restart" command will restart your JS application, so if you make any changes to your code after starting the debug session, "restart" will apply those changes to your current session. 




