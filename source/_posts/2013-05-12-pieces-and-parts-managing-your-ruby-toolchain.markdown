---
layout: post
title: "Pieces and Parts: Managing Your Ruby Toolchain (part 1)"
date: 2013-05-12 13:12
comments: true
categories:  [ruby, rvm, rbenv, toolchains, scripting]
---
I recently received an email from someone who attended my [ChefConf talk][talk] and asked me to expand on a few things. As I started to answer the email, I could see it was going to turn into a dissertation, so I migrated it over into a blog post.  As I started to write the blog post, it seemed to grow into a book, so now I've decided to break it down into multiple posts.  In this post I'll try to summarize the question and situational environment and cover the easiest answers first: scripting languages and RVM.  


**The Question**  
Given the dynamic nature of Ruby tooling and the uncertainty of internet connections, and given that I work in the Enterprise, how should I design my supporting infrastructure and should I make Ruby my default scripting language?

**The Situation**  
Situated in the Enterprise, working with Red Hat/CentOS Enterprise Linux(EL). 

**Concerns**    
* Rapidly changing versions of gems and software on the internet  
* A constant need to download and compile Ruby (also compilers in production)  
* The dynamic nature of downloading an RVM script from the internet on every install  
* No connection to the internet (I'm not sure if this was a concern of his, but I'm betting it's one of yours)  

Let's look at our major parts, shall we? In this set of posts we'll cover   
* [Scripting Languages](#script)   
* [RVM](#rvm)  
* [Package Repositories]    
* [Package Versions]     
* [Ruby Management]       
* [Rubygems]    


<a id="script">**Scripting Languages**</a>  
The question was, should we go all in on Ruby as a scripting language? My answer to that is, unless everyone loves Ruby, that's probably an unnecessary hardship to impose while also trying to adopt Chef.  Generally you find some combination of Shell, Python, Perl or Ruby on most enterprise server systems. I think you should script in the language that works best for you, where "works best" is a combination of what you're comfortable with and what will best suit your immediate need. 

Honestly I think we'll never move entirely away from Shell scripting and that's ok. It's easy to learn and suits functional tasks like cron jobs and other server-specific work. Do I think you should have a script suite in 4 different interpreted languages? Well, no. If it were me, I'd pick Shell and Ruby for my options. But there's no reason it can't be Shell and Python or any other combo.

Me? I love Ruby and have an irrational dislike of Perl that stems from my early days in ops dealing with travesties of unreadable scripts. So I like Ruby first and Shell second, Python third and Perl never.  But it's hard enough learning a new tool like Chef. Don't impose artificial constraints on your team.

Also, Your devs are probably already programming in Java or C or whatever. Over time most devs pick up a smattering of shell. If you force Ruby on them too it's likely to breed resentment.

<a id="rvm">**RVM**</a>   
While I'm not an expert on [RVM][rvmio] and other Ruby managers, I have heard that it's not meant to be a server tool.  I do use RVM for workstation development and I've used [Rbenv][] as well. I don't mind compiling Ruby from scratch when installing it locally although I will love it when RVM gets precompiled rubies for OSX.  

##### A Slight Digression on Workstation Automation
I've written workstation automation using Fletcher Nichol's [chef-rvm cookbook][chefrvm].  A frustrating problem I have run into is that RVM undergoes frequent development and at first I had people coming to me because the workstation toolchain install wouldn't work and it was almost always because the RVM version had changed and something was now wonky in the settings or install chain. So I started pinning the RVM version to install with occasional revisits to bump the version when I could.  
 
If you want to look at workstation automation for OSX 10.8 try a couple of these repos:

* [Sprout-wrap][] - This used to be Pivotal Workstation and is now basically an instruction page for getting going with Soloist 
* [SoloWizard][] - a browser based checklist that will assemble a chef solo provisioner for your workstation.
* Joshua Timberman's workstation-chef-repo and mac_os_x-cookbook for his personal collection of workstation automation
* [Puppet-RVM][] - Puppet module for installing and managing RVM
* [Boxen][] - A tool for automating OSX with Puppet. Links to a blog post with details.

I have written workstation automation but I don't maintain it these days and I'll probably try Soloist when I get my next new Mac.
	

**TL;DR**:  
* Use RVM on workstations not servers    
* If you're automating for many people over time, pin your RVM version  

**Wrap Up**
To keep the blog post readable, this is all I was to discuss in this first post, but I'm about to publish Part 2 as well: a rant about package repos. I'm extremely interested in feedback on this series as I was only half joking about writing a book, though it would likely be an online doc of some kind. This type of discussion evolves too quickly to commit to paper.

[howtosearch]: http://www.google.com/search?q=how+to+install+ruby+development+tools
[chefrvm]: http://github.com/fnichol/chef-rvm
[rbenv]: http://github.com/sstephenson/rbenv/wiki
[sprout-wrap]: http://github.com/pivotal-sprout/sprout-wrap
[solowizard]: http://www.solowizard.com
[talk]: http://www.youtube.com/watch?v=pHmU0aNkENc&list=PLrmstJpucjzXNMLcI5X-EjirpDd-SITd3&index=28
[rvmio]: http://rvm.io
[spec]: http://github.com/imeyer/ruby-1.9.3-rpm
[aptruby]: http://packages.ubuntu.com/quantal/ruby1.9.3
[puppet-rvm]: http://github.com/blt04/puppet-rvm
[boxen]: http://boxen.github.com/
[eol]: http://www.ruby-lang.org/en/news/2011/10/06/plans-for-1-8-7/
[fpm]: http://github.com/jordansissel/fpm/wiki
