---
layout: post
title: "How To Build A Simple News Reader"
date: 2013-04-25 16:58
comments: true
categories: 
---

A Flatiron TA (Blake Johnson) recently introduced us to the 'say' command in the Mac terminal. The command does exactly what you think it would - if you send in a string after the command, your computer will talk back to you! With this new-found coder superpower, I thought it would be interesting to try and build a simple news reading application. I've pasted the code below after the fold, or you can find my code <a href="https://github.com/eewang/automated-news-reader" target="_blank">here on Github</a>.

<!--more-->

I used to read the news constantly, checking my Google Reader feed or the New York Times several times an hour. Since starting at Flatiron, its been difficult for me to keep up with my regular news consumption. I try to read a number of newspapers and periodicals throughout the week, including the Economist, Atlantic, NY Mag and the NYT. With my deep-end dive into coding, however, traditional news reading has been difficult, so I've been trying to find new ways to get my news. For example, I started to get back into podcasts, which has been great as a way not only to get my news, but to take a break from constantly looking at screens.

I wanted to build a simple script that could scrape the New York Times front page and read the news back to me. As I thought about the problem, I decided to create two classes to represent the page that I wanted to scrape and the actual story itself. The code itself is pretty simple; it uses Nokogiri to open up the NYTimes front page and extracts the stories using a the "story" class css selector. Using Nokogiri to scrape a webpage converts the HTML into Nokogiri nodes, to which methods like 'text' or 'value' can be used to get information about the specific Nokogiri node.

The "front_page_stories" instance method in the Page class extracts all the stories from the front page. Then, the "create_stories" method goes through each story and creates a Story instance, on which it calls various methods to get the headline, byline and lede graph. 

To get the full text of the article, I extracted the link to the full story from the front page story node and passed that link into the Nokogiri HTML parser. Each story page includes the full body text in a paragraph tag with an "itemprop = articleBody" attribute, which makes it easy to access the body text of a given article. I then save each paragraph as a separate element in an array, which can then be iterated over for the terminal 'say' command to read.

One challenge that I had to address was the fact that the NYTimes uses different headline tags for the primary, secondary, tertiary, etc. stories. So what I did was write a Story instance method to check which headline tag was not empty for a given story. The script then takes the text value of the non-empty headline tag and applies it to the story headline attribute.

{% codeblock lang:ruby %}
require 'net/http'
require 'open-uri'
require 'nokogiri'
require 'pry'

class Page

  attr_accessor :url, :bylines, :stories

  def initialize(url)
    @url = url
    @stories = []
  end

  def document
    Nokogiri::HTML(open(@url))
  end

  def front_page_stories
    self.document.css('.story')
  end

  def create_stories
    self.front_page_stories.each do |story|
      if story.css('h6.byline').text != ""
        s = Story.new
        s.get_headline(story)
        s.get_byline(story)
        s.get_link(story)
        s.get_lede_graph(story)
        @stories << s
      end
    end
  end

  def read_stories
    self.create_stories
    @stories.each do |story|
      `say -v "#{story.voice}" "Story #{@stories.index(story)+1} of #{@stories.size}"`
      story.speak_story
    end
  end

end

class Story
  attr_accessor :headline, :byline, :lede_graph, :link, :voice

  VOICES = [
    "Alex",
    "Bruce",
    "Fred",
    "Junior",
    "Ralph",
    "Agnes",
    "Kathy",
    "Princess",
    "Vicki",
    "Victoria"
  ]

  def initialize
    @doc = ""
    @body_paragraphs = []
    @voice = VOICES[0] # .shuffle[0]
  end

  def doc
    @doc = Nokogiri::HTML(open(@link))
  end

  def get_link(story)
    self.link = story.css('a').attribute("href").value
  end

  def get_byline(story)
    self.byline = story.css('h6.byline').text.strip
  end

  def get_lede_graph(story)
    self.lede_graph = story.css('p').text.strip
  end

  def get_headline(story)
    attrs = ['h2', 'h3', 'h5']
    attrs.each do |headline|
      if story.css(headline).text != ""
        self.headline ||= story.css(headline).text.strip
      end
    end
  end

  def full_text
    self.doc
    @doc.css("p[itemprop=articleBody]").each do |p|
      @body_paragraphs << p.text.strip
    end
    @body_paragraphs
  end

  def speak_story
    puts "#{self.byline}"
    `say -v "#{self.voice}" "#{self.byline}"`
    puts "#{self.headline}"
    `say -v "#{self.voice}" "#{self.headline}"`
    puts "#{self.lede_graph}"
    `say -v "#{self.voice}" "#{self.lede_graph}"`
    self.full_text.each do |p|
      puts "#{p}"
      `say -v "#{self.voice}" "#{p}"`
    end
  end

end

news = Page.new("http://www.nytimes.com")
news.read_stories
{% endcodeblock %}

Now, I can theoretically wake up in the morning, fire up my terminal, run this script and get ready for the day as my computer reads the news to me. In practice, the computer voice isn't all that sexy and it inaccurately pronounces some names or other proper nouns and generally has a lumpy, awkward quality to it. I'm sure there are programs out there that you can download to add custom voices to the computer that are better than the standard voices. Another feature I'd like to add would be a scrape for Twitter to add a news feed feature. Also, I'd like to add a cron job feature with the Twilio API to notify me when the NYTimes adds a banner breaking news headline to the site. I know that the NYT sends out e-mail alerts for breaking news already, but it never hurts to be the first to know! 