---
layout: post
title: "Building ViewFinder (Flatiron - Week 9)"
date: 2013-04-07 14:42
comments: true
categories: 
published: true
---

The majority of this past week was spent building <a href="http://viewfinder.io" target="_blank">ViewFinder</a>, which is a Rails app that leverages the Instagram API to enable users to guess where photos were taken and gamefies Instagram photo feeds. Building ViewFinder along with <a href="http://www.erinandcode.com" target="_blank">Erin Lee</a> and <a href="http://jlarusso.github.io" target="_blank">Jesse LaRusso</a> has been a pretty great experience; we got way farther than I had expected initially for the NYC on Rails presentation. Looking back at our Trello board, we defined our MVP for the presentation as an app where users could browse tagged photos and write in the address or intersection where they think the photo was taken. What we ended up with was much more than that, and more visually appealing than I could have imagined initially.

<!--more-->

<strong>1) Brainstorming and ideation</strong>

The idea for ViewFinder came from the View From Your Window (VFYW) game that the blogger Andrew Sullivan has on his blog, <a href="http://dish.andrewsullivan.com/" target="_blank">The Daily Dish</a>. If you haven't read the Daily Dish, you should start (and pay for the privilege!). Sully is a coherent and reasoned blogger, and his writing covers a large swath of topics, from public policy and politics, to business, to arts and literature. For his VFYW game, readers will send him photos of their view outside their window and since his readership spans the globe, the photos tend to be from far-flung regions like Japan, Indonesia or Estonia. I've never played the game, because the amount of context clues provided by a VFYW photo are not enough for me to have a reasoned guess of where the photo was taken, given the global scope. However, I've always thought that the concept of the game was fun, and that it could be made more accessible to the average person by adding context, localizing the game and making it social.

I had initially planned to pursue this as a side project during the Flatiron program. Separately, Jesse, Erin and I were considering doing a project for The Disruptor Cup, which is a NYC hackathon that focuses on emergency preparedness. We had discussed an idea centered on analyzing government spending data in the aftermath of Hurricane Sandy and using the d3 Javascript library for data visualization as the interface for users to interact with the data. However, that idea didn't get too far, as just getting up to speed with the d3 library such that we could build complex data visualizations presented a pretty steep learning curve. And given the timing of the Disruptor Cup (early/mid-April) relative to our actual class projects, we decided to punt on the hackathon. We figured that there will always be other hackathons to participate in (and civic data is a particular passion for Jesse and I), and we didn't want to get too bogged down such that our in-class work quality would fall.

We explored a few other data visualization ideas for the NYC on Rails presentation. Most of these centered on using the NYC Open Data API to build a data visualization or map. However, we couldn't quite figure out what we could build that would be a great learning experience, be a functional product and, most importantly, actually be fun to build. We knew that we wanted to work with data and APIs in some form, do something with maps and build an the end product with a well-designed front end. 

In the end, I proposed ViewFinder as our NYC on Rails presentation. Erin and Jesse seemed to warm up to the idea pretty quickly, and we brainstormed a lot of ideas and features that we could build. We ended up agreeing to focus on building out a location-based game, where users could guess where Instagram photos were taken by geographic area in New York. Initially, we had Union Square, Times Square and 30 Rock as our main areas, but those gradually evolved into Midtown Manhattan, Downtown Manhattan and Downtown Brooklyn.

<strong>2) Schema, design and gameplay</strong>

I was happy that I was able to apply various programming techniques and tools that Avi/Bob/Blake have covered over the past few weeks. The two that come most clearly to mind are metaprogramming and asynchronous background job processing. Metaprogramming is a really useful technique to write methods that write their own methods. They are most applicable to code abstract patterns that vary by the data they accept rather than the actual methods that are applied to the data. 

The area where I used metaprogramming most actively was in designing the Instagram API calls and connecting that to the gameplay. For the Instagram API calls, there are a few different ways to access the API - you can search by tag, by geographic location, by user or by popular media. For each of these calls, I needed to filter the data to remove any images that were not geotagged, as non-geotagged photos would be useless in a location guessing game. Thus, in my Instagram wrapper class - which wraps the Instagram gem, which itself directly accesses the Instagram API - I have the following code:

