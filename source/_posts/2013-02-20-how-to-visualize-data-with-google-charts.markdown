---
layout: post
title: "How to Visualize Data With Google Charts For Ruby"
date: 2013-02-20 17:42
comments: true
categories: Flatiron Data Visualization
published: true
---

I love data. Specifically, I love data visualizations. Infographics, charts, whatever you call it, if it displays data visually, chances are I'll want to take a look at it. Data graphics combine the creative and analytical elements of programming to succinctly and efficiently communicate information to a reader. If a picture is worth a thousand words, I'm pretty sure an infographic is worth a thousand pictures. Data graphics can take an array of different forms - charts, 3-D graphs, maps, etc., the possibilities are virtually endless. I wanted to explore data visualization tools in Ruby, starting with basic chart functionality. 

<!--more-->

In learning how to code over the past two weeks, I've come to appreciate the flexibility of Ruby and the ability for pretty much anything to be structured as a dataset. Conceptually, I was looking for a tool that could take in a data as an array or hash and convert that data into a table or graph. In perusing <a href="http://www.ruby-toolbox.com/" target="_blank">Ruby Toolbox</a>, I came across the Googlecharts gem, which is basically a Ruby wrapper for the <a href="http://developers.google.com/chart/" target="_blank">Google Charts API</a>. In taking a look at the <a href="http://googlecharts.rubyforge.org/" target="_blank">documentation</a> and <a href="http://github.com/mattetti/googlecharts/" target="_blank">code base on GitHub</a>, the instructions seemed pretty straight forward, so I figured I'd give it a try.

<strong>1) Installing Googlecharts</strong>

Getting setup with the Googlecharts gem is pretty straightforward. Like other gems, you can install it straight from the command line by running:

{% codeblock lang:bash %}
$ gem install googlecharts
{% endcodeblock %}

Once the installation command runs, you just need to be sure to require the gem at the top of your Ruby file by writing:

{% codeblock lang:ruby %}
require 'gchart'
{% endcodeblock %}

And that's it! You're ready to start using Googlecharts.

<strong>2) Let's Chart Some Data!</strong>

The primary class that you interact with when using Googlecharts is the Gchart class. In looking at the source code on GitHub, this class has numerous attributes that can be used to define the data and formatting for bar charts, pie charts, line graphs and scatterplots (the gem also seems to support basic 3-D graphing). When you create a new Gchart, you can pass in a number of attributes as parameters to the new Gchart instance. My first time using this gem, my goal was just to create a simple bar chart with dummy data that would successfully save to my filesystem. So I added an array of a few integers as the data input for the chart:

{% codeblock lang:ruby %}
data_array = [1, 4, 3, 5, 9]

bar_chart = Gchart.new(
            :type => 'bar',
            :size => '400x400', 
            :bar_colors => "000000",
            :title => "My Title",
            :bg => 'EFEFEF',
            :legend => 'first data set label',
            :data => data_array,
            :filename => 'images/bar_chart.png',
            )

bar_chart.file
{% endcodeblock %}

Running the above code in my terminal (still with "require 'gchart'" at the top) produced this:

<img src="/images/post_images/bar_chart_1.png"></img>

It gets the job done, but its not too exciting for a few reasons. First, there aren't any y-axis labels; I have no sense of scale or measurement. Second, there are no labels on my data bars. Third, only one set of data is displayed; what if I wanted to compare two related datasets next to each other? In order to address these issues, I passed a few more initial values to the Gchart instantiation:

{% codeblock lang:ruby %}
data_array_1 = [1, 4, 3, 5, 9]
data_array_2 = [4, 2, 10, 4, 7]

bar_chart = Gchart.new(
            :type => 'bar',
            :size => '600x400', 
            :bar_colors => ['000000', '0088FF'],
            :title => "My Title",
            :bg => 'EFEFEF',
            :legend => ['first data set label', 'second data set label'],
            :data => [data_array_1, data_array_2],
            :filename => 'images/bar_chart.png',
            :stacked => false,
            :legend_position => 'bottom',
            :axis_with_labels => [['x'], ['y']],
            :max_value => 15,
            :min_value => 0,
            :axis_labels => [["A|B|C|D|E"]],
            )

