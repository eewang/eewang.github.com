---
layout: post
title: "How to smell check your code quality in Rails"
date: 2014-01-18 09:24
comments: true
categories: 
published: false
---
I've found that as you progress in your code career, you start to discover more patterns in how to structure code and how to address common questions. In the same vein, you also learn what not to do and your sense for code smells improves. Your methods might get smaller, your logic better encapsulated and your classes more singularly responsible.

But even as you become a better developer, it never hurts to have someone watching over your code, keeping you accountable to the best practices that you know in your heart but rarely exercise with your hands. In a perfect world, that would be a senior developer or other more seasoned mentor who can pair program with you and is highly invested in your success. If you can find yourself in that situation, fantastic. In any case, it doesn't hurt to have some automated tools that can quickly run through your code, find duplication, unused methods or non-descriptive variable names.

At the more involved end of the spectrum, there are hosted solutions for checking code quality like (Code Climate)[http://codeclimate.com]. But those can get pretty expensive and might be overkill for keeping your code up to snuff on a daily basis for small projects. On my team, we're exploring using tools like CodeClimate, but me and my fellow developers have integrating more code quality checks on our local machines so that any PR we submit to our develop branch is as clean as can be.

There are 4 gems in particular that I've been using recently to sniff my code and tell me what's pleasant, what's pungent and what downright reeks. While each gem takes a slightly different approach to checking your code and displaying the results, I've found them all to be useful in finding syntactic sugar or challenging me to apply more sustainable code paradigms to solving problems in my applications.

*reek*

*metric_fu*

*rails_best_practices*

*rubocop*