---
layout: post
title: "Ruby Toolchaining Part 3: Walking the Package Talk"
date: 2013-05-24 03:16
comments: true
categories: [ruby, packages, fpm, gems, toolchains] 
---
This is the third post in a series about managing your Ruby Toolchain. Expect at least one more after this. You can also read:  
[Pieces and Parts: Managing Your Ruby ToolChain(part 1)][pt1]   
[Managing Your Ruby Toolchain Part 2: The Package Repo Rant][pt2]   

Why I rant about package repositories: Static software without a repository is the pebble that becomes an avalanche. So many undesirable behaviors stem from no one owning the package collection.  Read the rant if you want details.  This is a practical discussion, although I'm not telling you what to do. I'm just telling you what I've done and what I've seen over the course of my career.  There are many ways to solve this problem. Do what works for you.

This post grew so much that there is one more after it that specifically deals with some toolchaining tools and ideas as well as a few anecdotes on how I've dealt with some of these challenges in the past.

Today we're going to talk about:  
[Tarball Orphans vs Artifacts vs Inbetweeners](#tarballs)    
[The Internet](#internet)  
[An Approach to Repositories](#repos)    
[Packaging Tarballs](#packing)    
[Ruby Management](#ruby)     
[Ruby Toolchain Management](#toolchaining)    

## <a id="tarballs">Tarball Orphans</a>  
What do I mean when I rant about tarballs? Basically I mean that pile of source you downloaded from the internet, or Tomcat package, JBoss tarball, Ruby source, whatever.

What is exempt from my ranting 	wrath? Custom code that you might tar up instead of jar/ear/war.  Anything that changes often shouldn't be in our static package repository. Those are places to keep long term, install-once per server software.  

When I talk about tarballs, I mean tumbleweed off the internet.  When I refer to compiled code I'll use the word **artifact**.  What's an artifact? It might be a jar file you bundle with your deployed code. It might be a war file. It might be a tar of ruby code Or database artifacts.  Anything that's your stuff, we don't put that into package repositories.  We put it into artifact repositories like Artifactory and Nexus. People also use S3 storage to store and retrieve artifacts.

Some things may not be easily categorized. Newer products get frequent updates that may require frequent patches or deployments. For example, I worked with a team that implemented Basho's [Riak][] which they updated frequently and ended up engaged in a disagreement with Ops on how often they could update it. The ops team wanted to restrict it to quarterly and the devs wanted much more frequently. This sort of management requires a custom solution that may be package-based or not.

## <a id="internet">The Internet</a>  
If your infrastructure has free access to the internet, that's awesome. Many infrastructures have limited or no access to the internet.  Often the reasons for this seem silly.  But sometimes you can't fight city hall, or you can but you need to save your fights for the life or death kind.  Your servers don't need the internet to survive. Or they should not.  

Eventually you will find yourself needing stuff from the Internet. In order to work within restrictions, you can set up a [Bastion][] host or AWS VPC infrastructure that can call out to the internet in order to create private mirrors of any repositories you need.  You can follow the slightly more difficult path of setting up an internal server that contains your repos that you need to sync in a way that doesn't have direct access to the internet.

**Internet as Troublemaker**  
Internet access brings its own set of problems as well. If you aren't specific about package versions, with every server provision you could have slightly different versions of things installed.  This is a problem because it can cause erratic behavior across your infrastructure. If you have 1000 nodes built over the course of six months, each build wave can introduce incremental differences. When you control the packages in your internal repos, you exert a stabilizing effect, which is a good way to control your system without making it brittle.  

Even if sloppy package management doesn't actually cause issues, it will be a suspect in any intermittent, mysterious problems. Picture an outage conference bridge including execs. Now envision yourself explaining to them how this mismatch isn't a problem. Can you envision any explanation that doesn't make you sound like a dolt?

**Example**: A few years ago JBoss 5 shipped with a bug that was a major performance blocker but difficult to track down. It was later fixed in a minor patch release. Imagine having a JBoss package block that just says `package "jboss" {action :install}` and using an internet repo to install it. 6 months later you provision 100 servers from your trusty internet repo and over your peak season, the bug triggers but everything is weird because it's only a problem on some servers.  Imagine tracking this down. (Yes this is a flawed example b/c as far as I know, there's no internet repo with a JBoss rpm)

If you're a dev and you think this isn't a problem, try chatting with a friend in the sysadmin/ops area before you poo-poo. If you're a sysadmin and you think this isn't a big deal, may we never be responsible for the same infrastructure. 

**Be Conservative.** As a rule it's ok to use external signed key repositories for your base OS packages as those shouldn't change. Tread carefully even with patch/update servers to ensure homogenous installs and any packages acquired from other internet repos (like EPEL) should be installed with explicit versions.

**Adopt Orphans** Don't let Internet-sourced software roam around your infrastructure unsupervised. If you need a package from the internet, it should be easy enough to download and insert into your custom repository. There are several tools for managing repositories. For Enterprise Linux, [Cobbler][], the provisioning and infrastructure management tool has repository management and mirroring. My new favorite thing for this though is a tool called [Pulp][], an open source project by Red Hat.  If you prefer simplicity, you can just install a web proxy and use a command line tool like `createrepo` to manage your yum repos.

**Don't assume I can talk to the Internet.** When it comes to configuration management tools, I find it in bad taste to write a module or cookbook intended for the community with an Internet dependency.  If you have to write one, label the dependency in large font.

## <a id="repos">An Approach to Repos</a>

### OS Packages
If you're going to set up repositories, consider a collection like this list.  

**Base** - base EL install repo  - This is generally straight up ISO content  
**Patch** - repository containing patches  
**EPEL** - EL Extras repo mirror  
**Custom** - repository containing all the custom packages I need that I've either crafted myself or downloaded   

**Purity of Purpose:** Always keep your base and patch repos pure. Don't let people just dump random packages into a Base OS collection. You'll never know what was supposed to be there and what was added later.  

**Prioritization and Specificity:** If you have two versions of a package in different repos, either be specific when installing or configure a priority for each repo. 

### Rubygems 
This is where you get/put your gems. Gem is the package manager for Rubygems. You either need to connect to the internet to get all of these things or you need internal package repos/mirrors.  See this [blog post][rubygemsrepo] by David Moulton for how easy it is to set up a rubygems repo.

### Artifacts
This is where your deployment artifacts, jar, war, ear files should be contained; a tool where it's easy to retreive them programmatically. I have seen people use [Nexus][], [Artifactory][] and even S3 to store artifacts. If you're not a java shop, you may not need something this complex. My major experience is with Java shops.

### YMMV 
There are many ways to craft your infrastructure. Having package repositories is an advantage because they make it really obvious to everyone in your organization where to look for software and where they should put software they need.

The important thing to keep in mind here is that, especially if you're in the Enterprise, you aren't designing for yourself or just one team. This will balloon out to include dev teams using your tools. In order to deploy their app, the devs will test using VMs provisioned by your infrastructure and they should be taking advantage of your beautiful repos to deposit their extra software packages.  Your team may even be administering the VM infrastructure.  

A major goal to strive for is for all VMs and servers to be provisioned the same way in all environments, desktop to production. You make this easiest and more likely to happen when you make it easy to interact with software repositories.  If it's not easy, other teams will "figure something out" which will likely outrage you when you discover it.

## <a id="packing">Practical Tarball Management</a>
### Avoid Compiling From Source
Believe me when I say that you **do not** want to compile Ruby from scratch every time you build a server. The more automation achievements you unlock, the easier it becomes to use ephemeral VMs.  In Dev, people should be popping up and killing VMs all the time. If it takes 10 minutes to build a server, they won't be doing this, dev VMs will have [major cruft buildup] [awesome] and dismay will ensue when people realize they can't actually reproduce their dev VM.

### Turning a Tarball into a Package
How do you get packages for your repos when all you have is a pile of tarballs? If you're brilliant, write a package spec for it. If you're like the rest of us, you use [FPM][].  Even if you are in a situation where you can't have a package repo, you can at least create and store packages which will speed up installation and simplify implementation.

[FPM][]: Learn it, love it, live it.

### <a id="ruby">Ruby Management</a>
### Ruby Source
**1.8.7 is EOL**  
First, on the topic of existing packages, as [announced in October of 2011][eol], Ruby 1.8.7 is officially end of life and won't even get security updates after June 2013.  *Just say no to the supplied 1.8.7 packages* on your Enterprise Linux(EL) and the one that comes installed with OSX. No really, never touch it. If you're using it, start seriously considering how to move on.

**System Ruby**  
Whatever else you're doing, your servers will all want a system Ruby. It's possible to get along without this(see omnibus in subsequent sections), but especially in dev, people will be shocked and dismayed when there's no easily accessible Ruby.  So get you some Ruby, not from the ancient EL repos, but get the source tarball from [Ruby-Lang.org][rubylang]. 

Why do I tell you this obvious thing? We had the brilliant idea of switching out our compile-every-time system ruby install with just using the embedded Ruby that ships with Chef client. Never do this. The trouble is that your Ruby install is not just an executable, but a name space for installing gems and related software. This is is why Chef ships in an omnibus in the first place: to avoid dependency hell and version conflicts.  The other problem with this idea is that you are still lacking a system Ruby. You either have symlink to the Ruby executable or people have to know where to look for. So install a system Ruby independent of any other software.

Next make a Ruby RPM. RPMs are hard. Luckily [Ian Meyer][ian] is watching out for us and has a [defined, working spec][spec] from which to build an RPM. I've made this Ruby RPM for two different clients and it compiled without a hitch. 

Once you create an RPM, put it into your custom package repo so that you can yum install ruby-1.9.3 or install it via cookbook later.

I repeat this series of steps for the Rubygems tarball, except using [FPM][] to package it.  Then they go in my custom repo where the custom repo has a higher priority than my base repo and my recipe just has a package block to install Ruby and Rubygems. It takes about 30 seconds instead of the 5 minutes it takes to compile Ruby from scratch.

If you use Ubuntu, well, they are cooler than EL and have [Ruby-1.9.3 packages][aptruby].

## Where Do We Go From Here?
An important part of toolchaining is maintaining control of the supply chain.  The way we do that is by creating space for packages and actively managing that space. It should be freely accessible to both deposit and receive software. Don't manage things by locking people out, but by making it easy to use and providing guidelines for usage. Most people want to do the right thing. When you make the right thing easy to do, more people will do it.


[pt1]: http://blog.brattyredhead.com/blog/2013/05/12/pieces-and-parts-managing-your-ruby-toolchain
[pt2]: http://blog.brattyredhead.com/blog/2013/05/17/managing-your-ruby-toolchain-part-2-the-package-repo-rant
[srs]: http://knowyourmeme.com/memes/the-internet-is-serious-business
[omnibus]: http://github.com/opscode/omnibus-ruby
[utensils]: https://github.com/schisamo/omnibus-chef-utensils
[seth]: http://twitter.com/schisamo
[mygithub]: https://github.com/sbates/omnibus-chef-utensils
[cope]: http://en.wikipedia.org/wiki/Coping_%28psychology%29
[riak]: http://basho.com/
[bastion]: http://en.wikipedia.org/wiki/Bastion_host
[pulp]: http://pulpproject.org/
[cobbler]: https://github.com/cobbler/
[awesome]: http://workstuff.tumblr.com/post/50911984233/some-tips-on-getting-started-with-vagrant-and-chef
[fpm]: https://github.com/jordansissel/fpm
[nexus]: http://www.sonatype.org/nexus/
[artifactory]: http://www.jfrog.com/
[eol]: http://www.ruby-lang.org/en/news/2011/10/06/plans-for-1-8-7/
[rubylang]: http://www.ruby-lang.org/en/
[ian]: https://twitter.com/ianmeyer
[spec]: https://github.com/imeyer/ruby-1.9.3-rpm
[aptruby]: http://packages.ubuntu.com/quantal/ruby1.9.3
[rubygemsrepo]: http://davidmoulton.me/2011/02/07/create-a-private-gem-repository.html
[chefomni]: http://www.opscode.com/chef/install/

