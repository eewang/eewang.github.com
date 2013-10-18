---
layout: post
title: "How to manage complex association with nested attributes in Rails"
date: 2013-10-16 23:43
comments: true
categories: 
published: false
---
In my experience using Rails to build web apps, I've come to realize that the framework has a very powerful flow of convention and patterns that you swim against at your own peril. One good example of this that I've been using recently is the accepts_nested_attributes_for helper in Rails.

<!--more-->

For those unfamiliar with ANAF, adding the class method to your ActiveRecord models enables a given object to assign and update attributes of an associated model object. This can be very powerful in that multiple objects with complex associations can be updated simultaneously with a single line of code. However, Rails makes certain assumptions about the structure of the params hash that gets passed to a given object when trying to update nested attributes.

When you add the accepts_nested_attributes_for helper to a model, Rails expects there to be a  in the hash you pass to either #assign_attributes or #update_attributes a key with _attributes appended to it. Rails will then use that _attributes value (which is itself a hash of key-value pairs corresponding to attributes of the nested model) to assign attributes for the associated object. By default, accepts_nested_attributes_for assumes nothing about your association relationships (e.g., belongs_to, has_many, etc.). 

If this all sounds a bit confusing, don't worry - I was confused too when I first heard of the class method back in Flatiron. And while using accepts_nested_attributes_for is a luxury in that the same behavior can be written out manually, using it is more maintainable since you don't have to write additional code to handle new associations so long as the params hash passed to your controllers are properly structured. Here's an example of how the accepts_nested_attributes_for helper works.

Sticking with the football/sports metaphor that I like to use, say that I have a team model that has a has_many relationship with a player model. Each player belongs_to one team and each team has many players. Using accepts_nested_attributes_for enables you to update a team's players' attributes or a player's team's attributes in a single form.

<-- insert example code with models -->

For a belongs_to relationship, be aware that when you pass in a hash like {team_id: 1, {team_attributes: {city: "Chicago", mascot: "Bears"}}, by default Rails will ignore the team_id part and create a new team using the team_attributes value. If you instead want to update the attributes of the team with id of 1, you'll need to add the update_only option to the ANAF call. Note, however, that adding the id key to the team_attributes hash is not equivalent to having the team_id key outside of the team_attributes hash. In fact, adding an id to the team_attributes hash that is not the id of the currently associated team will raise an error (something along the lines of "team with ID= could not be found for player with ID="). This seems counterintuitive to me, as I would want to be able to pass along all my associated data in the team_attributes hash (including the id of the team that I want updated) and have ANAF do both the association and the attribute assignment. 

Links:

http://railscasts.com/episodes/196-nested-model-form-part-1
