---
layout: post
title: "Learning to Let Go (or how I stopped worrying and learned to love the
bomb)"
date: 2012-07-11 15:54
comments: true
sharing: true
footer: true
categories: 
- Consulting
- Culture
---


Today I was having a conversation about consulting and how important it is to
not be emotionally attached to your solutions. As a consultant, you exist to be
an enabler. You help others succeed with tasks that, without your expertise, they
might fail instead.  As consultants, our value comes from a long history of
observing and participating in spectacular failures and fantastic
successes. We know what works and what doesn't work and we can articulate the
reasons why.  But our value can be compromised if we can't interact effectively
with our clients. This means checking your emotions and inner schadenfreude at
the door and working with clients to discover a solution that is practical for
their environment.

##### BOFH
Back in the day, I worked on a silo'd web operations team at a big retail
company. We knew how we liked things done and, although we might argue amongst
ourselves, we presented a united bullying front to the dev teams.  For example,
  we didn't allow them to deploy apps into production when they came with the
  caveat of "you have to restart the app every night." We drank developer tears
  with breakfast, and we liked it that way. 

  Guess what? Consultants don't have that luxury. I might also argue that
  internal operations teams no longer have that luxury, but that's a blog for
  another day. 
  <!-- more -->

  In that job, I saw every kind of connection leak, OOM and spectacular
  cascading infrastructure disaster you can think of and I participated in
  resolving several.  Eventually I moved on to consulting for a large company
  where I designed and deployed infrastructure for large web-based
  applications. My previous experience was not just invaluable for designing
  infrastructure in diverse environments, It also enabled me to advise client
  development teams on how to write robust applications and assist them in
  tracking down performance problems during the development process.  I was
  full of ideas! They were all brilliant! Wait, what!? Where is everyone going?
  How can you not like my ideas? 

##### Keep your WTFs to yourself
  As a new consultant, I had excellent technical knowledge, some decent
  communication skills, and a host of opinions.  I knew the right way to get
  things done and people should listen to me because *I was the consultant*.
  But technical knowledge and opinions can only take you so far. Some people
  aren't suited to life with clients. Often there is no one to shield you from
  difficult people they way your manager would in a normal job. You need to
  work with all types of personalities, while maintaining a calm demeanor. You
  need to be able to not take things personally. You must be capable of
  articulating the why's behind your ideas and speak objectively about ideas
  from the client, even if you think it's the worst idea since Greedo shooting
  first. 

  You can't say things like, "That's the dumbest thing I've ever heard," or
  "Why in god's name would you ever do that?"  A favorite that I had to give up
  was, "Why do you want to make me cry?"

##### Don't Get Attached
   A concept that challenged me is that there is no absolute right or wrong way
  to design something. The corollary to that is you will always disagree with at least
  one thing the client wants to do. Sometimes you will disagree with many.
  Sometimes they will disagree with your solutions and expect you to implement
  what they want, not what you want.  

  Well, WTF? You think to yourself, how am I supposed to do that? Newsflash:
  You need to figure it out because that's why you get paid the big bucks. As
  an independent consultant now, I have some leeway to do my due diligence and
  decline projects that look risky or are implementing tech stacks of which
  I don't approve. As an employee for a consulting company, I went where they
  pointed me and I liked it. And I figured it out, because they paid me
  a boatload of money to use my brain.

  Something you will hear often as a consultant: "That's a great idea, but
  we're not going to it." A client once decided to implement mission critical
  queuing on WebSphere Application Server's internal message bus. I could and
  did explain that this solution was not robust until I was blue in the face,
  but the fact is, WebSphere MQ software is expensive and open source wasn't an
  option(because). *Retail order fulfillment queuing* was implemented where
  they wanted it and I spent my days and nights reading up on how to ensure as
  little data loss (orders, right?!) as possible if the unclustered,
  non-redundant server went down. As an aside, I should note that this client
  has since gone out of business.

  You can't be emotionally attached to your ideas. I know you think you have
  the one true path to success.  You don't. Regardless of how smart you are,
  the customer knows their systems better and they know their politics better.
  They know what can be accomplished.  Get used to having your ideas shot down
  and don't let it upset you. Don't hang on to them.  You need to get over it
  and move on. If you're still hanging on to today's disagreements tomorrow,
  you won't be able to think clearly tomorrow about new technical challenges.

