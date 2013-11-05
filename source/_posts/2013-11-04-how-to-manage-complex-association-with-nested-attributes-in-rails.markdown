---
layout: post
title: "How to manage associations with nested forms in Rails"
date: 2013-11-04 18:43
comments: true
categories: 
published: true
---
In my experience using Rails to build web apps, I've come to realize that the framework has a very powerful flow of convention and patterns that you swim against at your own peril. One good example of this that I've been using recently is the accepts_nested_attributes_for helper in Rails.

<!--more-->

For those unfamiliar with ANAF, adding the class method to your ActiveRecord models enables a given object to assign and update attributes of an associated model object. This can be very powerful in that multiple objects with complex associations can be updated simultaneously with a single line of code. However, Rails makes certain assumptions about the structure of the params hash that gets passed to a given object when trying to update nested attributes.

When you add the accepts_nested_attributes_for helper to a model, Rails expects there to be in the hash you pass to either #assign_attributes or #update_attributes a key with _ attributes appended to it. Rails will then use that _attributes value (which is itself a hash of key-value pairs corresponding to attributes of the nested model) to assign attributes for the associated object. By default, accepts_nested_attributes_for assumes nothing about your association relationships (e.g., belongs_to, has_many, etc.). 

If this all sounds a bit confusing, don't worry - I was confused too when I first heard of the class method back in Flatiron. And while using accepts_nested_attributes_for is a luxury in that the same behavior can be written out manually, using it is more maintainable since you don't have to write additional code to handle new associations so long as the params hash passed to your controllers are properly structured. Here's an example of how the accepts_nested_attributes_for helper works.

Sticking with the football/sports metaphor that I like to use, say that I have a team model that has a has_many relationship with a player model. Each player belongs_to one team and each team has many players. Using accepts_nested_attributes_for enables you to update a team's players' attributes or a player's team's attributes in a single form.

{% codeblock lang:ruby %}
# player.rb
class Player < ActiveRecord::Base
  attr_accessible :name, :team
  belongs_to :team
  accepts_nested_attributes_for :team, :update_only => true
end

# team.rb

class Team < ActiveRecord::Base
  attr_accessible :name, :city, :coach
  has_many :players
end
{% endcodeblock %}

For a belongs_to relationship, be aware that when you pass in a hash like {team_id: 1, {team_attributes: {city: "Chicago", mascot: "Bears"}}, by default Rails will ignore the team_id part and create a new team using the team_attributes value. If you instead want to update the attributes of the team with id of 1, you'll need to add the update_only option to the ANAF call. Note, however, that adding the id key to the team_attributes hash is not equivalent to having the team_id key outside of the team_attributes hash. In fact, adding an id to the team_attributes hash that is not the id of the currently associated team will raise an error (something along the lines of "team with ID= could not be found for player with ID="). This seems counterintuitive to me, as I would want to be able to pass along all my associated data in the team_attributes hash (including the id of the team that I want updated) and have ANAF do both the association and the attribute assignment. 

The end goal of all this is to pass a params object into an assign_attributes or update_attributes call in the controller to update not only the primary object, but also all related objects. 

Assuming the params object looks like this:

{% codeblock lang:ruby %}
{
  id: 5,
  name: 'Jay Cutler',
  position: 'Quarterback',
  team_id: 10,
  team_attributes: {
    name: 'Chicago Bears',
    city: 'Chicago',
    coach: 'Marc Trestman'
  }
}
{% endcodeblock %}

I can then write a simple controller action that will update both player and team attributes with a single line of code, since the accepts_nested_attributes_for helper will assign attributes to associated models, so long as the hash passed into the assign_attributes call matches the Rails convention of using model_attributes and model_id to find and update an associated model attributes.

{% codeblock lang:ruby %}
class PlayersController < ApplicationController
  def update
    @player = Player.find(params[:id])
    @player.assign_attributes(params[:player])
  end
end
{% endcodeblock %}

With ANAF, assuming you have a properly structured nested hash for any associated data, Rails will be able to update the attributes of the associated models (or create new ones if there is no team_id attribute) as well as update the player's attributes all in one fell swoop.

Adding a team_attributes hash enables the creation of a new team or, with the addition of a team_id key, the updating of an existing team's attributes. ANAF also allows you to destroy associated teams if you pass in an '_destroy': true key-value pair adjacent to the team_id field. Note, however, that to enable this option, be sure to set the 'allow_destroy: true' option when specifying the assigns_nested_attributes_for helper.

Links:

<a href="http://railscasts.com/episodes/196-nested-model-form-part-1" target="_blank">Railscast on nested model forms</a><br>
<a href="http://vicfriedman.github.io/blog/2013/10/09/accepted-nested-attributes-for-with-name-field-in-html/" target="_blank">Victoria Friedman's blog post on accepts_nested_attributes_for</a><br>
<a href="http://robots.thoughtbot.com/accepts-nested-attributes-for-with-has-many-through" target="_blank">Thoughbot on ANAF with has-many-through relationships</a>
