---
layout: post
title: "MVC Architecture with Node and Mongo(ose)"
date: 2013-10-11 23:06
comments: true
categories: 
published: false
---


Here is my one model file:

{% codeblock lang:js %}
// ./app/models/player_rankings.js
var cheerio = require('cheerio'),
    request = require('request'),
    mongoose = require('mongoose'),
    _underscore = require('underscore'),
    Schema = mongoose.Schema;

var Rank = new Schema({
  scoring_system: {type: String, default: '', trim: true},
  best: {type: Number},
  worst: {type: Number},
  average: {type: Number},
  consensus: {type: Number},
  stdev: {type: Number},
  created_at: {type: Date, default: Date.now}
})

var PlayerRankingSchema = new Schema({
  name: {type: String, default: '', trim: true},
  team: {type: String, default: '', trim: true},
  position: {type: String, default: '', trim: true},
  ranks: [Rank],
  created_at: {type: Date, default: Date.now}
});

// PlayerRankingSchema.methods = {
  // instance methods go here
// }

PlayerRankingSchema.statics = {
  list: function(){
    var rankings = [];
    rankings = this.find({});
    return rankings;
  },

  scrape: function(pos, callback){
    var _ = require('underscore');
    var _self = this;
    var root_url = "http://www.fantasypros.com/nfl/rankings/";
    var url = root_url + pos + '.php';
    request(url, function(err, res, body){
      if (err) {
        throw err;
      } else {
        $ = cheerio.load(body);
        $('.table').find('tbody').last().find('tr').each(function(index){
          var body_array = _.map($(this.find('td')), function(value){
            return $(value).text();
          })
          var player_name = body_array[1].split(' ')[0] + ' ' + body_array[1].split(' ')[1];
          var team = _.map(body_array[1].split(' '), function(value) {
            if (value[0] == '(') { return value }
          });
          var team_name = _.compact(team)[0].replace('(', '').replace(')', '');
          var ranks = body_array.reverse().slice(0, 4);
          var player_attrs = {
            name: player_name,
            team: team_name,
            position: pos
          }
          // console.log(player_attrs);
          var rank_attrs = {
            scoring_system: 'standard',
            stdev: ranks[0],
            average: ranks[1],
            worst: ranks[2],
            best: ranks[3],
            consensus: index + 1
          }
          _self.findOne({name: player_name}, function(err, person){
            if (person.length == 0) {
              var player = new _self(player_attrs)
              player.ranks.push(rank_attrs);
              player.save();
            } else {
              person.team = team_name;
              person.position = pos;
              person.ranks.push(rank_attrs);
              person.save();
            }
          });

        });
      }

    });
    callback();
  }

}

mongoose.model('PlayerRanking', PlayerRankingSchema, 'player_rankings');
{% endcodeblock %}

The router: 

{% codeblock lang:js %}
// ./routes/index.js
module.exports = function(app, controllers) {
  app.get('/', controllers.index);
  app.get('/leagues', controllers.leagues);
  app.get('/players/:position', controllers.players);
  app.get('/scraper/:position', controllers.scraper);
  app.get('/search', controllers.search);
  // app.get('/auth/yahoo', passport.authenticate('yahoo'));
  // app.get('/auth/yahoo/return', passport.authenticate('yahoo', {failureRedirect: '/players'}),
  //   function(req, res){
  //     res.redirect('/leagues');
  //   }
  // );
}
{% endcodeblock %}

The controller:

{% codeblock lang:js %}
// ./app/controllers/index.js
var mongoose = require('mongoose'),
    PlayerRanking = mongoose.model('PlayerRanking');
    _ = require('underscore');

var dir = "../app/views";

exports.index = function(req, res){
  res.render(dir + '/index', { title: 'Express' });
};

exports.leagues = function(req, res){
  res.render('leagues/index', {title: 'League List'});
}

exports.players = function(req, res){
  var position = req.params.position;
  PlayerRanking.find({position: position}, function(err, players){
    res.render(dir + '/players/index', {title: 'Player List', players: players});
  });
}

exports.search = function(req, res){
  var search = req.query.player;
  if (!search) {
    res.render(dir + '/players/search');
  } else {
    PlayerRanking.findOne({name: search}, function(err, player){
      res.render(dir + '/players/show', {player: player});
    });
  }
}

exports.scraper = function(req, res){
  var position = req.params.position;
  PlayerRanking.scrape(position, function(){
    res.redirect('/players/' + position);
  });
}
{% endcodeblock %}