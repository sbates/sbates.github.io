---
published: false
layout: post
title: "Attributes Origami"
date: 2012-07-28 23:00
comments: true
categories:
---

This post has been percolating in my brain for a long time. I mean like, long. Ever since I [read a snippet](http://wiki.opscode.com/display/chef/Cookbook+Style-Guide+Outline) from last year's Chef Summit discussing best practices regarding attributes, I've wanted to write something. But what I've got to say keeps changing.

This post assumes some basic familiarity with Chef and node attributes. I assume that most people are going to be bored with the subject matter unless you care about nerdy details or why strings are the official preferred notation. You can get the [short version](http://wiki.opscode.com/display/chef/Cookbook+Attributes#CookbookAttributes-Notation) on the Chef Wiki Cookbook Attributes page. I have a longer discussion here that focuses on my discovery process and evolution of thought.


#### A Quick Attribute Notation Overview
When I first started working with Chef, the publicly available cookbooks and wiki documentation were written in different styles.

  * Some used **symbols**: node[:some][:symbol]
  * some used **strings**: node['some']['string']
  * some used **method_missing**: node.dot.notation

I usually refer to this last as "dot notation," mostly because I had no idea what else to call it at first. I still call it that as it's an intuitive reference for someone who doesn't know much Ruby. I read up on [method_missing](http://ruby-doc.org/docs/ProgrammingRuby/html/ref_c_object.html#Object.method_missing) in prep for this blog post, but it was a new term and concept to me.

#### O dot notation, how do I love thee?
Getting back to it, I *lurved* my dot notation. It was easy to type. It was easy to read. It was harder to make typos. I am the queen of typos. Anything that helps me make fewer is my favorite friend. Then Chef Summit happened and recaps happened and I was like:

<img src="images/yutakeawaymaidots.jpg" float: left />

At first I was outraged that they were *oppressing* my dot notation and I was terrified they would deprecate it, which would make me sad. For a long time after that, I was just mad and still loved my dot notation and resolved to just keep using it anyway. This was the first time I had the urge to blog about it. I was planning to tell everyone to piss off. I loved my dot notation and I didn't want it taken away.

<img src="images/nocanheerz.jpg" float: right />

I mean, yeah, dot notation didn't always work and I probably never understood why. I was like this when I first learned chef:

<img src="images/babylol.jpg" />

