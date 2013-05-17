---
layout: post
title: "Building an Admin Interface"
date: 2013-05-13 21:58
comments: true
categories: 
published: false
---

A classmate and I recently had the opportunity to work with Warby Parker to create an admin interface for Instagram data related to Warby. The basic service we were trying to provide was to enable Warby to better engage with people in Instagram and analyze the resulting data. People love Warby Parker on Instagram (think self portraits of people trying to decide which frame to buy), and we were tasked with building a tool to help collect and interpret that data in real time.

<strong>Using a front-end framework like AngularJS</strong>

<strong>Building an object-oriented Instagram application</strong>

<strong>Applying Active Admin</strong>

<strong>Regular expressions</strong>

One challenge we faced was figuring out how to cut out the noise that emerges when doing regular expression text searches. We were fortunate that Warby Parker is a pretty unique brand, and many of its frames are named after literary characters and have names like "Ainsworth" that aren't commonly used our everday vernacular. Nevertheless, there were some instances where we had to come up with more creative ways to exclude syntactic references to "warby" but not semantic references to Warby Parker. For example, there is a ranch in Montana called the Warby Range, and we inadvertantly pulled in photos tagged with #warbyrange in our Instagram calls that have nothing to do with Warby Parker. Similarly, there is a clothing line called War By Ticarmo (i.e. #warbyticarmo) that also presented some difficulties. I couldn't think of a clean way of filtering out all of these semantic irregularities programmatically (e.g., #warbyrange is not outside the realm of possible Warby Parker references), so we just included an array of names to exclude when conducting our search. Even then, doing it this way opens up the possibility that if a #warbyrange hashtag is introduced that actually does relate to Warby Parker at some point, we would have to revisit our filtering system.

<strong>Dealing with the Instagram API</strong>

A big consideration for Warby was to collect photos that mentioned warbyparker (i.e., "@warbyparker") but did not use a #warbyparker hashtag. As of today, Instagram does not have a public API endpoint that allows users to collect @mentions. 