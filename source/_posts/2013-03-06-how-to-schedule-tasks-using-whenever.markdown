---
layout: post
title: "How to Schedule Tasks Using 'Whenever'"
date: 2013-03-12 20:49
comments: true
categories: 
---

In building out my ticket tracker application, I realized that I needed to be able to automatically run scripts at certain intervals throughout the week. I wanted my application to execute a call to Stubhub's API and pull data on events periodically so that I didn't have to do it myself each day. In looking on <a href="http://ruby-toolbox.org" target="_blank">Ruby Toolbox</a>, I came across a gem called <a href="https://github.com/javan/whenever" target="_blank">Whenever</a> that allows you to quickly and easily schedule tasks using a nifty tool called a cron log.

<!--more-->

Whenever serves as a wrapper for the cron job processor, which is functionality inherent to your operating system that allows for scheduling jobs to run periodically. According to <a href="http://en.wikipedia.org/wiki/Cron" target="_blank">Wikipedia</a>, cron tasks are often used to automate system maintenance or administration, though it can be used for other purposes, like connecting to the Internet or downloading e-mail. Apparently you can set up cron tasks independently of Ruby or any web application; Whenever enables you to manipulate standard functionality of your machine using Ruby code.

To get started with Whenever, you need to install the gem. Like any other gem, this is a pretty simple installation process:

<strong>1) Set up Whenever gem</strong>

{% codeblock lang:bash %}
gem install whenever
{% endcodeblock %}

Once you have the gem installed, you need to "wheneverize" your application. You can do this by going into the root directory of your application and using the following console command:

{% codeblock lang:bash %}
wheneverize .
{% endcodeblock %}

This command creates a "schedule.rb" file in your config folder, which you'll be using to manage your application's automated tasks.

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
        # Code to instantiate an event
        })
        @event.save
      end
    end
    puts "#{Time.now} - Success!"
  end
end
{% endcodeblock %}

The above code takes advantage of a few rake-specific keywords, like namespace, desc and task. The namespace:task combination is how you'll actually call your rake task - in the Rails rake task "db:migrate", "db" is the namespace and "migrate" is the task. In this particular instance, I'm using Whenever to parse event data from Stubhub's API and save them to a database using ActiveRecord. After writing the rake task, I am now able to call the above rake task in my console using:

{% codeblock lang:bash %}
rake events:fetch
{% endcodeblock %}

After I set up my rake task, I need to use Whenever to schedule my task. Thankfully, the schedule.rb that Whenever automatically creates in your config folder comes ready with in-file instructions about how to set-up cron tasks:

{% codeblock lang:ruby %}
# Use this file to easily define all of your cron jobs.
#
# It's helpful, but not entirely necessary to understand cron before proceeding.
# http://en.wikipedia.org/wiki/Cron

# Example:
#
# set :output, "/path/to/my/cron_log.log"
#
# every 2.hours do
#   command "/usr/bin/some_great_command"
#   runner "MyModel.some_method"
#   rake "some:great:rake:task"
# end
#
# every 4.days do
#   runner "AnotherModel.prune_old_records"
# end

# Learn more: http://github.com/javan/whenever
{% endcodeblock %}

 Whenever applies a simple, intuitive domain-specific language (DSL) with a few keywords to schedule tasks (you can find a more thorough run-down in in the gem's README file. Whenever uses a human-readable syntax that takes the form of "every [x].[minute/hour/day/week/etc.], :at => [time] do" to schedule tasks. The gem has pretty good support for specialized tasks, and even supports scheduling tasks on just weekdays or weekends. 

In the below code, I've told Whenever to run my events:fetch rake task every day at 10:00 pm. I've also set the environment to be my development environment and I set a location in my Rails application for both an error log and a default log so that I can get feedback from Whenever just to make sure that it ran correctly. I don't think the "set :output" line is absolutely necessary, but when I started using Whenever, I wasn't always sure if the job was running correctly, so I made the rake task output to a log so I could follow the execution path.

{% codeblock lang:ruby %}
set :environment, "development"
set :output, {:error => "log/cron_error_log.log", :standard => "log/cron_log.log"}

every 1.day, :at => '10:00 pm' do
  rake "events:fetch"
end
{% endcodeblock %}

<strong>3) Update the crontab</strong>

After setting up the rake task, you need to run following command in the console to tell Whenever to start running your periodic tasks:

{% codeblock lang:bash %}
whenever --update-crontab
{% endcodeblock %}

You should see a result along the lines of:

{% codeblock lang:bash %}
[write] crontab file updated
{% endcodeblock %}

If you want to check that the gem correctly scheduled your cron task, you can use the command "crontab -l" to list all cron tasks.

{% codeblock lang:bash %}
crontab -l
# Begin Whenever generated tasks for: /Users/eugenewang/projects/rails/ticket_tracker/config/schedule.rb
0 22 * * * /bin/bash -l -c 'cd /Users/eugenewang/projects/rails/ticket_tracker && RAILS_ENV=development bundle exec rake events:fetch --silent >> log/cron_log.log 2>> log/cron_error_log.log'
{% endcodeblock %}

And that should be it! You can now sit back and watch Whenever automatically execute your rake tasks. When you start using Whenever, I would recommend shortening the feedback loop by writing one or two test rake tasks that just print "Hello World" to your cron log every minute. This is what I did just to confirm that the gem was working. Then, I moved progressively onward until I was able to execute large API calls. 

One word of caution though when using Whenever - be sure you have a general sense of how long your tasks actually take to execute. One rake task I set up took over a minute to execute on its own, and I inadvertantely scheduled the task to run every minute, so naturally a task backlog quickly built up. Even when I stopped the task and updated the crontab, the backlog caused the task to continue to run until it ran out of gas on its own. When all was said and done, I had over 2 million rows of data in my database - much of it duplicative and unwanted. Although I ended up with way too much data, at least I knew that the gem worked!