bar_chart.file
{% endcodeblock %}

Specifically, I added six new attributes compared to the first chart. These new attributes include:
<ul>
  <li><strong>:stacked</strong> - indicates if the bars are to be stacked one on top the other (:stacked => 'true'; this is the default setting for bar charts) or if they should be side-by-side (:stacked => 'false')</li>
  <li><strong>:legend_position</strong> - indicates where the legend will be placed relative to the chart. Options are: 'bottom', 'bottom_vertical', 'top', 'top_vertical', 'right', and 'left'.</li>
  <li><strong>:axis_with_labels</strong> - indicates which axis you want to add labels to. I added the primary x-axis and y-axis (left and bottom), but I could have also added 'r' and 't' for 'right' and 'top' for the secondary vertical and horizontal axes, respectively</li>
  <li><strong>:max_value</strong> - indicates the maximum value for the x-axis. In tinkering with this attribute, it seems like this max value needs to be set in order to scale the data bars to the y-axis; otherwise they seem to get detached (i.e., you can change the y-axis scale without affecting the relative size of the bars).</li>
  <li><strong>:min_value</strong> - similar to the above, this attribute indicates the starting point of the bar chart.</li>
  <li><strong>:axis_labels</strong> - indicates the labels for the x-axis, separated by pipes ("|"). Note the formatting here - the labels in aggregate are a single string but are each delineated by pipes. If you want to replace this value with variable (e.g., if the data you want to chart may have dynamic x-axis labels), you may need to do some intermediate object transformation in order to get the appropriate syntax for Googlecharts. </li>
</ul>

I also added a second array of data at the top to see how my cluster, non-stacked bar chart looks. Note too that the HEX codes for the background ("bg") and bar colors do not need the leading "#" symbol as they do in CSS. After updating the code with additional attributes, this is my bar chart:

<img src="/images/post_images/bar_chart_2.png"></img>

One thing to note is that whenever you have multiple values for a single hash key, you need to wrap them in an array, otherwise the code won't properly execute.

This is the same data, except applied to a line chart (I just replaced :type => 'bar' with :type => 'line'):

<img src="/images/post_images/line_chart_1.png"></img>

And here it is again, except with just the first data set and applied to a pie chart:

<img src="/images/post_images/pie_chart_1.png"></img>

As you can see, its pretty easy to switch between graph/chart types. What's more important is to think through the purpose of the graphic and choose the appropriate type. For example, if the data you are presenting is along a continuum (e.g., stock prices, demographic distributions, etc.), line graphs may make the most sense. But if you're showing discrete datasets, you may want to use a bar chart.

<strong>3) Applying Object Orientation</strong>

Playing around with Googlecharts was fun, but ultimately, I wanted to use the gem in the context of a broader application. A typical use case may be wanting to display dynamic content that a user can request and format. I figured that this would be a good chance to extend a scraper for StubHub that I've been building on the side. Basically, this scraper goes through StubHub's NBA ticket listings and saves the information about each event (home/away team, date of game, venue, minimum price and number of tickets available, etc.) into a SQLite3 database. I wrote the scraper to be object-oriented, so I thought it would make sense to build in the functionality to auto-generate bar charts using Googlecharts.

For the sake of brevity, I won't post all of the code for the scraper and classes here, but you can find the <a href="https://github.com/eewang/tickets/" target="_blank">repository</a> on my <a href="https://github.com/eewang/" target="_blank">GitHub page</a>. The code below spells out the Team class, which houses the functionality to call a "search_team" class method and pass it a team name. This method will then create a bar chart dynamically using attributes of the team name that the user specifies. The method calls instance methods that perform actions like querying the database for price ("price for sql"), ticket ("tix_for_sql"), opposing team ("away_teams_for_sql") and event date ("date_for_sql") data. Additionally, I added in the ability for the class to search a hash of team colors to match the requested team against its primary and secondary team colors to format the chart dynamically. The underlying data map for team colors is housed as a constant in the "Baller" module, which is not reflected below. I scaled the y-axis based on the highest price in the price data array for the remaining home games. This way, I could make sure that I wouldn't get data that fell outside of my chart's range. Unfortunately, I wasn't able to disconnect the scale of the primary y-axis (left side) from the secondary y-axis (right side), so the "Number of Tickets" bars look a little squished.

