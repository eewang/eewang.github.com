---
layout: post
title: "RailsConf 2014 Reflections, Part 1 of 2"
date: 2014-04-29 08:59
comments: true
categories: 
---

This past week, I had the great privilege to visit Chicago and attend RailsConf 2014. Before RailsConf, I had been to two conferences - GORUCO 2013 and Windy City Rails 2013 - both of which had enough attendees to fill only a single track of talks. RailsConf, on the other hand, had four tracks (even five, at times) that were roughly grouped by focus area, like around domain design, careers or refactoring. Needless to say, RailsConf 2014 was the largest conference I’ve attended in my short career as a developer.

<!--more-->

In full honesty, attending RailsConf was part-work and part-vacation for me, in that in hailing from the Chicagoland area, I saw it as an easy decision to attend when offered the opportunity to go to one major conference a year on the NYT dime. Because of this, I didn’t do as much after-conference socializing as I probably would have done were RailsConf not held in a city where my family and friends live. While I wouldn’t have given up catching up with friends and seeing my family for anything, it would have been nice to have been more active in meeting people at the conference.

I found the best talks of the conference to be DHH’s opening keynote, Aaron Patterson’s (i.e., @tenderlove) closing keynote, and Sandi Metz’s talk on refactoring, which I’ll go into in Part 2. Sandi’s talk didn’t get the prime-time slot as the two keynote presentations, but I found it just as informative and useful in thinking about how to structure code. 

DHH and Aaron gave almost polar opposite talks, in terms of style, focus and substance. DHH stayed high-level and abstract - focusing on what he sees is the emergence of an unhealthy obsession with test coverage, metrics and coding practices like TDD. We are software writers, not scientists, he said. Maintaining a religious devotion to measuring our code quality and slavishly obeying coding patterns and paradigms like TDD or “Tell, Don’t Ask” leads us to miss the mark in delivering on what we are paid to do - write functioning software. 

Aaron, in contrast, gave a pretty technical talk (for a keynote presentation) on ActiveRecord internals and ended by merging his experimental AdequateRecord branch into the Rails master branch, a significant and symbolic gesture. Complete with graphs and performance benchmark numbers (i.e., science!), his talk touched on how improvements in SQL statement caching has led to the performance improvements of AdequateRecord, and also gave a helpful, whirlwind tour of how ActiveRecord translates a query (e.g., `Post.find_by_name(‘foo’)`) into raw SQL and returns a domain object.

While the two talks seemed quite dissimilar, at the end of the day, it seems to me that both Aaron and DHH are correct in how they approach software engineering / development / writing, however you want to call it. If that seems too neat, tidy and cliche for you, I apologize, but I think coding, in practice, is both art and science. Early on in the product development lifecycle, coding may be more like writing than science. Optimization and maintaining a robust test suite seems like overkill when its not even clear what it is you’re trying to build. 

However, there comes a point when you start solidifying the codebase, product features become more refined and business users develop a more coherent process. At this point, traffic increases, infrastructure expands and optimization becomes a critical focus to maintaining a consistent and pleasant user experience. Here, there’s at least a few clear metrics to measure yourself against; when a user clicks on a link or takes some action on my app, how quickly can the app return a response? This question encompasses a wide array of application pain points - asset rendering, SQL query counts, database connections, etc. - but ultimately, it all boils down to actual milliseconds and (hopefully not) seconds. 

To me, this is what Aaron was getting at in his talk on AdequateRecord. Rails has matured to the point where its clear what its trying to do and the feature set is robust. Aaron even said that he doesn’t want to see more features get added to Rails. Fine-tuning Rails and ActiveRecord inevitably means benchmarking and taking a scientific approach to improvements by changing individual inputs into the system and measuring the output each time (e.g., if I call `includes` on this association rather than `joins` how does that improve my performance?).

Software development as both writing and science do not have to be mutually exclusive. In fact, I sincerely hope they are not. I enjoy building software precisely because it engages both the creative / writing side as well as the mathematical / scientific side of my brain. When I’m busy iterating on a product early on in the lifecycle, I can both enjoy the process of coming up with creative solutions (even if they are hacky and not the most efficient) yet also know that there will come a time when I can focus on refactoring and optimizing what I’ve built. These two dimensions to code keep me from exhausting my mental resources capable of handling creative expression and scientific calculation. To my knowledge, software is one of the few industries that can maintain this duality, a healthy relationship between creativity and scientific reasoning. And for that, I’m tremendously thankful that I get to operate at that juncture.

If you haven't seen the DHH and Aaron Patterson keynotes, check them on on <a href="http://confreaks.com" target="_blank">ConFreaks</a> when they get posted. Definitely worth watching!
