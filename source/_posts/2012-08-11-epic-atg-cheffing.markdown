---
layout: post
title: "Epic ATG Cheffing"
date: 2012-08-11 08:47
comments: true
categories: [Chef, automation, ATG, JBoss]
---

I just want to take a moment to point you at an epic new Chef cookbook undertaking. John Larsen has been working on a [cookbook to configure a full ATG stack with JBoss](http://community.opscode.com/cookbooks/atg) and has finished a first version. This is something I always wanted to work on but never got around to, and so I'm being extra appreciative of someone who did take the time to get it working.
<!-- more -->
Anyone who works on ATG projects knows that installing and configuring the
first instances of ATG is hella complex and nearly impossible to do even with
documentation.  With all the options, variables and components, it mostly takes repetition and mistakes to get an initial ATG install correct.

That's **not** what this cookbook is about. You still have to know how to get ATG
right the first time or have someone on your team who does. This cookbook is
not going to make you an ATG expert, but it will relieve the tedium of
managing your configurations. It will also speed up adding a new instance. The more epic your configs, the more awesome this
cookbook will be for you.

The cookbook contains a modified version of [Bryan Berry's JBoss Cookbook](http://community.opscode.com/cookbooks/jboss), specialized for ATG deployment and does the following cool things:

* Configures single or multiple instances of JBoss on a server
* Provides flexibility to determine where you will setup your lock management,
global, process and workflow managers.
* Configures port management for ATG legacy ports
* Has templates for all the major ATG property files, like
Configuration.properties, Lock Manager, scenario manager and  workflow process manager
* Has templates for major jboss/atg configs like atg-ds.xml, bindings, log4j,
run.conf, etc
* Definitions for configuration sets (lock, staging, store...)

The ATG cookbook is well thought out, fairly large and handles complex scenarios. If you are in
the position of having to manage or build an ATG environment, I definitely
encourage you to check it out.  With something like this, it would even be
possible to stealth manage your configs with Chef-solo early on if you're looking to prove out
configuration management for ATG-JBoss. In my experience, there's something about
shops running this combo that makes them, in general, resistant to
configuration management. Possibly it's the breakneck speed at which I see them
running (often with scissors).
