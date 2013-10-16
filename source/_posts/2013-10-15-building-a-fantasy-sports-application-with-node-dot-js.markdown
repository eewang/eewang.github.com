---
layout: post
title: "Setting up a Node app with Express and Mongo"
date: 2013-10-15 20:49
comments: true
categories: 
---

Lately, I've been interested in branching beyond the traditional Rails web stack. Node has always seemed interesting to me - not only is it fast and built for concurrency, the notion of using Javascript to structure the back-end seems like a natural next step to me given the language's ubiquity in web browsers (check out <a href="http://stackoverflow.com/questions/1884724/what-is-node-js#6782438" target="_blank">this great Stack Overflow post on why you might want to consider Node</a>). Last Friday was the second 100% day at the NYT since I started back in July, so I figured that it would be a good opportunity to explore the Node stack and get myself out of always approaching the web from a Ruby/Rails perspective.

<!--more-->

In thinking of projects that could help me learn Node, I was reminded of Avi's maxim that you're generally better off building your own project (and thus, solving your own problems) than building someone else's. And given that there are countless tutorials on how to build a blog using X, Y or Z framework, I don't think I could be engaged in learning Node just by building a blog that I don't really care about.

Those who know me know that I'm a big football fan. I played two years in high school as a strong safety, and I've played intramural football in NYC since I moved here in 2010 (with a recent hiatus to spend time focused on my career shift from finance to technology). Hailing from Chicago, I grew up watching the Bears - names like David Terrell, Anthony Thomas and Shane Matthews still bring a cringed look to my face as I recall the painful memories of so many wasted seasons (except for the 2006-2007 Super Bowl season - that was a fun ride). Thankfully, it looks like the addition of Marc Trestman, the Cutler-Marshall connection and the emergence of Alshon Jeffrey as a viable second receiver seems to be moving the Bears in the right direction offensively, even if the defense continues its rebuilding phase in the post-Urlacher era.

But I digress. Like many football fans, I'm involved in a few fantasy football leagues that require me to set lineups, add/drop players and attempt trades if I have any hope of getting to the playoffs. My general process is to check some player and matchup research on a weekly basis and make lineup changes accordingly, but that process can be a bit laborious, since I find myself performing this weekly ritual where I check player rankings, then cross-reference those against the available free agents and waiver wire players in my respective leagues. 

Conceptually, this should be done programmatically. The data provided by the player rankings should feed an API search query against my fantasy football league database, so that I can get a report that shows who is available for me to pick-up in my league among the players that are ranked higher than those I currently have on my team (thus indicating a potential up-market action for me to take). At a more advanced level, I could expand this program to take in multiple ranking sources or look beyond the free agent market to suggest trade possibilities, but that's all beyond the scope of the MVP, so to speak. I want to build an application that can take in data from my fantasy football leagues, mix that with research and analysis from an external site and build me an set of suggested actions that I can take to improve my team. 

The stack I'm exploring to do this is centered on the Express framework, built on Node, with MongoDB as the database and Mongoose as the ODM (Object Document Mapper). On the client-side, I'm using Jade as the templating engine, Stylus for CSS pre-processing and (hopefully) Backbone as the client-side framework. For now, I plan to just stick with normal browser Javascript plus jQuery, since my short-term goal is to get familiar with Node and MongoDB. Then, once I set the foundation, I'll try to move onto building a more UI-focused, client-side application.

*Node*

Node is a fast, scalable server-side architecture built using Javascript. I consider its Ruby equivalent to be the Rack framework. To install Node, just use Homebrew (brew install node). Also, you'll need to install <a href="https://npmjs.org/" target="_blank">npm</a> (Node Package Manager) to handle Node plugins. Npm is similar to Ruby Gems; it serves as a registry for libraries like Underscore, Mocha and Express.

*Express*

Express is a web framework built using Javascript on a Node server. Express is database-agnostic and is relatively lightweight, similar to Sinatra in the Ruby community. I chose Express primarily because of the fact that there are a number of online tutorials about how to build web applications using Express, and Express makes it pretty simple to fire up a Node server and add a few routes.

*Mongoose (and MongoDB)*

In this web stack, Mongoose serves as the application's interface to Mongo - it provides structure and access to the database. Mongoose is similar to ActiveRecord in that you can make database reads and writes, but its specifically designed to work with a document-store database like MongoDB. 

*Jade*

Jade is a HAML-like templating language that allows for logic structures like each loops and if statements to determine HTML structure. Jade syntax is incredibly terse and does not have closing tags and, much like HAML, indentation matters. 

