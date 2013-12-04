---
layout: post
title: "With Great Power Comes Great (Single) Responsibility"
date: 2013-12-04 07:37
comments: true
categories: 
---

In software development, the Single Responsibility Principle is a bedrock principle that transcends language or framework. The SRP states that each function / method, class and module should perform a single responsibility. This enables your code to be flexible, extensible and less prone to bugs since software components that are only loosely coupled to their peer components are simpler and easier to maintain. 

<!--more-->

Although simple on its face, the SRP is very difficult to adhere to throughout an entire codebase. When I first started to code, my tendency was to build on top of existing methods or classes as I added functionality, rather than have an eye to constantly refactoring my code and separating and isolating the responsibilties and roles of each unit. This led to overgrown `User` classes that contained all sorts of logic unrelated to a strict notion of a 'user'. At a higher level, violations of the SRP can lead to monolithic applications that simply do too much, and I gather that much of the recent trend of breaking up monolothic apps into a service-based architecture has to do with trying to get back to a building apps in line with the SRP.

In the application I'm helping build at the NYT, we recently came across a simple problem that I felt was a good example of the SRP in practice. Say you have an app like Pinterest, whereby people can pin items to their pinboard that correspond to various destinations. These destinations can be food blogs, Instagram, etc. All destinations have one or more pinned items that may have different visual displays, but not all pinned items necessarily have a destination (e.g., if you just want to pin a note or reminder).

In this app, to create a pinned item, users have the ability to input a URL and the app can determine whether that URL relates to a popular destination (for example, Instagram) and perform some logic accordingly. There may be some additional functionality the app wants to apply for Instagram-related items on a board, given that Instagram is likely a popular source for pinned items. Similarly, a destination source should be able to tell if the its associated with Instagram by looking at the attached URL (e.g., `http://www.instagram.com/:user_id/:photo_id`). Although URLs are associated with destinations, they are accessed via the pinned items on the board, so pinned items have to delegate URL calls to their associated destination (if they have one).

The first version of this predicate method to return `true` or `false` depending on whether the URL string matched an Instagram regex, like `/instagram.com/` might look like this:

{% codeblock lang:ruby %}
class Item < ActiveRecord::Base
  belongs_to :destination
  delegate :url, to: :destination

  INSTAGRAM_REGEX = /instagram.com/

  def instagram_url?(check_url = nil)
    url = check_url ||= (destination ? self.url : nil)
    url ? INSTAGRAM_REGEX.match(url).present? : false
  end

end
{% endcodeblock %}

While terse, this `instagram_url?` predicate method does a lot of things. First, it can check any string provided in an argument and return true or false depending on whether it matches a preset Instagram URL. Second, it checks the URL associated with the destination if no URL is passed through as an argument. And finally, it manages the type checking if there is an associated destination (since not all pinned items have destinations, and calling `url` on an item without an destination will raise `NoMethodError: undefined method 'url' for nil:NilClass`. 

At first glance, the benefit as I saw it of this code is that its relatively succinct - only 4 lines of implementation to handle the multiple situations in which the app may need to handle checking URLs. That said, it clearly is not the most readable. It relies on too many ternary operators, and its not immediately evident in which situations the method returns `true` or `false`. In other words, the code smells bad. The method should actually be broken out into two different checks on URLs (one for strings, and one for the URL attribute on a destination), and another component that serves as traffic handler depending on if an item has a destination to which it can delegate a URL call. 

A better implementation that adheres more closely to the SRP may look like this:

{% codeblock lang:ruby %}
module Instagram
  INSTAGRAM_REGEX = /instagram/

  def instagram_url?(url)
    INSTAGRAM_REGEX.match(url).present?
  end
end
{% endcodeblock %}

{% codeblock lang:ruby %}
class Item < ActiveRecord::Base
  include Instagram
  belongs_to :destination
  delegate :url, to: :destination

  def has_instagram_destination?
    destination.instagram? if destination
  end
end

{% endcodeblock %}

{% codeblock lang:ruby %}
class Destination < ActiveRecord::Base
  has_many :items

  def instagram?
    INSTAGRAM_REGEX.match(url).present?
  end
end
{% endcodeblock %}

This changes the implementation in a few ways. First, the string checking has been moved to a module, since the method could technically be called across classes. It could even be used as a monkey patch to the `String` class, so that all you would have to do is write `"test url".instagram?`, but monkey patching is rarely the best solution for a problem, even if it is the fastest. Second, the determination of whether a destination is from Instagram (as determined by its URL) was moved to the `Destination` class, which makes logical sense. Destinations, not pinned items, should know if they are from Instagram. And finally, pinned items hold the responsibility of traffic cop - knowing if they have a destination or not, and if so, then checking if such destination is Instagram. This way, each method does just one responsibility, and the predicate methods for each are more clear, in that `has_instagram_destination?` makes sense for an item and `instagram?` makes sense for a destination.

While this refactoring is technically more lines of code than the first version, its clearly more readable and adheres more tightly to the SRP. Each method does one thing, and the implementation code is wrapped within a clear predicate method that can be used by other methods whenever they need to check where a URL comes from - be it a string or a saved attribute on a destination. This also better encapsulates logic so that future extensions to the code are easier to make (e.g., the `Destination#instagram?` method could forseeably have a more nuanced implementation that should be abstracted away from the associated item). 

I have found this tension between terse / DRY-to-the-max code and readable / clean code to be fairly common across my projects. With Ruby, its easy to go overboard in DRYing up your code into a shriveled, unreadable mess of heavily metaprogrammed methods. But if you're working in a team of other developers (which you almost always are), very DRY code also means that its probably less readable and may be in violation of the SRP.

Like with any other software development principle, the SRP exists in contrast to other goals, like keeping code DRY. Exercising good judgement on when to unbundle classes or methods by refactoring comes only with time, although in my experience, beginners tend to go too far in lumping all complexity into a single class or method. The challenge lies in knowing when adhering to the SRP or DRYing out your code would make your app more maintainable and flexible in the long-run in the face of near-certain change.

Here are a few helpful links on the SRP:
<br>
[SOLID Ruby: Single Responsibility Principle](http://www.sitepoint.com/solid-ruby-single-responsibility-principle/)

[The King of the Single Responsibility Principle](http://www.mikepackdev.com/blog_posts/38-dci-the-king-of-the-single-responsibility-principle)

[Ruby on Rails and the Single Responsibility Principle](http://jonkruger.com/blog/2010/10/19/ruby-on-rails-and-the-single-responsibility-principle/)