---
layout: post
title: "This is not a blog post (Flatiron - Day 009)"
date: 2013-02-14 11:58
comments: true
categories: 
published: true
---

This is an instance of a blog post. In object oriented programming languages, like Ruby, the existence of a class, in this case a "Blog Post," is independent of an individual instance of such class (i.e., this blog post). Each blog post has similar attributes with different values, such as title, author and date. These are known as instance methods in Ruby. However, the "Blog Post" class may have class-level methods that are inaccesible by a single instance of a blog post.

<!--more-->

An example of this might be a count of blog posts. How many blog posts I've written is not a characteristic of a single blog post; rather, that data can only be found by accessing the "Blog Post" class and not a blog post instance. The "Blog Post" class has functionality that does not exist for an individual blog post, and vice versa. Confused? Good, now you know how we felt for much of the day. 

We spent most of the day covering class constructors and recreating a few of the programs we've already written into object-oriented programs. The lecture and class discussion around object-oriented design was dense and hard to digest. Although I came across <a href="http://www.railstips.org/blog/archives/2009/05/11/class-and-instance-methods-in-ruby/" target="_blank">this useful article</a> covering how class and instance methods differ, I'm still processing my understanding of classes, and how they fit into the broader Ruby universe. 

After lecture today, we focused on trying to take our scraper that we built yesterday, rewrite it using a class constructor and link it to a SQL database. This way, instead of our scraper returning a hash of student attributes as its data structure, we could have our scraper input data directly into a database, which is easier for querying and analyzing than a hash or array. Although its been a difficult task to combine a number of different tools and skills we've learned over the past week, I'm enjoying the challenge of trying to map out and code how data will flow from the page to the database.

For homework, we continued to work on Ruby Koans and our scrapers. This weekend, I'm hoping to be able to spend some time starting a few scraper projects that I have in mind. Its funny, since starting this program I've been looking forward to weekends so that I can just spend time coding and trying new things in Ruby. Sounds super nerdy, but its true. That said, I know that I need to seek balance in my life so that I don't get burnt out on coding. Hopefully, I'll have many fruitful years ahead to keep learning and exploring new horizons in technology. With the frenetic pace of the class and my tendency to dive deep into the weeds of what I'm working on, my body has been tired and I haven't been exercising much recently. However, I've learned more in the past two weeks than I have in quite awhile. And its pretty exhilirating.