{% codeblock lang:ruby %}
# instagram_wrapper.rb

def self.acts_as_locatable(*queries)
  queries.each do |query|
    define_method "filter_#{query}" do |options|
      location_images = self.send(query, options).delete_if { |i| i.location.nil?}
      location_images
    end
  end
end

acts_as_locatable :tag_recent_media, :media_search, :user_recent_media, :media_popular, :user_media_feed

{% endcodeblock %}

"acts_as_locatable" at the end of the above code block is a class method that takes in an arbitrary number of arguments and is executed at runtime. For each argument, it creates a method with the name of the argument prefixed with "filter_". Each of these filter methods strip away the non-geotagged results of any of the Instagram API queries defined by the parameters passed to the acts_as_locatable class method. These methods flow into the photo model, which then takes the results of the filtered Instagram query and saves them to the database.

{% codeblock lang:ruby %}

def self.acts_as_instagrammable(*queries)
  queries.each do |query|
    define_singleton_method "instagram_#{query}" do |options|
      i = InstagramWrapper.new
      filtered_images = i.send("filter_#{query}", options)
      filtered_images.collect do |pic|
        Photo.save_instagram_photos(pic)
      end
    end
  end
end

acts_as_instagrammable :media_search, :tag_recent_media, :media_popular, :user_media_feed, :user_recent_media

{% endcodeblock %}

Like in the first code block, "acts_as_instagrammable" is a class method that creates a new instance of the Instagram wrapper, calls the relevant filtered Instagram API call and saves the photos to the database. This way, I know that whenever I want to get photos from Instagram, I can use their method names (e.g., tag_recent_media) and know that what I ultimately get back are instances of my photo database, which will only include geotagged items.

Finally, the below meta-method is the crux of the gameplay, and queries the database for photos while simultaneously initiating a background processing job via Sidekiq to get more data from Instagram.

{% codeblock lang:ruby %}
def self.location_games(*games)
  games.each do |game|
    define_method "#{game}" do
      @game = game
      @coordinates = LOCATION_GAMES[game.to_sym][:coordinates]
      radius = LOCATION_GAMES[game.to_sym][:radius]
      size = LOCATION_GAMES[game.to_sym][:size]
      user = current_user
      @photos = Photo.game_photos_random(@coordinates, radius, user, size)
      @photos.each do |p|
        p.locale_lat = LOCATION_GAMES[game.to_sym][:coordinates][0]
        p.locale_lon = LOCATION_GAMES[game.to_sym][:coordinates][1]
        p.save
      end
      self.create_game({:game => @game, :coordinates => @coordinates, :photos => @photos})
      @start_photo = session[game][:photos].empty? ? 0 : Photo.first_unguessed_photo(session[game][:photos], current_user)
      @guessed_count = @photos.count { |p| p.guessed_by?(current_user) }
      InstagramWorker.perform_async(@coordinates)
      render "index"
    end
  end
end

location_games :downtown, :midtown, :downtown_brooklyn #, :world_trade, :dumbo

{% endcodeblock %}

The above block does a number of things, and is admittedly probably a decent candidate for additional refactoring. This meta-method creates a method for each game that is created. Initially, we just defined three games in a hash (not shown here) called LOCATION_GAMES, but eventually we think it would be cool for users to create their own games. For example, someone who is really into architecture or beautiful aboreal landscapes can tag photos on Instagram with #viewfinder and another tag and basically create themed games. Additionally, we imagine that this game can be replicated across most any geography, so we wanted to have a flexible design where you could quickly define the parameters of a game and have that flow into the application. Currently, games are defined within the context of a photo, but I think we are looking to break out games into their own game model and make ViewFinder more solidly object-oriented.

Anyways, the above code is activated when a user clicks on a location-based game on the ViewFinder landing page. For each game, the method will determine the game parameters (i.e., coordinates, radius and photo set size), go to the database and get a random set of photos that have not been guesesd by the logged in user, are tagged with #viewfinder or #vfyw and are within the defined radius of the coordinates. Simultaneously, the method will set off a background job to get more data from Instagram within the location parameters of the game. This way, as more people play and demand for a given game scales, the application should theoretically make more API calls and thus get more real-time data from Instagram, all without impacting the user experience as the arduous task of accessing the Instagram API and saving photos to the database is disassociated from the photos that are loaded on the front-end.

