---
layout: post
title: "How to Schedule Tasks Using 'Whenever'"
date: 2013-03-06 08:49
comments: true
categories: 
published: false
---

In building out my ticket tracker application, I realized that I needed to be able to automatically run scripts at certain intervals throughout the week. I wanted my application to execute a call to Stubhub's API and pull data on events periodically so that I didn't have to do it myself each day. In looking on <a href="http://ruby-toolbox.org" target="_blank">Ruby Toolbox</a>, I came across a gem called Whenever that allows you to quickly and easily schedule tasks using a cron log.

<!--more-->

Whenever serves as a wrapper for the cron job processor, which is functionality that ... [write about Unix cron log processing]

<strong>1) Set up Whenever gem</strong>


{% codeblock lang:bash %}
gem install whenever
{% endcodeblock %}

{% codeblock lang:bash %}
wheneverize .
{% endcodeblock %}

To set up the cron

<strong>2) Create a rake task</strong>

There are several ways to set up cron tasks to run automatically using Whenever, but I found the simplest way to be to just write a rake task in your Rails app's tasks folder. 

{% codeblock lang:ruby %}
namespace :events do
  desc "Rake task to get events data"
  task :fetch => :environment do
    events = Event.nba_search
    events.each do |item|
      item.each do |hash|
        @event = Event.new({
          :act_primary => hash["act_primary"],
          :act_secondary => hash["act_secondary"],
          :channel => hash["channel"],
          :city => hash["city"],
          :description => hash["description"],
          :eventGeoDescription => hash["eventGeoDescription"],
          :event_date_local => hash["event_date_local"],
          :event_time_local => hash["event_time_local"],
          :event_type => hash["event_type"],
          :genreUrlPath => hash["genreUrlPath"],
          :genre_grand_parent_id => hash["genre_grand_parent_id "], 
          :genre_grand_parent_name => hash["genre_grand_parent_name"],
          :last_chance => hash["last_chance"],
          :maxPrice => hash["maxPrice"],
          :maxSeatsTogether => hash["maxSeatsTogether"],
          :minPrice => hash["minPrice"],
          :state => hash["state"],
          :totalPostings => hash["totalPostings"],
          :totalTickets => hash["totalTickets"],
          :venue_name => hash["venue_name"],
          :espnUrlPath => hash["espnUrlPath"],
          :sportsTeam => hash["sportsTeam"],
          :latitude => hash["latitude"], 
          :longitude => hash["longitude"],
          :lat_lon => hash["lat_lon"]
        })
        @event.save
      end
    end
    puts "#{Time.now} - Success!"
  end
end
{% endcodeblock %}

{% codeblock lang:ruby %}
set :environment, "development"
set :output, {:error => "log/cron_error_log.log", :standard => "log/cron_log.log"}

every 1.day, :at => '10:00 pm' do
  rake "events:fetch"
end
{% endcodeblock %}

<strong>3) Update the crontab</strong>

4) 

- Creating your rake task
- Running console commands to update cron tasks
- Test your script first
- Be careful what you wish for
  - One inadvetantly scheduled and structured rake task took several hours and collected over 2.3 million rows of data. None of them had the right event_id, unforunately.

  