##### Pick Your Battles
  Sometimes you'll encounter strong opposing opinions. If you're an IT
  consultant, it's guaranteed. Remember, 95% of battles do not need to be
  fought. Just because you don't approve of a naming convention is no reason to
  argue, especially if another engineer is in love with it.  You might
  implement applications that need nightly rolling restarts, even if you could
  prove they don't need it, just because management has been burned in the
  past. Don't fight it. Nightly restarts aren't the end of the world.  You
  might meet a team who wants to migrate all of their bash scripts verbatim
  into configuration management. This would be a hard one for me to let go, and
  I might try to convince people not to do it, but I would concede in the end,
  because **refactoring exists**.  

  Save up your argues for when it matters. Eventually you will come upon
  something you absolutely won't want to compromise. If you haven't been
  arguing about everything up to this point, it's likely people will actually
  listen when you bring it up.

  Your job isn't to say no.  It's to say yes.  If your first inclination is to
  dismiss an idea or say no, stop and think about why. Do you have valid
  concerns? What is a valid concern? A valid concern might be a high
  availability requirement with only one database server in the plan.  That's
  a problem. But you still don't get to say no. That's not your job.  Instead
  you should point out the very obvious risk involved in zero redundancy.
  Several years ago, I was presented with a client's server plan that I knew
  would never survive the first engagement. In this case, we knew they were
  trying to save as much money as possible, so the consulting team got together
  in a room and came up with a perfect world scenario and then two less
  expensive alternatives that allowed for some robustness while saving on
  hardware expenses. I then power pointed the entire thing and presented to
  senior management. I had been consulting for all of 3 months at the time and
  was petrified but we won them over to one of our solutions in the end.

##### This isn't relevant to me, I'm a full time employee
  Are you on an ops team? Do you work with development teams to get apps into
  production? How about a monitoring team? Do you work with devs to create
  coherent actionable alerts? Then you are a consultant. Developers care about
  one thing: writing code. Some developers do care about more and have the
  background to be interested in more, but in general, especially in big
  companies or outsourced development, all devs care about is writing code.
  They don't know how anything else works. It doesn't make them dumb, it makes
  them part of a world of which you only see a part.

  This makes you their enabler and a consultant. You have knowledge critical to
  their success. It's not just your job to provide them with an app container
  and now buzz off.  You're their lifeline to the rest of the infrastructure.
  Telling them "no" or ridiculing an idea will only make them angry and
  determined to circumvent all the sane safeguards you've put into place for
  everyone's good. 

  Instead of treating development teams like they know nothing, take a moment
  to find out why they want to do what they're doing.  Many times you'll find
  that they've actually just spent two solid days trying to solve a prickly
  problem and this is the only working viable option they've found. Maybe your
  perspective will help you advise them on a better way, or they could be right
  and you could be stuck with something suboptimal. At this point, instead of
  being grumpy about it, start figuring out ways to limit the damage or make it
  more robust and work with your dev instead of against him.  Full time
  employees, I'm talking to you.

  Consulting isn't for everyone. If you can't learn to take deep breaths and
  count to ten, it might not be for you. I still make mistakes and have often
  been grateful for working remote where I can wear my rage face in private.
  In the end, you have to be able to let it all roll over you. This isn't your
  infrastructure and sometimes compromises must be made.  

  For me, consulting is emotionally easier in some ways because I can accept
  decisions with which I don't necessarily agree, knowing that I won't be
  around to see the heartache in a year. All I can do is my best with whatever
  is under my control. And my best is impressive. I am a master tweaker and
  I document the hell out of ALL THE THINGS.  But I often see sacrifices of
  stability for expediency and it does hurt me.  I've just learned to not let
  it hurt too much. The great thing about consulting is, if this project
  didn't work out exactly the way you wished, the next one is right around the
  corner, waiting for you bring it all your awesomeness.
