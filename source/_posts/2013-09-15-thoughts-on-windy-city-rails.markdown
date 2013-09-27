---
layout: post
title: "Thoughts on Windy City Rails, Day 2"
date: 2013-09-15 10:46
comments: true
categories: 
published: false
---


<!--more-->

#### "Git and Github Secrets" by <a href="https://twitter.com/holman" target="_blank">Zach Holman</a>

Zach Holman of Github gave an insightful and pretty funny talk about some of the useful yet obscure features of Git and Github.

<em>Git</em>

Tired of getting all those pesky default merge commits that aren't that informative? There's a git command that can either (i) avoid the merge commit altogether, or (ii) add a commit message to the merge commit directly

{% codeblock lang:bash %}
git merge -m "adding a message to my merge commit!"
{% endcodeblock %}

{% codeblock lang:bash %}
git merge --no-commit
{% endcodeblock %}

{% codeblock lang:bash %}
git stripspace # strips trailing whitespace, collapses new lines
{% endcodeblock %}

{% codeblock lang:bash %}
git diff --check # checks for whitespace
{% endcodeblock %}

{% codeblock lang:bash %}
git diff --cached # shows diffs with staged but uncommitted changes
{% endcodeblock %}

<em>Github</em>

Press 'y' on Github to convert a page into the canonical URL (even if file is deleted)

':<emoji>:' will open up the emoji editor to allow you to add

'#' to get a search for issues in a repository

File finder - type 't' you can find in the project for search

#### "Seeing the Big Picture: Quick and Dirty Data Visualization with Ruby " by <a href="https://twitter.com/" target="_blank">Aja Hammerly</a>

Ryan Bates' graph gem

Use highcharts for quick and easy JS-based graphing

#### "Minitest & Rails" by <a href="https://twitter.com/holman" target="_blank">Ryan Davis</a>

Minitest was intended to be small, fast and portable. Although its added a number of features since its initial release, it remains a pretty lightweight testing framework that comes built in with Rails 4.