*Stylus*

Stylus is SASS-like CSS preprocessor that is backwards-compatible with normal CSS. Mixins, variables and interpolation are all components of Stylus, which also emphasizes minimalist syntax (e.g., no curly braces).

There's a number of solid Express/Node/Mongoose tutorials online, so I won't reproduce what those blog posts have already done well. Here are a few of the best ones that I've come across:

<a href="http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/" target="_blank">Simple tutorial with Node, Express and Mongo</a><br>
<a href="http://howtonode.org/express-mongodb" target="_blank">Create a blog with Node/Express/Mongo</a><br>
<a href="http://blog.ijasoneverett.com/2013/03/a-sample-app-with-node-js-express-and-mongodb-part-1/" target="_blank">Sample Node/Express application</a>

Setting up the application was surprisingly simple, and the above tutorials do a great job of walking through the necessary steps (e.g., install node, install npm, set up Mongo, initiate an Express app). Taking my app beyond the basic tutorial, however, required me to set up a directory structure that could handle multiple models, controllers and routes. Given my familiarity with Rails, I tried to set up my application in a Rails-ian fashion, with an app folder that consists of sub-folders for my models, controllers and views. Here's a few links that I found useful when setting up my Express file structure:

<a href="http://madhums.me/2012/07/19/breaking-down-app-js-file-nodejs-express-mongoose/" target="_blank">Directory structure of a Node application</a><br>
<a href="http://rycole.com/2013/01/28/organizing-nodejs-express.html" target="_blank">Organizing a Node/Express app</a>

And so I set up my application like this:

* app
  * controllers
  * models
  * routes
  * views
* config
  * settings.js
* lib
* public
  * images
  * javascript
  * stylesheets
* app.js
* package.json

The folders are pretty self-explanatory, and the package.json file acts as a Gemfile of sorts in that when you run the 'npm install', npm will install the relevant libraries so that you can use them in your application.

To actually run the application, I can run the command 'node app.js', which executes the app.js file:

{% codeblock lang:js %}
// app.js
var express = require('express'),
    fs = require('fs'),
    http = require('http'),
    path = require('path'),
    mongo = require('mongodb'),
    mongoose = require('mongoose'),
    passport = require('passport'),
    util = require('util'),
    jsdom = require('jsdom'),
    YahooStrategy = require('passport-yahoo').Strategy; 

var db = mongoose.connection;
var app = express();

mongoose.connect('mongodb://localhost/fantasy_football');

// require('./settings')(app)
// all environments
app.configure(function(){
  app.set('port', process.env.PORT || 3000);
  app.set('views', __dirname + '/views');
  app.set('view engine', 'jade');
  app.use(express.favicon());
  app.use(express.logger('dev'));
  app.use(express.bodyParser());
  app.use(express.methodOverride());
  app.use(express.cookieParser('your secret here'));
  app.use(express.session());
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(path.join(__dirname, 'public')));
})

// development only
if ('development' == app.get('env')) {
  app.use(express.errorHandler());
}

// Models
var models_path = __dirname + '/app/models',
    model_files = fs.readdirSync(models_path);

model_files.forEach(function(file){
  require(models_path + '/' + file);
});

// Routes
var controllers = require('./app/controllers');
var routes_path = __dirname + '/app/routes',
    route_files = fs.readdirSync(routes_path);

route_files.forEach(function(file){
  require(routes_path + '/' + file)(app, controllers);
});

// Controllers
var controllers_path = __dirname + '/app/controllers',
    controller_files = fs.readdirSync(controllers_path);

controller_files.forEach(function(file){
  require(controllers_path + '/' + file);
})

http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});
{% endcodeblock %}

The file starts by loading the module dependencies for use in the application. Then, it instantiates the application, sets up the database connection via Mongoose, configures the app to use an array of Express middleware modules and libraries and points the application to port 3000. Finally, app.js requires the files in the app folder to the application so that the router, models and controllers can be linked together.

Express seems to be a pretty flexible framework in terms of how the file structure is set up. Like Sinatra and unlike Rails, Express doesn't have opinions as to how to organize your application and while there are countless ways to organize your application using Express, the framework comes ready to use out of the box if you just want to quickly set up some routes and get something on your screen. For me, I decided to establish an app structure that's both familiar to me and able to handle multiple resources in a RESTful manner before I got too deep in the business logic while relying on a harder-to-scale structure. Now that I have the app set up, The next step is to write out my models, routes and controllers, which I'll save for another blog post.
