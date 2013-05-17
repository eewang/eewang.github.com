---
layout: post
title: "Metaprogramming Ruby and Rails Antipatterns (Part 2 of 2)"
date: 2013-05-17 08:45
comments: true
categories: 
---

I wrapped up Rails Antipatterns about a week ago shortly after reading Metaprogramming Ruby. Reading both books side-by-side has helped me better understand the relationship between Ruby and Rails and how Ruby's metaprogramming capabilities provide the "magic" that Rails is often associated with. Rails Antipatterns is a great book that uses commonly abused patterns in Rails to illustrate refactoring opportunities. I was able to complete the book a few weeks back, but I've been keeping busy with the interview process (more on that in another post) and side projects such that I haven't been able to blog as much as I would have wanted. That's been one of the hardest parts of the interview process in that I just have less time to actually code, blog and read. Anyways, I figured that I'd write down some of my thoughts on Rails Antipatterns alongside Metaprogramming Ruby and the areas where Antipatterns helped me the most in structuring my Rails code.

<!--more-->

I recommend Rails Antipatterns as a great way to improve your "code smell." It provides practical advice to how to structure your code and general best practices when developing a Rails application. Compared to Metaprogramming Ruby, you won't get the same depth into Ruby's object model and method dispatch process in Rails Antipatterns, but then again, that's not the purpose of the book. Rails Antipatterns will tell you that you should use "delegate_to" as a way to reduce method chaining for your domain objects, whereas Metaprogramming Ruby will explain the fundamentals of how "delegate_to" actually works and less about the day-to-day use cases for the macro. One of my favorite parts of Metaprogramming Ruby is that it goes in depth in the second half of the book into how ActiveRecord creates its so-called "magical" finder methods dynamically using #method_missing. Conversely, Rails Antipatterns provides practical advice on when to use those macros and helper methods that Rails provides. Antipatterns explains what's in the Rails toolkit and how to use it, while Metaprogramming explains how the toolkit works.

