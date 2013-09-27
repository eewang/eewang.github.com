---
layout: post
title: "Presenting the Rails Presenter Pattern"
date: 2013-09-26 20:58
comments: true
categories: 
---
The past few weeks have been pretty busy for me in terms of work. My team's product schedule has been ramping up, and we're right in the middle of rapidly prototyping both the customer-facing product and our editorial interface. Also, we recently added Backbone.js to our project, so I've been getting up to speed on how to integrate a client-side Javascript framework into a Rails application. 

<!--more-->

I find adding Backbone to this project particularly exciting because I wrote most of the client-side functionality of our editorial interface in raw Javascript, so I'm more than happy to have the opportunity now to use Backbone to provide structure and cleaner interactivity. Much like how at Flatiron learning Ruby, SQL and Sinatra before turning to Rails made me appreciate all the work Rails does behind the scenes, implementing Backbone after having written less-than-stellar Javascript to create the same functionality makes me appreciate the value front-end frameworks like Backbone, Ember or Angular provide.

Outside of work, my development time has been mostly focused on <a href="http://www.litcharts.com" target="_blank">Litcharts</a> - a web and mobile platform for reviews and analysis of classic literature that was co-founded by a former fellow Flatironer and current NYTimes colleague, <a href="https://github.com/meowist" target="_blank">Justin Kestler</a>. My particular task has been to create a tool that builds newspaper-layout-like PDF versions of litcharts using HTML, CSS and Javascript. What seemed like a simple task - I initially thought I could just use CSS3 columns and a gem like Prawn or Wicked PDF - quickly turned complicated for a variety of reasons that I won't go into at the moment. Needless to say, the past month or so has been quite busy for me with NYT and Litcharts work.

Consequently, the time available for me to blog has rapidly dwindled. I set a goal for myself to write one blog post a week as a way to motivate myself to keep learning code and exploring technology. What I've learned, though, is that after a program like Flatiron, where you're immersed in code and laser focused on rapidly building an entirely new skill set, I've hit my stride with code in that its become a familiar extension of myself, like writing or reading. This means I can keep learning about code while living a more balanced lifestyle than I did at Flatiron; I can't imagine that the countless hours spent writing, reading and talking about code in school is particularly sustainable in the long run.

But now back to the code. I recently implemented the Presenter pattern in my team's Rails application. For those not familiar with the Presenter pattern, its a way to keep your view and controller logic as clean as possible by having a Ruby class serve as the interface between your model and the view/controller. Another way to think of a Presenter in Rails is as a serializer for your Views. And much like how ActiveModel::Serializers enable you to build a clean API and encapsulate API logic within methods that are easily testable, Presenters keep your model-specific view logic in one class that you can test like you would any other Rails model.

Imagine you have an Article class that represents a newspaper article. Articles each have associated multimedia elements, which can vary from an image, video or slideshow. For the sake of this post, an article is a stripped down model that just stores a serialized hash to represent the associated multimedia assets.

{% codeblock lang:ruby %}
class Article < ActiveRecord::Base
  serialize :multimedia, Hash
end
{% endcodeblock %}

The multimedia attribute looks something like this when serialized.

{% codeblock lang:ruby %}
{
  :caption => 'foo',
  :credit => 'bar',
  :images => {
    :small => {
      :url => 'http://nytimes.com/image-small.jpg',
      :dimensions => {
        :height => 500,
        :width => 500
      }
    },
    :large => {
      :url => 'http://nytimes.com/image-large.jpg',
      :dimensions => {
        :height => 500,
        :width => 500
      }
    },
  },
  :videos => {
    :small => {
      :preview => 'http://nytimes.com/video.m4v',
      :dimensions => {
        :height => 300,
        :width => 400
      }
    }
  }
}
{% endcodeblock %}

In my views and controllers, I need to be able to access the urls and dimensions of each multimedia asset to render a preview of the asset. One option to get these attributes is to do a whole bunch of type checking within my views and access the url of an image as follows:

{% codeblock lang:ruby %}
@article = Article.new
@article.multimedia[:images][:small][:url] # => http://nytimes.com/image-small.jpg
{% endcodeblock %}

Rather than have the logic in the controller or view, the Presenter pattern provides a Ruby class that wraps a Rails model in order to create a simple API to the underlying object. Rather than going through the hash in my view, what I want to be able to do is just call #url or #caption on my article instance and get back the necessary information. I can add methods to my ArticlePresenter class to semantically access multimedia urls in my controllers and views rather than constantly checking a key-value pair in a deep nested hash.

{% codeblock lang:ruby %}
class ArticlePresenter

  DEFAULT_FORMAT = 'small'

  FORMAT_TYPES = [:small, :large]

  def initialize(article = nil)
    @article = article
  end

  FORMAT_TYPES.each do |format|
    define_method "#{format}_image_url" do
      @article.multimedia[:images][format][:url]
    end

    define_method "#{format}_video_url" do
      @article.multimedia[:videos][format][:url]
    end
  end

  def url(type = :image)
    @article.multimedia[type][DEFAULT_FORMAT][:url]
  end

  def caption
    @article.caption || "No caption"
  end

  # ... Other presenter methods

  def method_missing(method)
    @article.send(method) rescue nil
  end
end
{% endcodeblock %}

This way, in my controller I can wrap each article in a collection in an ArticlePresenter instance, so that in my views I just need to call @article.small_image_url or @article.large_video_url to get what I need rather than checking the multimedia hash. Both ways get the same result, but using a Presenter provides a testable object-oriented interface for view-specific logic relevant to a Rails model.

And like ActiveModel::Serializers, I can set default values for nil return without having to change the underlying data. In this example, if an article multimedia item doesn't have a caption, instead of returning nil to the view, I can instead return a string, "No caption". This helps tailor your model instance to specific views without adding unwiedly logic to your ERB/HAML templates.

{% codeblock lang:ruby %}
class ArticleController < ApplicationController
  def index
    @articles = Article.all.collect { |article| ArticlePresenter.new(article) }
  end
end
{% endcodeblock %}

One question I had when exploring the Presenter pattern was how it differed from plain old Rails helpers. I think Presenters are useful when you have logic specific to your models that you need encapsulated, whereas Rails view helpers are intended for more generic logic that is model agnostic, like converting dates or measurements. Of course, you could probably use view helpers to accomplish the same function as Presenters, but having a one-to-one relationship between a Presenter and its model feels cleaner to me.

Here are a few links that I found helpful while implementing the Presenter pattern.

<a href="http://blog.jayfields.com/2007/03/rails-presenter-pattern.html" target="_blank">Jay Fields on the Presenter Pattern</a>

<a href="http://www.derekhammer.com/2012/11/06/a-pattern-for-rails-presenters" target="_blank">A Pattern for Rails Presenters</a>

<a href="http://www.tamingthemindmonkey.com/2012/04/10/exploring-the-presenter-pattern" target="_blank">Exploring the Presenter Pattern</a>
