---
layout: post
title: "How to Schedule Tasks Using 'Whenever'"
date: 2013-03-06 08:49
comments: true
categories: 
published: false
---

How to use the "Whenever" gem to schedule cron tasks.


- Creating your rake task
- Running console commands to update cron tasks
- Test your script first
- Be careful what you wish for
  - One inadvetantly scheduled and structured rake task took several hours and collected over 2.3 million rows of data. None of them had the right event_id, unforunately.

  

