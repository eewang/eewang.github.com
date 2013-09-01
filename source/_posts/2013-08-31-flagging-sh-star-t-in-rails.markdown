---
layout: post
title: "Using Vector Bitmasks in Rails"
date: 2013-08-31 12:49
comments: true
categories: 
---
I've been using a gem called <a href="https://github.com/pboling/flag_shih_tzu" target="_blank">Flag Shih-Tzu</a> in a number of my projects
recently. In addition to being a wonderfully (and aptly) named gem,
FlagShihTzu makes it super simple to add bit mask fields, or Boolean
indicators, without having to run additional migrations. In other
words, it allows you to flag shit.

<!--more-->

When I first heard about <a href="http://en.wikipedia.org/wiki/Bit_array" target="_blank">vector bit fields</a> at 8to18 Media, I'll admit that they sounded intimidating. Its a pretty computer scienc-ey sounding term - vectors! bits! technology! But in reality, they're pretty much just an array of zeroes and ones, and the position of those bits in an array map to a vector legend. Imagine you have a 1 or 0 in each position of an array of size 3. A '1' in the first position indicates 2 ^ 0, a '1' in the second position is equal to 2 ^ 1, and a '1' in the third position is 2 ^ 2. That way, an array of [1, 0, 1] is equal to 2 ^ 0 + 2 ^ 2, or 5. Similarly, an array of [1, 1, 0] is equal to 3, and an array of [0, 1, 0] is equal to 2. Vector bitmasks (a.k.a. bit arrays) are a fairly elegant way to keep track of boolean, on-off switch attributes. <a href="http://devblog.xing.com/ruby-on-rails/a-rails-plugin-to-store-a-collection-of-boolean-values-as-a-single-bit-field/" target="_blank">Here's</a> a helpful short blog post explaining bit fields.

What the FlagShihTzu gem does is basically convert an integer field in your
database table to a vector of bit masks. Based on the mapping
structure that you define in the relevant model, FlagShihTzu will
set and get the necessary bit fields and give you a bunch if useful
helper methods to enable you to plan effectively for horizontal scale
in your schema. Without this gem and without rolling your own similar
functionality, adding Boolean indicators means running a new migration
and getting methods set up for each new field. This can be mitigated
by using define_method or method_missing, but adding in a gem provides
the useful functionality without adding unwieldy dependencies.

Getting set-up with the gem is pretty standard - set up a Rails app and add it to the Gemfile. I've had to think a lot about apartments and housing in general in the last few weeks (searching for an apartment in the East Village and helping friends move will do that), so I figured I could use the domain of apartment and housing search for my example app. The more I've thought about it, the more I think the real estate area is a pretty good domain model - its something everyone is familiar with, and especially in the case of explaining bit fields, has a slew of reasonable use cases for boolean attributes.

{% codeblock lang:bash %}
rails new nyc_apts
{% endcodeblock %}

{% codeblock lang:ruby %}
# Gemfile
gem 'flag_shih_tzu'
{% endcodeblock %}

After running bundle install, add a model and create your database table. FlagShihTzu will automatically look for a column named 'flags' unless you explicitly tell it to use a different column for the vector bitmask. In my project, I set the :columns option in my Building model to 'amenities'. This way, FlagShihTzu will change the value of my 'amenities' integer field as I set my boolean attributes.

{% codeblock lang:bash %}
rails generate model apartment
{% endcodeblock %}

{% codeblock lang:ruby %}
# in db/migrate
class CreateBuildings < ActiveRecord::Migration
  def change
    create_table :buildings do |t|
    	t.integer	:amenities
    	t.string	:address
    	t.string	:building_structure
	    t.string	:leasing_company
	    t.string	:description
	    t.string  :phone_number
	    t.integer :year_built
      t.timestamps
    end
  end
end
{% endcodeblock %}

After running rake db:migrate, I can add the following code to my building.rb file to tell it to use the FlagShihTzu functionality in my Building model.