I thought Antipatterns was overall a useful book, but at times I found it a bit disjointed. More like a compilation of various how-to guides rather than a coherent instruction flow. Whereas Metaprogramming has the core theme of the Ruby object model, Antipatterns is less grounded on an overriding programming construct and is driven more by observation of bad habits by the average Rails programmer (hence the title). Its still a useful book, but one that I think can be valuable even if you just skim to the sections that you struggle with the most and read the book in bits and parts. Here were a few of the sections that I found most useful to remind myself of a few overarching principles in application development - keep code DRY (don't repeat yourself), minimize and manage dependencies and properly encapsulate logic.

<strong>Use view helpers</strong>

When I first started building applications with Rails, I tended to put logic in the first place where I could fit it in. Unfortunately, this often meant in my application views. Over time, my views would look like convoluted piles of mixed in HTML and ERb code with loops and conditionals alongside div tags and class attribute declarations. Antipatterns explains that one way to avoid this tendency is to move presentation logic into view helpers. Rails provides all sorts of helpful methods to mimic HTML markup in view helpers. For example, #content_tag is an instance method created by Rails that can be used to define a block of markup. The first argument is the HTML tag as a symbol, followed by any number of attributes structured as a hash that you want to appear in the markup (e.g., :class => "new_project"), followed by a block, like so:

{% codeblock lang:ruby %}
def rss_link(project = nil) 
  content_tag :div, :class => "feed" do
    link_to "Subscribe to these #{project.name if project} alerts.",
            alerts_rss_url(project),
            :class => "feed_link"
  end 
end
{% endcodeblock %}

This code does the same as the below HTML, but instead extracts the logic into a Ruby method that can then be applied elsewhere in your views. This makes the code easier to maintain by centralizing any updates into one file rather than having to wade through multiple ERb files to pick out the same block of code to fix.

{% codeblock lang:html %}
<div class="feed"> 
  <% if @project %>
    <%= link_to "Subscribe to #{@project.name} alerts.", project_alerts_url(@project, :format => :rss), :class => "feed_link" %>
  <% else %>
    <%= link_to "Subscribe to these alerts.",
    alerts_url(format => :rss), :class => "feed_link" %>
  <% end %> 
</div>
{% endcodeblock %}

In fact, the #rss_link helper method can be abstracted even more by replacing the #link_to macro with another #content_tag macro. #content_tag (documentation <a href="http://apidock.com/rails/ActionView/Helpers/TagHelper/content_tag" target="_blank">here</a>)is an abstracted method that can be used to define other view methods like link_to or image_tag. All you have to do is define the HTML tag in the first argument, the content in the second argument, then pass in all the other arguments as a hash after that. Its useful in structuring HTML in a friendly Ruby syntax, and can be especially helpful in creating view helpers that provide semantic method names to blocks of logic.

This last point is why I like using view helpers so much. The larger an application becomes, the more difficult it is to look at code in views and understand what's going on. For example, there may a block of ERb code that loops over a collection of objects and conditionally inserts a div element if a certain condition is met. Overtime, relying on this block of code can get cumbersome, especially as more conditionals are added, which further decreases the readability of code. View helpers provide a name to a block of logic so that people reading your code have insight into what that code is meant to do. They properly encapsulate logic and provide semantic meaning to what is at first glance just a bundle of HTML tags and Ruby code.
 
<strong>Organize finder methods into scopes</strong>

When I was building my TicketTracker side project during the first half of the Flatiron School, I quickly realized that I needed to have an easy way to query my database and organize / filter the results. ActiveRecord provides a helpful #scope class macro that can be used to chain together database query logic that you find yourself using regularly. For example, if you find yourself always retrieving objects from the database that are less than 5 days old, its easy in Rails to define a scope called "recent" that you can then apply as a class method to your model (e.g., Model.recent) rather than using (Model.where(:timestamp < Time.now - 5.days) ). There's a good deal of documentation on scopes in Rails, but at its core, scopes are just class methods. Here is a <a href="http://blog.plataformatec.com.br/2013/02/active-record-scopes-vs-class-methods/" target="_blank">great blog post from Platformatec</a> about using scopes and class methods to organize your database queries.

<strong>Apply callback macros</strong>

Rails has a slew of useful class macros that are executed during the ActiveRecord saving process. Similar to how Ruby has a few built in methods that are automatically triggered when a module is included (#self.included) or a method is added (#method_added), methods which conveniently allow for some pretty cool metaprogramming techniques, Rails also has methods that it looks out for when saving to the database. These callback macros include #before_validation, #after_validation, #before_save and #after_save. You can find a full listing of them <a href="http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html" target="_blank">here</a>.

For example, when building ViewFinder, we wanted to add the address of a  geotagged photo to the database immediately after the photo was saved. This way, we could always query the database by address as well as by coordinates rather than calculating the address at runtime. This was the result of decision that we had to make - do we save the address data to the database or calculate it whenever we need to use it? From a performance perspective, we figured that given that we were planning to make regular use of the address data (e.g., when displaying the actual location of a guessed photo), it would be more optimal to just retrieve static data from the database rather than using Geocoder each time to determine the address from the coordinates (which itself would make a Google Maps API call, so it would be slower than a database call).

Here is what the method looks like to convert coordinates to address. This was part of the Locatable (Locateable?) module that was mixed into the photo and guess models to enable point mapping.

{% codeblock lang:ruby %}
module Locatable
  # ...
  def generate_street_address
    unless latitude.nil? || longitude.nil?
      self.address = Geocoder.search("#{latitude}, #{longitude}")[0].address
    end
    save
  end
  # ...
end
{% endcodeblock %}

By using a callback macro, we could ensure that Rails would execute the #generate_street_address method whenever a new photo was deemed valid to our database by including the following into any model that included Locatable instance methods:

{% codeblock lang:ruby %}
class Photo
  # ...
  after_validation :generate_street_address
  # ...
end
{% endcodeblock %}

Callback macros are useful for cleaning and validating data and ensuring that objects are saved to the database are properly vetted. If you're saving the results of an API call as we were doing for ViewFinder, these callback macros can programmatically insert your application logic into the chain of events in converting the raw JSON result of an API call to application data.

Overall, Antipatterns provides useful nuggets of advice when creating Rails applications. That said, its important to know what to expect when reading the book - you won't get much in terms of explaining the inner workings of Rails or how Ruby code can generate all those dynamic finder and callback methods. Best go to Metaprogramming Ruby for a full walk-through of the Ruby object model. But, if you're looking for immediate, practical advice on how to leave behind bad habits, Antipatterns is a useful guide. When I started reading Antipatterns, I would only have to read a few pages before I could apply what I was reading to an application I was building. Think of it as a what-not-to-do guide to Rails and you'll be able to apply its instructions to an application right away.

