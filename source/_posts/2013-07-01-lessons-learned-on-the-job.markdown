---
layout: post
title: "Five Lessons Learned On The Job"
date: 2013-07-01 04:52
comments: true
categories: 
---

Last week I started my job at the New York Times. And while I've learned a lot over my first week, I feel it necessary to write a post on what I learned working at 8to18 for four weeks. As my first actual developer gig, working at 8to18 has helped me grasp what it means to be a professional developer, and while I undoubtedly still have a lot to learn, I felt better prepared going into my job at the NYT having worked at a start-up for a short period of time. Here are a few lessons that I've learned now that I've had a chance to explore an existing code base for a non-trivial web application that has an active userbase.

<!--more-->

<strong>1) Make git commits informative</strong>

Git commits should communicate your intent as a developer through both the code changed within a given commit and the associated commit message. I'll try to include active verbs in my commit messages and separate sub-units of work by semi colons (e.g., something like "adds functional tests, refactors model code"). At 8to18, Jira was the primary tool for workflow management, so every code commit reflected a specific Jira issue. Adding the Jira issue number to my commit messages and specific language preceded by a hashmark allowed for tighter integration between Github and Jira. Initially, this was difficult for me as I had admittedly been sloppy with my commits at Flatiron, where I would fix whatever I saw that was wrong rather than noting it in Trello or Github issues and reverting back to fix it later in a different git branch. Working in a professional development environment has shown me the communicative value of tight, focused git commits with informative messages.

<strong>2) Its (almost) all about forms</strong>

A wise teacher (i.e., Avi) once said, "90% of web development is just building forms." I found this to be especially true in the work I did for 8to18, which consisted primarily of bug fixes and feature developement for their registration application. Used by parents and school administrators to register students for extracurriculars and sports, the registration app has easily been the most complex set of forms that I've worked with. And I say "set of forms" rather than just "forms", because the app has an added twist by enabling admins to create their own forms on the fly - adding and removing form steps depending on the particular sport or activity in question. Furthermore, the app had to address idiosyncratic use cases  specific to certain schools (e.g., schools in North Carolina require prospective athletes to note their felony conviction history before registering whereas other states do not have that requirement). 

Building this app in a modular way required the use of not just the domain models that contain the core business logic but a set of support modules to perform specific tasks like validate, present or format data. Form workflow follows a certain pattern - users input information, information is checked and validated, then that information is used elsewhere in the application. This pattern isn't inherently difficult to implement in Rails as the framework provides a lot of helpers and guidance to get forms up and running quickly. Yet knowing which components of the workflow can be abstracted into a module or class to enable user flexibility and minimize the added work needed when requirements change (as they most certainly will), therein lies the challenge of building usable, scalable forms and seems to come with just more experience.

<strong>3) Testing is like eating your vegetables</strong>

Learning to write test code before implementation code has not been easy. Admittedly, I'm not quite at full test-driven development; I'm closer to test-and-implement-at-the-same-time development. Writing tests can be frustrating - I've spent a good chunk of time banging my head against the wall trying to write meaningful tests. And while its not always fun to write tests, you have to do it, and you generally feel better after writing meaningful, passing tests.

Also, I've learned that not all tests are made equal. I'm more willing to write unit tests rather than functional or integration tests, as my frustration level rises when I'm faced with a number of preconditions just to get certain controller actions to be triggered (e.g., before filters, login requirements, etc.). Unit tests are generally easier for me to write - the universe of dependencies is smaller and the model layer is the business logic of the application. Coming in to work on an existing application, much of the model code had already been written, so I worked a lot in the controllers - utilizing existing model methods to produce new functionality. Over the course of the 4 weeks at 8to18, however, I've come to appreciate the value of good tests in building a resilient application. They've also come in handy as documentation - when I'm not sure what a model or controller is intended to do, I'd revert to the tests to provide clues.

<strong>4) Metaprogramming isn't always the answer</strong>

In my zeal to try new programming techniques that I've learned, I've tended to see metaprogramming techniques as the end goal when writing code - good practice regardless of the context. However, I've learned that while metaprogramming can reduce the absolute number of lines of code in your application, it can come at the expense of readibility for other developers on your team. In some instances, this is unavoidable. Whenever I've explored the Rails source code, for example, the sheer amount of metaprogramming makes it difficult for me to grasp what's going on. At the same time, that code gives Rails the out-of-the-box functionality that makes it so easy to get apps up and running. I can imagine that writing that customizable functionality into Rails without using metaprogramming would be practically impossible.

What matters the most is balancing the readibility of normal code with the power and flexibility of metaprogramming. Like anything in the world of programming, its all about the right tool for the job. For a framework like Rails, with a core group of contributors and a wide universe of use cases, metaprogramming is essential. However, for a smaller team with new developers and a more defined set of uses cases, metaprogramming could be more detrimental than helpful.

<strong>5) Commercial concerns trump all</strong>

While clean, beautiful code is an ideal worth aspiring to, at the end of the day, its still subordinate to the commercial needs of the product or business. For example, usage of the registration app that I'm working tends to be lumpy and clustered in certain months of the year (e.g., August, right before the start of the school year). Spending weeks to write perfect code for a feature could actually be detrimental if that means its release is pushed beyond the period of peak usage. 

Knowing when to focus on great code versus just get something working and out the door is something I think Flatiron positioned me well to understand. Coming from a non-traditional coding background (i.e., didn't take a computer science class in college), I feel like I have a good sense of commerciality when it comes to building products for actual customers. Working at 8to18 just reinforced my understanding of code as a tool to solve real world problems; when writing beautiful code rather than finding a solution to the problem at hand becomes the end goal, that's when you need to recalibrate. 

