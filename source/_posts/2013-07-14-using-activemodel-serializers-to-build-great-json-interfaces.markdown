---
layout: post
title: "Using ActiveModel Serializers to Build Great JSON Interfaces"
date: 2013-07-23 14:07
comments: true
categories: 
published: true
---

I've been at the New York Times on the New Digital Products team for a little over two weeks now. Thus far, its been an awesome experience. I've had the opportunity to jump right in and commit code, primarily helping build the Rails backend interface for a mobile application. I've been spending time trying to get up to speed on the various internal software tools that the NYT provides, and I've come to appreciate that from a technical perspective, the NYT is an API-driven organization. 

Most of the APIs I dealt with at Flatiron were for public consumption (e.g., Viewfinder leveraged the Instagram API pretty heavily), so its been interesting to see how the NYT develops internal APIs to decrease inter-team dependencies while reducing the potential for overlapping responsibilities. With so many teams (e.g., internal CMS, user-generated content, video, etc.) working together to build a single journalistic product, I've learned that necessary to have public, internal JSON interfaces for different teams to work with. As such, I've had the chance to use a Rails module called ActiveModel::Serializers that helps developers build a sensible JSON interface in an object-oriented manner that's pretty intuitive.

<!--more-->

ActiveModel::Serializers is a component of the Rails API that enables an object-oriented approach to serializing ActiveRecord objects. The module can also be used to provide customized JSON objects depending on the requester. Serializers know about both a model and the environment in which it is being accessed, so if an admin user accesses the API you as the developer can provide one level of access to the underlying data, while providing a less detailed view to a non-admin user. To get this functionality, you can just add the 'active_model_serializers' gem to your Gemfile.

{% codeblock lang:ruby %}
# Gemfile.rb
gem 'active_model_serializers'
{% endcodeblock %}

Serializers serve as a thin layer of abstraction between models and your JSON, and can be a useful tool in quickly adjusting your application's API without making large scale changes to the underlying schema. With AM:Serializers, its easy to change JSON presentation logic without affecting underlying model logic, and enables you to quickly add or modify attributes to an object's JSON representation that are not explicitly part of the model/table.

For example, say you're responsible for building the backend for a football statistics application. The NFL wants to create a mobile app for delivering useful analytics to users and in order to do so, needs to be able to get statistics for individual players and teams. However, the data requirements can vary from week-to-week and by use case - for example, international apps built off the stats API require 'receiving yards' to be in meters whereas domestic apps display such stats as yards. By adding in serializer support, you can deliver a different data structure to each client application even if the underlying data is the same. Another example might be timestamps, which are saved in the database as a Unix timestamp but may need to be delivered to client apps in a human readable form, adjusted for each user's time zone.

This is admittedly a contrived example, but it gets the point across. Serializers make it easy to further separate the presentation layer of your application from the underlying model layer, even though by "presentation" I'm referring to the JSON representation of model objects rather than actual HTML files. This way, other applications can access the underlying data by virtue of a public API while you continue to tinker around with the backend. So long as your API remains consistent (or changes are properly documented), other teams or applications that depend on your API can continue developing without worrying about every change you make to the underlying code.

To get started, just use a Rails generator to create a serializer:

{% codeblock lang:shell %}
rails generate serializer team
{% endcodeblock %}

Imagine I have a Team class (i.e., model) to represent a football team. A single team has many games (16 regular season, in the case of the NFL) and many players. In the serializer, I specify which attributes I want available in the JSON object, which typically will correspond to the methods in the body of the class or the attributes of the model. If I have a custom defined attribute (e.g., an attribute that is not the same as an attribute in my model), I need to write out a method with the same signature so that Rails knows how to interpret my serialized attribute. 

I can also specify associations and customize how those associations appear in my JSON. Each team has many players, which is a separate model that has data about a player's height, weight, jersey number, etc. In this example, I don't need all of that information - all I need is the player's name, jersey number and age. So I can override the typical association dispatch call (which would embed the JSON representation of each player associated with the team under the :players key) by writing a custom method.