{% codeblock lang:ruby %}
# app/models/building.rb
class Building < ActiveRecord::Base
	include FlagShihTzu

	has_flags 1 => :pets_allowed,
						2 => :doorman,
						3 => :elevator,
						4 => :laundry,
						:column => 'amenities'
end
{% endcodeblock %}

Note that once you choose an order for your flags, you cannot change them. Because the gem converts an integer into an array of bits, changing the mapping of the bit positions will yield different results for the same integer value.

That's all you need to add to your model in order to start playing around with the gem. In the background, FlagShihTzu adds an slew of useful methods for setting, getting and checking your boolean attributes. Specifically, these are the ones I find most useful. The documentation on Github has more instance methods, but I haven't found much use for them so far in my projects.

* Building#doorman
* Building#doorman?
* Building#doorman=
* Building#doorman_changed?

In my rails console, I can execute the following commands to quickly set my amenities column value using semantic setters for my boolean attributes.

{% codeblock lang:bash %}
001:0 > building = Building.new
=> #<Building id: nil, amenities: nil, address: nil, building_structure: nil, leasing_company: nil, description: nil, phone_number: nil, year_built: nil, created_at: nil, updated_at: nil>
002:0 > building.doorman?
=> false
003:0 > building.pets_allowed?
=> false
004:0 > building.doorman = true
=> true
005:0 > building.amenities
=> 2
006:0 > building.pets_allowed = true
=> true
007:0 > building.amenities
=> 3
{% endcodeblock %}

Also, calling 'has_flags' within your model produces class scopes that are useful for getting all instances of a Building that have doormen, for example. These method scopes are pretty straight forward - Building#doorman and Building#not_doorman, for example, will return an ActiveRecord Relation of all model instances that do or do not have the #doorman attribute set to true.

Although FlagShihTzu provides useful methods for setting and getting individual boolean attributes, I've been adding additional methods to set multiple attributes. For example, here are methods that allow for setting and getting attributes in bulk.

{% codeblock lang:ruby %}
# app/models/building.rb
class Building < ActiveRecord::Base
	# ...
	def set_building_flags(flags, list_name)
	  flag_bits = flags.collect { |flag| Building.flags(list_name.to_sym).index(flag.to_sym)}
	  list_bitmask = flag_bits.inject(0) { |sum, item| sum + 2**item }
	  send("#{list_name}=", list_bitmask)
	end

	def self.flags(column)
	  Building.flag_mapping[column.to_s].keys
	end

	def flag_setting(setting, list)
    list.reject { |a| ((setting || 0) & 2**list.index(a)).zero? }
  end

	def amenities_list
		flag_setting(self.amenities, Building.flags(:amenities))
	end
	# ...
end
{% endcodeblock %}

The #set_building_flags accepts an array of flags and an integer bitmask column in your database table and will set column appropriately. Similarly, #amenities_list takes advantage of the #flag_setting method to return the list of amenities a building instance has rather than just the integer value of the bitmask field.

{% codeblock lang:bash %}
001:0 > a = Building.new
002:0 > a.set_building_flags([:elevator, :laundry], :amenities)
=> 12
003:0 > a.amenities_list
=> [:elevator, :laundry]
{% endcodeblock %}

Also, the #flags class method extends the #flag_mapping class method provided by the gem by checking the hash that gets returned by the #flag_mapping method and returning the list of possible boolean indicators for the given column.

{% codeblock lang:bash %}
001:0 > Building.flags(:amenities)
=> [:pets_allowed, :doorman, :elevator, :laundry]
{% endcodeblock %}

Pretty straightforward stuff. Using FlagShihTzu in my projects has been a pretty lightweight way to add functionality that is easy to extend as my project grows. This way, adding new boolean indicators only requires one extra line of code in my model files, rather than running a new migration each time, which can quickly get unwiedly, especially for a young project that has not fully been fleshed out.