{% codeblock lang:ruby %}
class Team
  extend Baller

  attr_accessor :name, :url

  @@color_hash = {}
  @@db = SQLite3::Database.new("tickets_3.db")

  def self.team_list
    Baller::TEAMS
  end

  def self.team_colors_list
    Array(Baller::COLORS.split("\n")).each do |item|
      @@color_hash[item.split(": ")[0].to_sym] ||= []
      @@color_hash[item.split(": ")[0].to_sym] << item.split(": ")[1].split(" ")[0].gsub("#", "")
      @@color_hash[item.split(": ")[0].to_sym] << item.split(": ")[1].split(" ")[-2].gsub("#", "")
    end
    @@color_hash
  end

  @@teams = []

  def initialize(name, url = "")
    @name = name
    @name_for_sql = @name.concat(" Tickets").to_s
    @url = url
    @away_teams = []
    @away_team_labels = []
    @@teams << [self.name, self.url]
  end

  def self.count_teams
    @@teams.size
  end

  def self.name_teams
    @@teams
  end

  def date_for_sql
    @@db.execute("SELECT event_date FROM events WHERE team_home == '#{@name_for_sql}'").flatten.collect { |date| date.split(",")[1].strip.split(" ")[0].split("/")[0..-2].join("/")}
  end

  def price_for_sql
    @@db.execute("SELECT min_price FROM events WHERE team_home == '#{@name_for_sql}'").flatten
  end

  def tix_for_sql
    @@db.execute("SELECT tix FROM events WHERE team_home == '#{@name_for_sql}'").flatten.collect do |item| 
        if item.is_a? Integer
          item / 100
        else 
          item.gsub(",", "").to_i / 100
        end
      end
  end

  def away_teams_for_sql
    @@db.execute("SELECT team_away FROM events WHERE team_home == '#{@name_for_sql}'").flatten
  end

  def x_labels(item)
    self.class.team_list.each do |combo|
      if combo[1] == item
        @away_teams << combo[0]
      end
    end
  end

  def x_labels_abbr
    away_teams_for_sql.each do |team|
      x_labels(team)
    end
    @away_teams
  end

  def away_team_x_labels_abbr
    @away_team_labels = x_labels_abbr.zip(date_for_sql).collect { |array| array.join(": ") }.join("|")
  end

  def team_color_primary
    Team.team_colors_list[@name_for_sql.split(" ")[0..-2].join(" ").to_sym][0]
  end

  def team_color_secondary
    Team.team_colors_list[@name_for_sql.split(" ")[0..-2].join(" ").to_sym][1]
  end

  def chart_filename
    @name.downcase.gsub(" ", "_").insert(0, "nba_charts/").concat(".png")
  end

  def self.search_team(team_name)
    nba_team = Team.new(team_name.split(" ").map { |word| word.capitalize }.join(" ") )
    bar_chart = Gchart.new(
                :type => 'bar',
                :size => '1000x300', 
                :encoding => 'extended',
                :bar_colors => [[nba_team.team_color_primary], [nba_team.team_color_secondary]],
                :title => "#{@name_for_sql}",
                :bg => 'FAFAFA',
                :legend => ['Minimum Price (LHS)', 'Number of Tickets (RHS)'],
                :legend_position => 'bottom',
                :data => [nba_team.price_for_sql, nba_team.tix_for_sql],
                :stacked => false,
                :axis_with_labels => [['x'], ['y'], ['r']],
                :axis_labels => [["#{nba_team.away_team_x_labels_abbr}"]],
                :bar_width_and_spacing => '25',
                :axis_range => [[nil], [25], [25], nil],
                :max_value => nba_team.price_for_sql.max + 25,
                :orientation => 'v',
                :filename => "#{nba_team.chart_filename}",
                )
    bar_chart.file
    puts "Creating file... Done"
  end

