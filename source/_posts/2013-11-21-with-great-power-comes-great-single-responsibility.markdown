---
layout: post
title: "With Great Power Comes Great (Single) Responsibility"
date: 2013-11-21 08:37
comments: true
categories: 
---

In software development, the Single Responsibility Principle is a bedrock principle that transcends language or framework. The SRP states that each function / method, class and module should perform a single responsibility. This enables your code to be flexible, extensible and less prone to bugs since software components that are only loosely coupled to their peer components are simpler and easier to maintain. 

Although simple on its face, the SRP is very difficult to adhere to throughout an entire codebase. When I first started to code, my tendency was to build on top of existing methods or classes as I added functionality, rather than have an eye to constantly refactoring my code and separating and isolating the responsibilties and roles of each unit. This led to overgrown User classes that contained all sorts of logic unrelated to a strict notion of a 'user'. At a higher level, violations of the SRP can lead to monolithic applications that simply do too much, and I gather that much of the recent theme of breaking up monolothic apps into a service-based architecture has to do with trying to get back to a building apps in line with the SRP.

In the application I'm helping build at the NYT, we recently came across a simple problem that I felt was a good example of the SRP in practice. In our web form, users have the ability to input a URL and the form can determine whether that URL relates to an NYT article or a non-NYT article. Similarly, an asset should be able to tell if the its associated with the NYT by looking at the attached URL. Although URLs are associated with assets, they are accessed via the post form, so posts delegate URL calls its asset.

The first version of this predicate method (to return `true` or `false` depending on whether the URL string matched an nytimes regex) looked like this:

Demonstrating the Single Responsibility Principle

{% codeblock lang:ruby %}
class Post < ActiveRecord::Base
  delegate :url, to: :asset

  def nyt_url?(check_url = nil)
    url = check_url ||= (asset ? self.url : nil)
    url ? NYT_REGEX.match(url).present? : false
  end

end
{% endcodeblock %}

{% codeblock lang:ruby %}
module NYT
  NYT_REGEX = /.nytimes\.com/

  def nyt_url?(url)
    NYT_REGEX.match(url).present?
  end
end
{% endcodeblock %}

{% codeblock lang:ruby %}
class Post < ActiveRecord::Base
  include NYT
  delegate :url, to: :asset

  def has_nyt_asset?
    asset.nyt? if asset
  end
end

{% endcodeblock %}

{% codeblock lang:ruby %}
class Asest < ActiveRecord::Base
  def nyt?
    NYT_REGEX.match(url).present?
  end
end
{% endcodeblock %}