There are definitely many areas where we can do additional refactoring and clean up the code. One place specifically where I think we can do better is in using view helper methods more actively, as well as moving more logic to our controllers. With the NYC on Rails presentation coming up on Thursday, I'll admit that we took a few shortcuts in the middle of the week just to get stuff working. In the next few weeks, I hope to go back through the code from start to finish and refactor extensively.

In terms of the front-end design of the application, we wanted to make the user interface highly visual and intuitive for new users to quickly get up to speed on the game. We went through several iterations of design that used various photo carousel options. We eventually rotated back to Twitter Bootstrap's carousel, and I'm pretty happy with how the design ended up. 

<strong>3) Technical challenges</strong>

Probably the biggest technical challenge that we faced was in figuring out how to use the Google Maps API to drag and drop a map marker that then posts to our guesses controller to create a photo location guess. We had to delve deeper into Javascript than we had previously in class, and we also learned how to use AJAX in the process. The map that is displayed when a user decides to guess on a photo is centered on the game that the user is playing. When a user decides to play the Midtown game, for example, the photos that get loaded into the game are tagged with the coordinates of the game origin. This way, when the guess map is loaded, the map is centered and zoomed in accordingly. 

After figuring out how to load the map correctly, we needed to be able to post the coordinates of the map marker to create a guess. To do this, we used Javascript to recalculate the map marker coordinates whenever the map changed (the map marker automatically moves to the center of the map) and AJAX to create a guess with the respective coordinates when the user pressed "GUESS". Then, when the next page is loaded (i.e., the guesses show page), the map contains a marker for each guess that has been made on that photo. Figuring out how to make Rails and Javascript play nice in terms of transferring data between the two was challenging, but now that we were able to do that successfully, it's a great feeling to know that I now have that skill in my toolbelt.

Another challenge we faced was working with Instagram authentication and enabling users to sign-in via Instagram and display their user feed and friend feeds. This challenge tied in with the difficulty of designing a clean, intuitive user flow - from choosing a game all the way to viewing the results after making a guess. The first thing I tried to do actually when working on ViewFinder was to try and set up Instagram authentication. I used the Omniauth gem to do this, but it was a frustrating experience. I learned eventually that I was over-programming, in a sense, and needed to just let Omniauth take care of the authentication process. Building an intuitive user experience with Instagram authentication is still something we're working on, and I think that making the gameplay more object-oriented will help in that regard.

<strong>4) Future extensions</strong>

I think it would be great to enable users to create their own games. Eventually, I'd like it for people passionate about sculptures, for example, to be able to take pictures across the globe and tag their pics with #sculpture and #viewfinder and have the application pick that up for their followers to guess where they've been. In general, I like the notion of themed games as a way to enable people that are passionate about whatever to better express that passion. 

Also, it would be really cool if users could play timed games on ViewFinder, or challenge their friends to see who can best guess a set of 10 photos in a fixed amount of time. Another feature that I'd like to work on is to build out the analytics of the game and tap into the competitive nature of people. I'm a bit of a data analytics geek, and it would be super cool if we could use user data from the game to create cool data visualizations.

On the technical side, there is a certain amount of technical debt that got built up with the rush to deploy before the NYC on Rails presentation. I don't think the debt is insurmountable, but the app can definitely benefit from some refactoring. Using view helpers more effectively, moving some code into modules for shared functionality and making gameplay more object-oriented are all changes that I think would make the codebase cleaner and the user experience more fluid. 

I definitely would like to continue developing ViewFinder and building out new features. I've enjoyed the process of designing the product, working in a team and writing code. There's an undefinable joy in having an idea and being able to execute on that idea and see the results. Like many creative ventures, coding is hard enough so that its always challenging, but its also accessible and progress is possible with concerted effort. And to steal a phrase from Avi, coding is all about taking complexity and delivering simplicity. A simple, sensible user interface hides the complexity within a codebase, and now being able to understand how that complexity creates simplicity has been fantastic.