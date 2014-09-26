---
layout: post
title: "Of Spreadsheets and Software"
date: 2014-09-24 21:14
comments: true
categories: 
---
A co-worker of mine recently attended [Strange Loop](https://thestrangeloop.com/) in St. Louis and directed me to a [presentation by Felienne Hermans about spreadsheets](https://www.youtube.com/watch?v=0CKru5d4GPk) and their potential as a tool for developers. Although I've lost some of my Excel skill since my finance days, I find that the thought process underlying software applications and spreadsheets are remarkably similar.

<!--more-->

I entirely agree with Felienne about the power and ubiquity of spreadsheets. My initial foray into software development was through the trojan horse of Excel. What started out as basic functions to support models around investor liquidity and product pricing eventually morphed into a tangle of VBA code to automate data processing. I never thought of Excel functions themselves as code, although now it makes a lot more sense.

Perhaps the best argument from my perspective for learning about spreadsheets is that they are practically the lingua franca of business people. As developers, our main task is to build software for users in a way they understand, and what better tool to use than one that comes installed by default in most corporate laptop setups.

Felienne points out that while spreadsheets haven't typically been the playground of serious developers, modern best practices in building software can be a deep resource for spreadsheet users to improve and standardize their Excel models. Testing and refactoring are the two examples cited in the presentation. When I was building Excel models in my previous job in finance, I never thought about testing my code. As for refactoring, I knew the benefits that could be gained, but it was never something I thought important or could systematically implement (or even successfully, given that I didn't have any tests to protect business functionality).

Looking back, I realize that had I known how to code, I could have been more effective at my job. That said, I probably would have still used the same tools (i.e., Excel) to do my work. Its hard to overstate the utility of Excel as a proto-software IDE in a business environment. A language, framework and text editor all wrapped in one, Excel runs consistently on every person's machine (provided that everyone is on the same version) and doesn't run the same risk of customization among its users (less technically saavy than the average developer) as software packages have. Excel files are easy to email around and don't typically rely on third-party libraries likes a Rails app would, so there is a low barrier to use and distribution.

Where I would have done my job differently had I known how to code is in the area of version control and distribution. I'm consistently impressed with the Git and Github model of version control and collaboration, and I would like to think that using Git to manage Excel models would have been a better strategy - provided that others on my team were willing to learn the technology - than renaming Excel files with a "v*" suffix and re-emailing out the latest version each time I made a change.

Coding with all our preferred tooling is great, but at the end of the day, if our job is to build usable software for the less-technically inclined, spreadsheets are a great way to achieve some level of automation and computing power while avoiding the steep learning curve demanded by traditional software development. When I first started to code, simply using my terminal was a challenge (don't get me started on vim). And while understanding and manipulating spreadsheets is still an acquired skill, spreadsheets are a great tool just to get something up and running that can be shared and used by other, non-technical people.
