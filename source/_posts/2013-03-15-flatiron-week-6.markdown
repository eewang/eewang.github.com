---
layout: post
title: "Flatiron - Week 6"
date: 2013-03-17 10:14
comments: true
categories: 
published: true
---

Today marked the half-way point for the Flatiron program. This past week was heavy on Ruby on Rails instruction; Avi wanted to hammer into us the importance of understanding complex forms and working with multiple, associated models. On Monday, we brainstormed ideas for our final group projects and went through a planning session where we discussed ideas at a high-level - the features, technical obstacles and work sprints that we could tackle within the short timeframe for development.

<!--more-->

Throughout the week, I tried to implement the lecture content into my side project. Learning forms was challenging, and I'm glad that Avi and Bob spent a lot of time on the topic. The previous week, I had tried to implement a "favorites" form for users of my ticket tracker application, basically allowing users to tag sporting events or concerts as favorites. Eventually, I want this functionality to drive my API calls and analytics; rather than make a large number of unnecessary API calls, I want users to tell me what they want to look for and then enable the application to retrieve the data they want. Attempting to build out the form before covering the topic in class was difficult - I could never quite get the params hash working correctly, and I hadn't made the proper inter-model associations. However, after much lecture and review this past week, I was finally able to get that functionality working in my application.

In addition to working on forms this week, I also restructured my schema and models. In previous iterations of my application, I had been retrieving data on a periodic basis and saving all the data to a single table. This created considerable data duplication, as many attributes for a given concert or sporting event are not going to change on a daily basis (e.g., event date or venue). I came to the realization that a single API call for a given concert on a given day actually could be broken down into three tables - one for the actual concert, one for the concert listing and one for the concert listing quote for that specific day. Thus, I restarted my application with this in mind and broke up the process of saving data into a three-step waterfall so that I would get all necessary data, but nothing more.

For example, say I make a call to the Stubhub API for event information about <a href="http://electricdaisycarnival.com/NewYork/" target="_blank">Electric Daisy Carnival - New York</a> (EDC). That API call actually returns to me information about 4 separate "events" - EDC 3-Day Pass, EDC Friday Only, EDC Saturday Only and EDC Sunday Only. The data is structured as JSON, and each hash has data fields that cover both daily static information - event date, venue, city, etc. - and daily dynamic information - minimum price, total tickets available, etc. Before, I had been saving all this information in the same table, but this past week, I broke it out into three tables, so that an API call for EDC will create 1 row in my concerts table, 4 rows in my concert_listings table and 4 rows in my concert_listing_quotes table. An API call tomorrow will create 4 new rows only in my concert_listing_quotes table, assuming that there are no new concert listings (an example might be EDC - NY After Party Tickets). I refactored my code so that each API call goes through a waterfall, checking for whether the relevant level of concert abstraction (a concert, concert listing or concert listing quote) already exists in the tables. If it does, great - move on. If it doesn't, save a row to the database.

Refactoring the code in this way plus adding in the proper associations (a concert has many concert listings, which have many concert listing quotes) made it much easier for me to traverse my models and move quickly from an individual user instance to what he or she favorited, through a FavoriteConcert model. My API calls take less time to run, and my application loads faster. All in all, I got a good lesson in the value of refactoring this past week and the benefit of adding reasonable layers of abstraction. 

In addition to coding, I've been reading Sandi Metz's book on design - Practical Object Oriented Design in Ruby, or POODR. Going through POODR has been great in conceptualizing design best practices and taking a step back from coding to think through how to balance technical specificity with product flexibility. Sandi Metz has a great way of interjecting pithy, memorable statements about design that are great to keep in the back of your mind while designing an application. I'm hoping to finish up the book this week, at which point I'll write a longer post with my review and thoughts on the book.


