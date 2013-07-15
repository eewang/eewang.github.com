---
layout: post
title: "Developing Web Applications with 8to18 Media"
date: 2013-06-11 20:49
comments: true
categories: 
---
I'll admit it - I've been slacking the past few weeks about blogging. Most of what I've been doing since mid-May since my job search concluded has been enjoying NYC, hanging out with friends and doing a some Javascript learning. Its tough to keep up the pace of Flatiron after the end of the program. While I've still be trying to code every day and spend quality time learning Javascript and Backbone, I've taken time to relax and enjoy myself since I'll have many more years of working ahead of me and increasingly less time to just live and enjoy the city.

<!--more-->

However, also like Flatiron, all good things must come to an end, so since Monday, I've been working at a startup called 8to18 Media in Lombard, IL (~30 mins west of Chicago) as a contract developer. My brother works as the co-head of engineering at 8to18, which builds web applications for high school activities (e.g., sports, clubs, summer camps, etc.), and they agreed to bring me on for a month before I start at the NYT to work on their backlog of bug fixes and feature requests. For me, I'm excited for the opportunity to get back into the habit of daily coding and to learn more about how to be a professional developer. While the 6 or so free weeks post-Flatiron were great (well, at least the 3 or so since wrapping up the job search process), I'll admit that I was getting a bit antsy and was itching to get back into coding and learning every day. I hope that this experience will give me momentum leading up to my NYT job this July.

For example, in just the first few days on the job, its been great to learn more about the project management aspects of being a developer. I'm working on 8to18's registration application, which is a Rails app that parents and schools use to register children in activities. The app is under active development and usage, so its been great to see how customer feedback directly affects development. 8to18 uses a project management tool called Jira, which is similar at a high level to Trello (used by the Handrai.se team at Flatiron) but much more robust and full featured. Jira syncs with Github and commits are pulled into a dashboard newsfeed that shows what everyone on the team is working on. It also provides cool analytics on which tickets have been assigned, what their status is and what else is scheduled in the queue. 

Compared to the group work at Flatiron, the git process here is more rigorous. I've learned how to keep my commits focused on a single issue; rather than cleaning up every unrelated error I see when going into the code to fix a bug, I'm trying to get into the habit of creating Jira tickets. This makes the bug fix commit as limited as possible, which minimizes the risk of introducing more bugs. Each commit message is prefixed with a Jira ticket number (e.g., REG-100 for the 100th ticket under the "Registration" project) and a hashtag that can then be read by the Jira system and allocated accordingly. For example, a #resolve hashtag appended to a commit message will automatically resolve the issue in Jira when the commit is pushed to Github. At Flatiron, our commits (at least mine) may not have had the most informative messages, which made them more difficult to tie to a specific issue, bug or feature. Also, we tended to make batch commits that included a lot of unrelated code changes in one commit. At 8to18, I'm trying to get into the habit of having smaller, more focused commits that are easier to audit and test.

Speaking of testing, another habit that I'm learning is being more vigorous about writing tests. 8to18 emphasizes test-driven development, so I'm learning a ton about how to think about the implementation code I'm writing through the lens of Rspec tests. Yesterday, I wrote about 5 lines of code in a Rails controller that I had to support with approximately 20 lines of test code to confirm expected behavior in edge case situations. While it took me more time to wrap up the bug fix, I came out more confident in my code because I had tests to support the functionality.

My contract work at 8to18 will end around July 4 for a total of four weeks, after which I'll start my job at the NYT. I'm happy to have the opportunity to code in a professional work environment and learn more about developer best practices at 8to18 before starting my full time job later this summer.

--
<em>Correction: 8to18 uses the default Rails testing framework (TestCase) rather than Rspec</em>