end
{% endcodeblock %}

When I call "Team.search_team(<"input team name">)", the program creates a file labeled with the appropriate team name in a sub-directory called "nba_charts". As you can see, Googlecharts can be used to create charts dynamically based on variable user input.

<strong>4) Adding User Interaction</strong>

In order to create charts quickly, I added a command line interface for the program. This code allows users to specify which team they want to see ticket data for and it will create bar charts using the gchart gem and save them to the local filesystem. I went ahead and created four bar charts for the Knicks, Lakers, Thunder and Nuggets. I wanted to create a chart for my beloved Chicago Bulls as well, but the red-and-white combination didn't show up very well on an off-white background, so I kept that chart out of this example. 

{% codeblock lang:ruby %}
response = ""
until response.downcase == "done"
  puts "What team do you want to search for? ('Done' to exit) "
  response = gets.chomp
  if response.downcase != "done"
    Team.search_team(response)
  else
    puts "Thanks for using my program!"
  end
end
{% endcodeblock %}

Here is the code when execute it in the terminal:

{% codeblock %}
$ ruby tickets.rb
What team do you want to search for? ('Done' to exit)
New York Knicks
Creating file... Done
What team do you want to search for? ('Done' to exit)
Los Angeles Lakers
Creating file... Done
What team do you want to search for? ('Done' to exit)
Oklahoma City Thunder
Creating file... Done
What team do you want to search for? ('Done' to exit)
Denver Nuggets
Creating file... Done
What team do you want to search for? ('Done' to exit)
Done
Thanks for using my program!
{% endcodeblock %}

And here are the bar charts that are created by the program:

<h6>New York Knicks Tickets - Remaining Home Games</h6>
<img src="/images/post_images/new_york_knicks_tickets.png"></img>
<h6>Los Angeles Lakers Tickets - Remaining Home Games</h6>
<img src="/images/post_images/los_angeles_lakers_tickets.png"></img>
<h6>Oklahoma City Thunder Tickets - Remaining Home Games</h6>
<img src="/images/post_images/oklahoma_city_thunder_tickets.png"></img>
<h6>Denver Nuggets - Remaining Home Games</h6>
<img src="/images/post_images/denver_nuggets_tickets.png"></img>

The left-hand y-axis refers to the price in dollars of the cheapest ticket, since StubHub's primary event page indicates where ticket prices start from (i.e., the lowest price), and the right-hand y-axis refers to the number of tickets remaining in hundreds. I was having difficulty disconnecting the primary and secondary y-axes from each other, so the scale for the remaining tickets is not ideal (the primary y-axis determined the scale).

<strong>5) Final Thoughts</strong>

As you can see, the Googlecharts gem can be useful for quickly linking up a program with a data visualization tool. While it doesn't appear to be the most powerful or most intuitive data graphing tool, its easy to install and the documentation is fairly straightforward, at least for basic functionality. I found it helpful to go through the code base for the Gchart class and try and follow the logic of each method whenever I got stuck. I found it to be a transparent gem in terms of figuring out what was going on "under the hood". 

That said, I found the syntax to be finicky, unnecessarily so, in my opinion. Also, it seemed like some of the more advanced functionality that I was looking for, that I was used to from using Excel many times, wasn't readily available. Next on my list of graphing tools to try out is <a href="http://www.highcharts.com/" target="_blank">High Charts</a>, a Javascript tool for interactive data graphics that also has an accompanying Ruby gem, <a href="http://github.com/michelson/lazy_high_charts/" target="_blank">Lazy High Charts</a>. Then, I can learn how to create awesome stuff like <a href="http://prafulla.net/interesting-contents/world-interesting-contents/us-presidential-election-2012-ohio-as-critical-state-spending-tv-adsinfographics/" target="_blank">this</a>.
