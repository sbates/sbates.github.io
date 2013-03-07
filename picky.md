I was thinking about styling tonight and all the different things people dislike. I've been experimenting with what cookbook workflow would look like if I had a go at an "application wrapper" styled cookbook("http://devopsanywhere.blogspot.com/2012/11/how-to-write-reusable-chef-cookbooks.html") 

I am not always the most analytical person around. When I'm coding in new territory, I tend to go with what works first, what's pretty second and what's stylistically correct third, although the last two are usually coupled for me. So I'd never really thought about some of the cookbook styling choices I make. I have a few duh style rules that are common sense rules, right? No bash blocks or execs unless there's no other way, use attributes not hardcoded values, keep data out of cookbooks, that sort of thing. But other style choices I tended to make on the fly although making an effort to stay consistant within a cookbook and possibly across a collection if possible. 

When I started with a new client in August, I was informed that the team didn't use "include_recipe" in cookbooks as they found it to be an antipattern that obscures code origin. I hadn't really thought about it before, but concluded that they were probably right and agreed to not use it. Even if I did disagree, I have bigger battles to fight. 

This was reinforced a few times with folks I spoke with in the organization and the community. Just yesterday I was chatting with Jeff Sussna about this topic and he brought up that he doesn't like documenting the need for external cookbooks in more than place, which, at the time, made sense to me.

Which brings us to the present day where I'm working on conceptualizing an application wrapper cookbook both for myself and as something to model to the rest of the org. When I first started stubbing the cookbook, I realized I would have to make heavy use of include_recipe to define workflow. I found this mentally hard after spending the last 4 months agreeing that we shouldn't do that.  I got over it though and have now littered my cookbook with includes all over the place (hyperbole). 

Tonight I was pondering include_recipe along with Brian Berry's post on Gangnam-Style cookbooking as well as the many threads in the community discussion forum around all the things people don't like stylistically speaking. This jarred a couple of thoughts loose.

First, if I let myself be swayed by everything someone dislikes in the community, I'm going to spend my life staring at a blank page, too paralyzed to write code.

Second, I've decided that I don't consider include_recipe to be an antipattern. It's a workflow indicator.  Let's think about all the places you can indicate a need for a cookbook.

**Metadata** - we can use depends, recommends, suggests; all of these are ways to tell Papa Chef that you need a cookbook in order to proceed.
**Recipes** - include_recipe can be used in a recipe to indicate that another recipe should be processed prior to continuing beyond this point. It is most often used at the top of the recipe to indidcate a pre-req, but it doesn't have to go there. My wrapper cookbook has an application that gets installed into Tomcat and then indicates at the end that a subsequent recipe should be called.
**Roles** - And of course, we can indicate workflow in a role run_list

I don't think these are mutually exclusive indicators. I think you need the declaration in all three places and I don't think it's violating DRY when you do. Here's why:

A role is not a cookbook. A cookbook is not a role. A role describes a collection of cookbooks that should run on a given node for that node to find Nirvana. I believe in explicit cookbook requirements in roles. I believe a "node role" would generally contain other roles. 

While a role prescribes the path to Nirvana, it is not directly coupled to a cookbook. You're looking at a node role flow, not a cookbook flow. The role orchestrates the entire node but that cookbook will not always belong to that role. Even if you're a role minimilist, I think it's important to strive for a level of explicit in your role run list so that other eyes know wtf when it comes to the prescription. 

In order to explicitly tell other users and Chef what's needed for a cookbook to function, you have to tell someone somewhere something about the cookbook requirements. Yes you could write them in a wiki or in the readme, but it's much more powerful to use metadata management. This makes the cookbook self documenting in a way that just writing it down in a readme does not. One of the best things I've heard all year that I think of nearly once a day is when Jon Cowie was presenting at Velocity on Knife-spork and said, "We thought about writing all these rules down in a wiki, but we decided to automate it instead." 

Consider a cookbook collection of java, solr, zookeeper, tomcat, somesolrapp, and a large "base" collection of cookbooks containing provisioning-type software. 

I would probably break this down into 3 node roles:
1. A Tomcat role with run_list "role[base]", "recipe[java]", "recipe[tomcat]"
1. A Solr Node with run_list of "role[tomcat]", "recipe[solr]"
1. A Zookeeper role with run_list of "role[base]", "recipe[zookeeper]"

