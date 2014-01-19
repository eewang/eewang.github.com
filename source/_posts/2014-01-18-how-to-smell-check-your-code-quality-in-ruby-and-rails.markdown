---
layout: post
title: "What's that (code) smell? How to monitor code quality in Ruby and Rails"
date: 2014-01-18 09:24
comments: true
categories: 
---
I've found that as you progress in your code career, you start to discover more patterns in how to structure code and how to address common questions. In the same vein, you also learn what not to do and your sense for code smells improves. Your methods might get smaller, your logic better encapsulated and your classes more singularly responsible. Thinking more strategically about how to implement features with an eye to code maintainability, complexity and readability is a career-long endeavor. There's no single set of tools that will make you a better developer, but I've recently come across several gems that have helped me develop a better sense for code quality and development standards.

<!--more-->

As you become a better developer, it never hurts to have someone watching over your code, keeping you accountable to the best practices that you know in your heart but rarely exercise with your hands. In a perfect world, that would be a senior developer or other more seasoned mentor who can pair program with you and is highly invested in your success. If you happen to find yourself in that situation, that's fantastic. In any case, it doesn't hurt to have some automated tools that can quickly run through your code, find duplication, unused methods or non-descriptive variable names.

At the more involved end of the spectrum, there are hosted solutions for checking code quality like <a href="http://codeclimate.com" target="_blank">Code Climate</a>. But those can get pretty expensive and might be overkill for keeping your code up to snuff on a daily basis for small projects. On my team, we're exploring using tools like Code Climate, but me and my fellow developers have started integrating more code quality checks on our local branches so that any PRs we submit are as clean as can be.

Reek, rails_best_practices, Rubocop and metric_fu are 4 gems in particular that I've been using recently to sniff my code and tell me what's pleasant, what's pungent and what downright reeks. While each gem takes a slightly different approach to checking your code and displaying the results, I've found them all to be useful in finding syntactic sugar or challenging me to apply more sustainable code paradigms to solving problems in my applications.

The gems aim to notify you of where your code falls short of accepted best practices and suggest ways you can improve code quality. The phrase 'code quality' has always appeared a little slippery to me, since determining whether a code base is high-quality seems linked to the idiosyncracies of a particular application's purpose and is thus difficult to establish universal code quality standards. However, there are rules-of-thumb that are relevant for all applications when determining how "good" a code base is. These criteria range from high-level, design pattern implementation to low-level, syntactical choices. Properly applying concepts like well-encapsulated logic, division of responsibilities and descriptive method names can help improve the quality of an application, regardless of the application's purpose.

I decided to run these four gems against what was probably my most hacky, yet functional, piece of code that I've written - my Lollapalooza scraper. I wrote about this previously in [my blog here](http://eewang.github.io/blog/2013/03/21/how-to-hack-lollapalooza-and-still-not-get-tickets/), but basically I hacked together a web scraper back when I was at Flatiron that would check the Lollapalooza website and text me via Twilio whenever early bird tickets went on sale. The overarching goal was so that I could get discounted tickets without checking the site randomly but frequently over the course of a few days, since the festival organizers wouldn't say when early bird tickets would go on sale.

**Reek**

<a href="https://github.com/troessner/reek" target="_blank">Reek</a> is a gem that will comb through your application (or specific folder / file) and notify you of where you're not following best practices. Generally accepted practices like not using single letters for variable names or not having multiple nested conditional statements are caught pretty quickly. What's also great about Reek is that it also informs you of what coding practice you're violating with your smelly code. There's a slew of code patterns that Reek checks for, and I've found their [wiki page](https://github.com/troessner/reek/wiki) quite useful for better understanding antipatterns like Feature Envy, where a class references other objects more than it does itself, or Long Paramter List (pretty self-explanatory).

Here's what came out when I ran Reek against my Lollapalooza scraper.

