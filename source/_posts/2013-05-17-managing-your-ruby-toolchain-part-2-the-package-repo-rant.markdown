---
layout: post
title: "Managing Your Ruby Toolchain part 2: The Package Repo Rant"
date: 2013-05-17 09:23
comments: true
categories: [packages, toolchains, devops]
---

**TL;DR** Use package repos for all your static software

Last weekend I wrote a [blog post][post] that started exploring standards for managing your Ruby toolchains. To review, I had a query from someone on how to cope with the dynamic nature of Ruby-based tooling and managing a Chef infrastructure. and the fact that you don't always want to go to the internet to get stuff, it's a pain to be compiling things on servers all the time AND to do that, you need a compiler which is regarding as a Bad Thing<sup>TM</sup> in Production.

The question came to me via a talk I did at Chef conf and I'm going to use Chef examples, but, unless someone tells me different, I'm going to come down on the side of you can use this advice with Puppet and CFEngine and any other CM tool out there. It works without CM too, but why would you ever?

In this blog post, I want to talk about why I am so adamant about package repositories and offer some real world examples.  I pick on Tomcat a lot here. Nothing against Tomcat. It's something most people understand and that I have also had personal experience with it not being managed as a package and creating much distributed pain.

## Broken Record Much?
I can't shut up about package repos. Why am I so passionate about the need for all package repos all the time and to have a team actively managing and grooming them? 

As part of the decentralization of Ops teams and the rise of DevOps, we now see multiple autonomous teams simultaneously working on similar projects but not collaborating or communicating on infrastructure.  It behooves the infrastructure managers, whether that's ops or devops or the tools team or a central collaborative group comprised of reps from all the teams, to own and groom a collection of package repositories. 

