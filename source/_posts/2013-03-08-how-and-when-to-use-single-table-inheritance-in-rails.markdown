---
layout: post
title: "How (and When) to Use Single Table Inheritance in Rails"
date: 2013-03-08 00:04
comments: true
categories: 
published: false
---

Last week as I was developing an application to track and analyze ticket and event postings, I came across a design problem. I had first started working on my ticket tracker application as a way to parallel the learning that we were doing in class. I started with a simple scraper that pulled data out of the Stubhub's client-facing HTML documents, then moved on to writing a simple ORM that saved the data to a SQLite database, then moved the application into Sinatra and finally into Rails. 

<!--more-->

Along the way, as I was increasing the level of structural complexity, I was also expanding the scope of the project. I had initially started with just NBA tickets, but I've recently started exploring how to broaden the application to any event. This presented me with a design conundrum. I designed my models in the initial Ruby application to just get NBA data - the classes, methods and variables were all had "NBA" or "basketball" in the name. Also, the model attributes and table fields were specific to the JSON data related to NBA games that I could retrieve. However, this wouldn't work exactly for concerts or music festivals. Basketball games have both a home and away team, but the same cannot be said for concerts. Furthermore, sports events - e.g., baseball, basketball, football - are mostly similar in how the data is structured (home and away teams, venue, date, etc.), but each may present idiosyncratic data that is not shared by other sports. On the one hand, each type of event shares many of the same attributes, but each also have their own quirks.

As I explored how to accurately design my models and schema while also striving to remain DRY and not denormalize my data too much, I came across the concept of Single Table Inheritance (hattip to our instructor <a href="https://twitter.com/withloudhands/" target="_blank">Bob Whitney</a> for introducing me to the design concept.) 

<strong>1) What is Single Table Inheritance (STI)?</strong>

STI is basically the idea of using a single table to reflect multiple models that inherit from a base model, which itself inherits from ActiveRecord::Base. In the database schema, sub-models are indicated by a single "type" column. In Rails, adding a "type" column in a database migration is sufficient (after writing the models) to let Rails know that you're planning to implement STI. For example, I decided to use STI to convert what could have been one table/model per event type - a different table for football, basketball, hockey, festivals, concerts, etc. - into just two tables, sports and concerts. Conveniently enough, this also mirrors Stubhub's API, which categorizes events broadly into three categories - sports, concerts and theater (which I'll aim to add at a later date). 

Where STI is helpful is in the structure of the models that relate to the table. For my sports table, I have multiple models that each save data to the same table. I have a class called "Sport" (I know, an awkward name, but I'm still getting used to the Rails pluralization conventions) that inherits from ActiveRecord::Base, and separate models called ProBasketball, ProFootball, Baseball, etc. that all inherit from the Sport class. This enables each of the individual sports to take on the functionality of a Sport (and ActiveRecord by default), yet also provides me with sufficient flexibility to write lower-case sport-specific methods, constants and variables. Each of these sports save to the "sports" table, with the sole differentiator being the "type" column, for which Rails uses the name of the Sport "sub-class" as the value (Rails just knows, don't ask me how.).

<strong>2) When should I use STI?</strong>

STI enables you to use all the typical model methods in Rails, like .new or .create, to both the super-class and the sub-classes within a single table.

# Add codeblock to for Rails console

A word of warning when using STI - don't use STI just because objects seem similar. Make sure that there is an object-oriented relationship between them. STI is a way to apply a higher-level abstraction to your schema and models, but don't get carried away. For example, it wouldn't make sense to just have a single table called "Objects" that uses STI to manage many relationships among unrelated models, each of which just happen to be an instance of an "object" (which everything is in Ruby anyways). A more practical example might be whether to use STI manage a table called "Vehicles," which relate to "Car", "Bicycle" and "Tank" sub-classes. In this case, STI probably doesn't pass the good design smell task, since a car has different characteristics and functionality than a tank, even if both are technically vehicles. A better use case for STI might be a "Cars" table that relates to sub-classes of Car, such as SUV, Hybrid and Sedan. These sub-classes may each have idiosyncratic functionality, but share many of the same characteristics and, more importantly, are intuitively related from an OO perspective. In this case, using STI may be an effective way to streamline data collection and avoiding repetition in your schema.

<strong>3) How do I implement STI?</strong>

To get started with STI from a database perspective, all you need to do is add a field called "type" to the table. Rails takes this type field and applies the name of the sub-classes that inherit from the class for which the table is named as the value for a row of data. 

{% codeblock lang:ruby %}
class CreateSports < ActiveRecord::Migration
  def change
    create_table :sports do |t|
      t.string :type
      t.string :act_primary
      t.string :act_secondary
      # ... more column fields #
      t.timestamps
    end
  end
end
{% endcodeblock %}

Once the schema is set up, the next step is to get the models set up and the proper inheritance tree established. But, before you do that, make sure to add any subfolders in your app/models folder to the autoload path in the application.rb file. This will ensure that Rails knows to look at subfolders, which may be good to use to effectively organize your model files.

{% codeblock lang:ruby %}
# /config/application.rb
config.autoload_paths += %W(#{config.root}/app/models/sports)
{% endcodeblock %}

Next, you can write out the structure of the models. STI in Rails presumes that there is one "super-class" that takes on the name of the schema table and inherits from ActiveRecord::Base. In my example, this is the "Sport" class. Here is where I keep any variables or methods that relate to sports generally - e.g., the API call requests, for example. Then, you can create separate sub-classes for objects that will inherit the super-class functionality, yet also enable you to write sub-class-specific methods. For example, each individual sport has its own teams that I want to get data for, so I keep the constants (an array of NBA teams, for example) relevant to only one sport in that sport's sub-class.

{% codeblock lang:ruby %}
# /app/models/sports/sport.rb
class Sport < ActiveRecord::Base
  # Methods, variables and constants
end
{% endcodeblock %}

{% codeblock lang:ruby %}
# /app/models/sports/probasketball.rb
class ProBasketball < Sport
  # Methods, variables and constants
end
{% endcodeblock %}

{% codeblock lang:ruby %}
# /app/models/sports/profootball.rb
class ProFootball < Sport
  # Methods, variables and constants
end
{% endcodeblock %}

{% codeblock lang:ruby %}
# /app/models/sports/baseball.rb
class Baseball < Sport
  # Methods, variables and constants
end
{% endcodeblock %}

<strong>4) What are some drawbacks of STI?</strong>

STI isn't always the best design choice for your schema. If sub-classes that you intend to use for STI have many different data fields, then including them all in the same table would result in a lot of null values and make it difficult to scale over time.



<strong>5) Where can I learn more about STI?</strong>

There are a few good resources and blog posts online, as well as the requisite StackOverflow inquiries, about STI.

<a href="http://www.alexreisner.com/code/single-table-inheritance-in-rails" target="_blank">Alex Reisner - Single Table Inheritance in Rails</a>

<a href="http://www.therailworld.com/posts/18-Single-Table-Inheritance-with-Rails" target="_blank">The Rail World - Single Table Inheritance with Rails</a>

<a href="http://www.archonsystems.com/devblog/2011/12/20/rails-single-table-inheritance-with-polymorphic-association/" target="_blank">Archon Systems - Rails Single Table Inheritance with Polymorphic Association</a>