Note that you get access to an 'object' variable within a given serializer, which returns the related model object (e.g., a given team in this example) and would be the same as referencing 'self' within the corresponding model file.

{% codeblock lang:ruby %}
class TeamSerializer < ActiveModel::Serializer
  attributes :team_name, :date_updated, :multimedia, :team_url, :stadium_address, :finances
  has_many :players
  has_many :games
  delegate :current_user, :to => :scope

  def team_name
  	"#{object.city} #{object.name}"
  end

  def date_updated
    object.updated_at.to_i
  end

  def team_url
  	"http://www.nfl.com/#{obje.ctcity}"
  end

  def stadium
  	stadium = object.stadium
  	"#{stadium.address}, #{stadium.city} #{stadium.state}, #{stadium.zip}"
  end

  def multimedia
  	object.team_logo
  end

  def players
  	object.players.collect { |player| [player.name, player.number, player.age]}
  end

  def finances
  	if current_user.is_admin?
  	  {:revenue => object.revenue, :profit => object.profit, :costs => object.costs}
  	else
  	  {}
  	end
  end

end
{% endcodeblock %}

In the controller, make sure that you add a respond_to block that specifies a json format.JSON responses to requests that hit the '#show' action will be translated by the TeamSerializer class and formatted accordingly.

{% codeblock lang:ruby %}
# teams_controller.rb
class TeamsController < ActiveModel::Controller
  # ...
  def show
    @team = Team.find(params[:id])
    respond_to do |format|
  	  format.html
  	  format.json { render :json => @team }
    end
  end
  # ...
end
{% endcodeblock %}

Notice too that I use the 'current_user' helper method (a method that I would typically define in my application controller to reference the currently logged in user) to restrict the access level to a team's financial data only to team administrators. In order to have access to this method, I need to delegate the current_user method call to the scope object, which I can specify in my application controller like so:

{% codeblock lang:ruby %}
# application_controller.rb
serialization_scope :view_context
{% endcodeblock %}

Rails will automatically look for a TeamSerializer file when it publishes JSON, but if you want to specify a customized serializer, you can indicate it in your model file, like so, assuming you also change the serializer name to match:

{% codeblock lang:ruby %}
# team.rb
class Team < ActiveRecord::Base
  #...
  def active_model_serializer
  	FootballTeamSerializer
  end
  #...
end
{% endcodeblock %}

Then, specify the custom serializer within the controller action call by passing a :serializer => :custom_serializer key-value into the render call, like so:

{% codeblock lang:ruby %}
render :json => @team, :serializer => FootballTeamSerializer
{% endcodeblock %}

I've posted a few useful links below that go over how to start using serializers in your application (surprise surprise, there's even a RailsCast on ActiveModel::Serializers).

As for testing these serializers, I've been testing my serializer classes using model specs with Rspec. I've used OpenStruct to create mock objects within the spec, just to make sure that the resulting JSON is correctly formatted. I've also been meaning to add integration tests so that the full request-response cycle is validated, not just the JSON API endpoint.

If you're looking for more information, here are some helpful links that explain further the benefit of using ActiveModel::Serializers in building well-structured JSON for your API.

<a href="http://robots.thoughtbot.com/post/50091183897/fast-json-apis-in-rails-with-key-based-caches-and" target="_blank">Thoughtbot on Fast JSON APIs</a>

<a href="http://www.ruby-doc.org/gems/docs/a/active_model_serializers-0.6.0/ActiveModel/Serializer.html" target="_blank">Documentation on ActiveModel::Serializers</a>

<a href="http://byroot.github.io/ams-slides/#/" target="_blank">Helpful Slides</a>

<a href="http://yehudakatz.com/2010/01/10/activemodel-make-any-ruby-object-feel-like-activerecord/" target="_blank">Yehuda Katz on Serialization</a>

<a href="http://railscasts.com/episodes/409-active-model-serializers" target="_blank">Active Model Serializers Railscast</a>