{% codeblock lang:bash %}
♥ reek lolla.rb
lolla.rb -- 9 warnings:
  [16, 14]:Lolla declares the class variable @@account_sid (ClassVariable)
  [17, 15]:Lolla declares the class variable @@auth_token (ClassVariable)
  [54, 16]:Lolla declares the class variable @@client (ClassVariable)
  [22, 30, 34, 20]:Lolla declares the class variable @@doc (ClassVariable)
  [61, 22]:Lolla declares the class variable @@lolla_node (ClassVariable)
  [20, 18]:Lolla declares the class variable @@source (ClassVariable)
  [83, 86]:Lolla#self.check calls Lolla.index_check twice (DuplicateMethodCall)
  [80]:Lolla#self.check has approx 7 statements (TooManyStatements)
  [73, 73]:Lolla#self.node_contains_link? calls Lolla.node_names twice (DuplicateMethodCall)
{% endcodeblock %}

As you can see, I wasn't the best at following best practices back then (and, probably still now). I also wrote this scraper in a few hours one evening, so I'll give myself a break. Reek catches on that I used a lot of class variables in the file, which is generally not a great thing to do. The output also helpfully includes the line number of the infraction, so I can quickly find the source if the issue.

What's also great about Reek is that you can add <a href="https://github.com/troessner/reek/wiki/Configuration-Files" target="_blank">configuration files</a> to your application to fine-tune how Reek evaluates your code. Don't like that a long parameter list is defined as any method with greater than 3 parameters? You can modify that within the config file so that Reek hits that evaluation sweet spot - not too noisy for you to gloss over violations that it finds, but not too lenient so that it doesn't find any problems to begin with.

**Rails Best Practices**

Like Reek, you can run <a href="https://github.com/railsbp/rails_best_practices" target="_blank">rails_best_practices</a> simply by passing the command a folder or file (e.g., `rails_best_practices app/models` will check all your model files). The gem will then check your code against a configurable list of accepted best practices for Rails, like skinny controller / fat models or treatment of mass assignment. Unlike reek, RBP is focused specifically on Rails conventions, not necessarily general code best practices, even though there are considerable parallels between the two.

I ran RBP against my Lollapalooza scraper, and these were the results:

{% codeblock lang:bash %}
♥ rails_best_practices lolla.rb
Source Codes: |=====================================================================================================================================================|
/Users/eugenewang/projects/lollapalooza/lolla_check/app/models/lolla.rb:29 - remove unused methods (Lolla#document)
/Users/eugenewang/projects/lollapalooza/lolla_check/app/models/lolla.rb:80 - remove unused methods (Lolla#check)
/Users/eugenewang/projects/lollapalooza/lolla_check/app/models/lolla.rb:49 - remove trailing whitespace

Please go to http://rails-bestpractices.com to see more useful Rails Best Practices.

Found 3 warnings.
{% endcodeblock %}

As you can see, I would be well served to go back through my code and take out some unused methods. While unused methods don't necessarily hurt a code base, they add to the amount of code that needs to be maintained, and I've found that old code that is deprecated but still included in a code base replaces some of the value that version control systems like git provide. Rather than leaving in code that you think may or may not be used again in the future, best to keep in only what you're currently using and leave the rest to version control so you don't have to maintain code that you're not using.

**Rubocop**

Of these four code quality gems, I've found <a href="https://github.com/bbatsov/rubocop" target="_blank">Rubocop</a> to be both the most informative and specific yet also the most nitpicky. Whereas Reek will simply state what you have in your code with the implicit understanding that its not a good coding practice, Rubocop suggests specific changes to your code to clean it up. 

For example, Reek states that the `Lolla` class declares multiple class variables - generally a code smell that the class is not written well (see <a href="http://oreilly.com/ruby/excerpts/ruby-best-practices/worst-practices.html" target="_blank">this</a> for why). Rubocop also spots these class variables, yet goes the extra step to say that I should replace those class variables with an instance variable. Sometimes the help Reek provides in simply spotting improper use of code conventions is enough, but in other cases, having the specific suggestion of what to change can save time, especially when I'm don't have the muscle memory to know how to implement certain coding practices.

{% codeblock lang:bash %}
♥ rubocop lolla.rb
Inspecting 1 file
W

Offences:

lolla.rb:1:1: C: Missing utf-8 encoding comment.
require 'twilio-ruby'
^
lolla.rb:13:1: C: Extra blank line detected at body beginning.
lolla.rb:14:3: C: Replace class var @@account_sid with a class instance var.
  @@account_sid = '###############################'
  ^^^^^^^^^^^^^
lolla.rb:15:3: C: Replace class var @@auth_token with a class instance var.
  @@auth_token = ''###############################''
  ^^^^^^^^^^^^
lolla.rb:16:3: C: Replace class var @@client with a class instance var.
  @@client = Twilio::REST::Client.new @@account_sid, @@auth_token
  ^^^^^^^^
lolla.rb:18:3: C: Replace class var @@source with a class instance var.
  @@source = "http://www.lollapalooza.com"
  ^^^^^^^^
lolla.rb:18:14: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
  @@source = "http://www.lollapalooza.com"
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
lolla.rb:20:3: C: Replace class var @@doc with a class instance var.
  @@doc = Nokogiri::XML(open(@@source))
  ^^^^^
lolla.rb:22:3: C: Replace class var @@lolla_node with a class instance var.
  @@lolla_node = @@doc.children[1].children[3].children[1].children[1].children[7].children[0].children[0]
  ^^^^^^^^^^^^
lolla.rb:22:80: C: Line is too long. [106/79]
  @@lolla_node = @@doc.children[1].children[3].children[1].children[1].children[7].children[0].children[0]
   ^^^^^^^^^^^^^^^^^^^^^^^^^^^
lolla.rb:25:5: C: Use the new Ruby 1.9 hash syntax.
    :SOON => 764,
    ^^^^^^^^
lolla.rb:26:5: C: Use the new Ruby 1.9 hash syntax.
    :Souvenir => 743
    ^^^^^^^^^^^^
lolla.rb:49:23: C: Trailing whitespace detected.
  def self.index_check
                      ^
lolla.rb:54:5: W: Useless assignment to variable - message
    message = @@client.account.sms.messages.create(:body => msg,
    ^^^^^^^
lolla.rb:54:52: C: Use the new Ruby 1.9 hash syntax.
    message = @@client.account.sms.messages.create(:body => msg,
                                                   ^^^^^^^^
lolla.rb:55:9: C: Align the elements of a hash literal if they span more than one line.
        :to => "+16303791842",
        ^^^^^^^^^^^^^^^^^^^^^
lolla.rb:55:9: C: Use the new Ruby 1.9 hash syntax.
        :to => "+16303791842",
        ^^^^^^
lolla.rb:55:16: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
        :to => "+16303791842",
               ^^^^^^^^^^^^^^
lolla.rb:56:9: C: Align the elements of a hash literal if they span more than one line.
        :from => "+17082219589")
        ^^^^^^^^^^^^^^^^^^^^^^^
lolla.rb:56:9: C: Use the new Ruby 1.9 hash syntax.
        :from => "+17082219589")
        ^^^^^^^^
lolla.rb:56:18: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
        :from => "+17082219589")
                 ^^^^^^^^^^^^^^
lolla.rb:57:10: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
    puts "Message sent!"
         ^^^^^^^^^^^^^^^
lolla.rb:73:34: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
    if Lolla.node_names.include?("a") || Lolla.node_names.include?("href")
                                 ^^^
lolla.rb:73:68: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
    if Lolla.node_names.include?("a") || Lolla.node_names.include?("href")
                                                                   ^^^^^^
lolla.rb:80:3: C: Method has too many lines. [12/10]
  def self.check
  ^^^
lolla.rb:81:29: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
    if Lolla.index_changed?("SOON")
                            ^^^^^^
lolla.rb:83:80: C: Line is too long. [114/79]
      puts "Site changed! 'SOON' was at #{Lolla.index_check[:SOON]} before, now at #{Lolla.search_index("SOON")}."

lolla.rb:84:32: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
    elsif Lolla.index_changed?("Souvenir")
                               ^^^^^^^^^^
lolla.rb:85:80: C: Line is too long. [83/79]
      Lolla.notify("The placement of 'Souvenir' changed on the Lollapalooza site!")
                                                                               
lolla.rb:86:80: C: Line is too long. [122/79]
      puts "Site changed! 'SOON' was at #{Lolla.index_check[:Souvenir]} before, now at #{Lolla.search_index("Souvenir")}."
lolla.rb:88:20: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
      Lolla.notify("A link has been added to the node on the Lollapalooza site!")
                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
lolla.rb:88:80: C: Line is too long. [81/79]
      Lolla.notify("A link has been added to the node on the Lollapalooza site!")

lolla.rb:89:12: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
      puts "Site changed!"
           ^^^^^^^^^^^^^^^
lolla.rb:94:1: C: Extra blank line detected at body end.

1 file inspected, 34 offences detected
{% endcodeblock %}

Rubocop catches a lot of basic syntax infractions that Reek and rails_best_practices don't. Stuff like using double quotes for strings that do not interpolate Ruby variables, or using simple if/else statements when a one-line ternary operator would be sufficient. That's the nitpicky part of Rubocop, but its also a feature that I appreciate as it helps enforce a common style guide for projects with multiple collaborators, each with their own code style. One of my pet peeves is when there's no consistency in how many spaces are used for indentation (2!), how to add comments in code or whether to use the hash-rocket syntax for hash key-value pairs or the Ruby 1.9, JSON-like syntax. At the end of the day, these syntactical inconsistencies are less mission-critical than poorly implemented features, but getting the simple stuff right helps ensure a baseline level of consistency within development teams.

**metric_fu**

Of these four tools, I've integrated <a href="https://github.com/jscruggs/metric_fu" target="_blank">metric_fu</a> the least into my coding process. Running metric_fu against your codebase will open up a webpage with a variety of metrics and measurements of code quality. Metric_fu acts as a web interface around other code quality tools - including reek and rails_best_practices. I've found it less useful, though, than the other three because its harder to translate the resulting metrics into actual changes to make to your codebase. I do like that it seems comprehensive and provides a quick way to run an array of checks on your code, but I prefer the in-terminal results that the other three gems provide to an interactive web browser.

{% codeblock lang:bash %}
♥ metric_fu .
*****churn metric not activated, cannot load such file -- churn/churn_calculator
******* STARTING METRIC cane
******* ENDING METRIC cane
******* STARTING METRIC flay
******* ENDING METRIC flay
******* STARTING METRIC flog
******* ENDING METRIC flog
******* STARTING METRIC stats
******* ENDING METRIC stats
******* STARTING METRIC saikuro
******* ENDING METRIC saikuro
******* STARTING METRIC reek
******* ENDING METRIC reek
******* STARTING METRIC roodi
******* ENDING METRIC roodi
******* STARTING METRIC rails_best_practices
******* ENDING METRIC rails_best_practices
******* STARTING METRIC hotspots
******* ENDING METRIC hotspots
******* SAVING REPORTS
******* GENERATING GRAPHS
*****Generating graphs
*****Generating graphs for tmp/metric_fu/_data/20140119.yml
all done
{% endcodeblock %}

One thing these tools all have in common is that they count how many code quality infractions you commit. They vary in their specificity and the standards to which your code is compared, but having that number of 'X' number of warnings / offenses in your face is very helpful. It provides a tangible measurement of how you can improve your code; write better code and you'll have fewer warnings and offenses. That said, its important not to be a slave to Rubocop or adopt RDD (Reek Driven Development), since seeking to maximize only code quality (by minimizing warnings or offenses) is like teaching to the test. There may be cases when Reek yells at you for having Feature Envy in your code base, but who knows, there may be a specific reason for that, or refactoring your code extensively just to placate Reek would simply be infeasible.

At the end of the day, these gems help keep you accountable as you work on a feature or bug fix. They should never drive your development - rather, they should supplement your development and even if you don't take Rubocop's suggestions, you at least become more aware after using these tools of code best practices that you can start building muscle memory to implement.