**But Why?**   
With the rise of self-service, all you can eat virtual machines, development teams are expected and want to be independent from the corporate server supply chain.  Unfortunately this also decouples them from people they depend on: infrastructure and middleware experts(Yes Ops, that's you). They may sail gayly on and do their best or trudge forward grudgingly, resenting all of the extra work they now have to do because the Enterprise wants to be "like a startup." Either way, they're going to move forward, and when you don't offer them ways to easily use software, they'll figure out ways to [cope][]; ways that will horrify you when you discover them.

**I work at a startup because Enterprise is stupid like that.**  
Nope. It's not only the Enterprise that needs to fear the chaos of unmanaged, difficult to use infrastructure. Say your whole startup is fewer than 20 people? You're the "devops engineer" and you work with a 5 person dev team.  The dev team may or may not include you in everything.  You might not know of actions they've taken to [cope][] with problems you don't even know they have. They don't realize there could be a simple solution to their problems because they are brilliant startup devs and they will just figure it out.  Then they'll hand you their giant shiny mud pie and you will cry because you could have saved them a bunch of work with a few well placed repos and a web proxy.

Package repos create a beautiful garden where you static software can flourish and discourages tarball weeds from springing up in the cracks everywhere.

**If you establish a well known, central location for packages, people will know to look there first for an existing package that meets their needs. And they will ask to place packages they need there as well.**

Ex: In the situation where someone thinks 
>*"I need Tomcat7 and Red Hat only ships with Tomcat5"*  

Their correlating thought will NOT be   
>*"I should go download that from the internet and store it on the jumphost"*  

but instead
>*"I wonder if we have that in our custom repo?"*

or in an even more mature organization
>*"I should include our Tomcat cookbook in my runlist and override the attributes that are unique for my situation"*
  

### When you make package repos easy to access and use, you
  * Eliminate several slightly different copies of software (ex: tomcat) floating around your infrastructure.  
  * Eliminate several slightly different versions of packages deployed to Production.
  * Discourage placing random tarballs in version control or Chef cookbook files.  
  * Make your configuration management tool easy and intuitive to use.

### The Problem With Tarballs - a Real Life Example
Config management tools are designed to be idempotent, which means they only do things once and ensure things look the same regardless of how many times you run the the same process.  *Tarballs subvert the use of configuration management primitives*, requiring the use of non-idempotent execute blocks to manipulate them.  Real world example:

Without a package repo in place, this happens:
``` ruby 
remote_file "/tmp/tomcat7.tar.gz" do
  source "https://www.sometomcaturl.com"
  not_if { File.exists?("#{node[:tomcat][:home]}/bin") }
end
 
execute "extract_tomcat" do
  command "tar xf /tmp/tomcat7.tar.gz"
  cwd "opt"
  not_if { File.exists?("#{node[:tomcat][:home]}/bin") }
  only_if { File.exists?("/tmp/tomcat7.tar.gz") }
end
```

Or this utter travesty:
``` ruby
bash "install_tomcat6" do
	tomcat_version_name = "apache-tomcat-#{node.tomcat.version}"
	tomcat_version_name_tgz = "#{tomcat_version_name}.tar.gz"
	user "root"
	code <<-EOH
	  curl --proxy https://aproxy.com:8080/ --user user:pass https://myartifactoryurl.com/artifactory/ext-release-local/apache-tomcat/apache-tomcat/#{node.tomcat.version}/#{tomcat_version_name_tgz} -o /tmp/#{tomcat_version_name_tgz}
	  tar -zxf /tmp/#{tomcat_version_name_tgz} -C /tmp
	  rm /tmp/#{tomcat_version_name_tgz}
	  mv /tmp/#{tomcat_version_name} #{node.tomcat.install_path}
	  chown -R #{node.tomcat.run_user}:#{node.tomcat.run_group} #{node.tomcat.install_path}
	  chmod -R 755 #{node.tomcat.install_path}
	  rm -rf #{node.tomcat.install_path}/webapps/ROOT
    EOH
end
```
Notice that they very carefully delete that tarball when they are done, ensuring that it will be re-downloaded 30 minutes later on the next run. They also untar the file and then move it to the install location without checking to see if anything is already there.  Again, 30 minutes later the entire process happens again despite whatever is already there. 

Now look at what happens with a package repo in place:
``` ruby
package 'tomcat7' do    
  action :install  
end  
```
You may have to customize config files and init scripts, but this is a far simpler and less error prone way to install the base software. 

In this case, Chef knows what OS you are using, what your package manager should be, repository connection details and how to install the package. Puppet does the exact same thing. So does every other config management system in the history of ever.  The less code you have to write and the less verification code you have to write, they happier you'll be and the people who come after you will be. 

### The End
I've heard a lot of complaints from ops and tools folks that devs do "crazy" stuff and they are much affronted that the devs don't seem to understand the unpublished rules of operationalizing apps and the best way to use tools, and omg did you see the travesty of a Tomcat cookbook they wrote? 

I have spent years on ops teams, tools teams and automation teams now. I have been one of those self-righteous, affronted peeps.  This behavior doesn't scale, yo.

One of the best messages that DevOps has for all of us is, if you don't like the way someone's doing something, talk to them and find out why. You'll probably learn that there were great reasons for everything, including "I didn't know how to do it, Chef is hard, Tomcat is hard, and I was just a Java developer until you guys started making me do all this automation stuff without telling me the rules or offering guidelines, so I copied another team's Tomcat cookbook. I had no idea it's crap. It installs Tomcat and that's all I care about."

Indeed. That's all they care about. If you care about more than just the install, maybe you should reach out and offer to partner with them on the parts you care about.  Don't like the way devs are building application infrastructure? Well maybe you should do it for them. Provide them with a consistant VM build from a stable package repo and possibly infrastructure cookbooks with sane defaults and overrideable parameters. Chances are good devs don't like recreating that crappy Tomcat wheel any more than you like them doing it. It's likely they will be pathetically grateful because, while they are coping with the tasks they've been handed, they don't really know what to do in detail and researching JVM tuning parameters is not their idea of a good time.

Why am I telling you this story? Because I believe that a major piece of your infrastructure stability is created with package repos. Making them easy to find, use and contribute to will help smooth any number of peripheral problems you are trying to solve.  In my next post I'll talk about practical considerations for repositories, packages and toolchains.





[post]: http://blog.brattyredhead.com/blog/2013/05/12/pieces-and-parts-managing-your-ruby-toolchain/
[chefrvm]: http://github.com/fnichol/chef-rvm
[rbenv]: http://github.com/sstephenson/rbenv/wiki
[ian]: http://twitter.com/ianmeyer
[spec]: http://github.com/imeyer/ruby-1.9.3-rpm
[aptruby]: http://packages.ubuntu.com/quantal/ruby1.9.3
[eol]: http://www.ruby-lang.org/en/news/2011/10/06/plans-for-1-8-7/
[fpm]: http://github.com/jordansissel/fpm/wiki

