---
layout: post
title: "Linking Code and Finance"
date: 2014-01-09 07:52
comments: true
categories: 
---

Working in technology as a developer has parallels to working in asset management as a lawyer. I'm not a lawyer, mind you, but in my previous job I worked with several lawyers who had practiced at some of the leading firms in New York. During my 2.5 year stint in the hedge fund group at a large alternative asset manager, I had the opportunity to work at the nexus of institutional investors and hedge funds, where I helped build fund platforms and coordinate investments into underlying hedge funds. 

From the outside, this sounds like quite a different job than the one I currently hold as a developer. But I've found that there are more parallels at a deeper level than I first thought. Both developers and lawyers construct complex, knowledge-intensive infrastructure, the former calling the practice "software development" and the latter calling what they do "fund structuring". 

For those unfamiliar with hedge funds, they are often established as a set of interlinked investment companies domiciled in places like Delaware (if you're an American taxable, or "onshore" investor) or the Cayman Islands / British Virgin Islands / Malta (if you're a foreign or American non-taxable investor, aka "offshore"). In the popular media, hedge funds are generically labeled such that it appears like a single entity both takes in money from investors and disburses it out in the form of equity or fixed income investments. The reality, like in most spheres of life, is much more complicated. In my experience, rare are the times where the fund that actually invests the money and the fund that accepts money from investors the same. A more common fund structure is a "master-feeder" structure, where a set of feeder funds (at least two - one for onshore and one for offshore investors) will collect money from investors, then in turn invest that money into a master fund, which will actually hold the investments. Each feeder fund will own a share of the assets of the underlying master fund in proportion to the money that has been invested in them.

This is the most basic of fund structures. From there, the level of complexity only rises. I've seen an 

In software,

One difference between the two practices is the whimsical approach software takes to naming its tools compared to asset management. Hedge funds often have boring, descriptive names like or XYZ Offshore Master Fund Ltd. Software tools, on the other hand, have fun, metaphorical names like [Bees with Machine Guns](https://github.com/newsapps/beeswithmachineguns), which programmatically sets up EC2 instances (i.e., 'bees') to send large amounts of traffic to your web server (i.e., 'machine guns') in order to test an application's load handling.