# Rescuing Rails Apps - What I've learned

### "Rescuing Apps" ? Is that a thing?
As Rails matures as a development platform, we see plenty of developers reaching for Rails, attracted by the friendliness of Ruby and the much vaunted speed of development that Rails provides. However, it is easy, especially for developers new to Rails and the MVC pardigm to get carried away and start putting code in places where it isn't supposed to be. Business logic in the controllers and views, authentication and authorization logic sprinkled throughout the app and so on. Many developers new to Ruby also have not yet climbed the learning curve on RSpec and automated testing so deployment remains a stressful activity. In the end, half knowledge of ActiveRecord makes it very easy to shoot oneself in the foot and so many people do.

### What is *not* a rescue?
To be clear, an app that needs major work because it has outgrown its original architecture is not a rescue operation. Sometimes a Rails app is chugging along in production for months or years and is starting to hit some limits to scale. By this time the business is generating revenue, the model has been validated and there is generally less stress in the system. Such apps also seem to have been built by more capable teams who are perhaps at the limit of the chosen architecture and need a whole new way of doing things such as splitting the app into several services. This, in my opinion, is not a Rails rescue operation. This is a logical outcome of growth

### So what *is* a rescue?
The word "rescue" implies that the app got into unexpected trouble. A Rails Rescue operation is one where the app is put together badly by inexperienced developers and despite massive efforts, is just not performing up to desired levels. Such situations are more and more prevalent as the demand for developers far outstrips supply and non-technical entrepreneurs are not able to judge good developers from bad. Also, as businesses iterate, the codebase suffers terribly as new requirements are jammed on top of existing behaviour and stale code is never removed from the codebase. So by the time the "MVP" is ready, the code has already been on a long and circuitous march and is tired. This is an extremely stressful situation for the most part as one is close to launch, resources are running low, nothing is proved, competitors are lining up and investors are getting impatient for a launch. Into this deep valley, we must now descend and rescue our app.

### So devs that need rescuing are bad devs, right?

Mostly not. The first time definitely not. The second time less so, maybe even forgiven if the problem is sufficiently complex and the third time should never happen. Looking at apps that need rescuing as "failures" is not constructive. Building software is an incredibly complex activity and we happen to have a lot of people solving these massive problems for the first time. In the Mythical Man Month, it is stated as a given that all software eventually gets rewritten. Rescue ops should be seen almost as a rite of passage for a site. The app has grown beyond the capabilities of the original developers and this is a good thing. In no time these guys are going to be rescuing someone else's app. The cycle of life goes on.

And I know a thing or two about being on the other side. Take a look at the source code of Mostfit [mostfit code](http://github.com/svs/mostfit).That was the code quality that drove Mostfit to failure. 

### Give us a few pithy quotes

When it comes to Rails rescues, there are two quotes that are worth keeping in mind. The first is by the American general Eisenhower who said,  "plans are meaningless, planning is everything". i.e. in the fog of war, there is no plan of yours that is going to survive for very long, however, the hours you spent planning will stand you in good stead when you have to improvise. The other great quote which guides Rails rescues for me is by @_anshulk who said "You can never turn svn into git". A fundamentally broken data model will always hinder development and you can make a much better svn, but svn can never be git.

In short, any Rails app that can be rescued, probably doesn't need rescuing. Hwoever, one must try and rescue it anyways for a variety of reasons that I'll get to in a moment. Rails rescues also present very interesting organisational challenges. Many times, the incumbent developer is still in the picture and may start to feel defensive about his or her code. Senior leadership of the company that signed off on the code don't want to hear about how bad the code is. Rails rescues are therefore rarely refered to as such by the client, prefering instead to use terms like "make the app scalable", etc.

In the end, software reflects the organisation that created it and so this article is only nominally about Rails rescues. Every app comes with a bunch of people, legal relationships, egos, fallacies and beautiful human imperfection as part of the package and the two can never be separated. So without much further ado, here's a step by step guide on how to rescue a startup from its failing culture and organisational structure, usually pinned onto the hapless Rails app and its creators.


Step 0 - Domain and Organisational Mapping

Before you can fix something, usually, it is nice to know what it is that needs fixing. As programmers, we often get tossed into new and interesting domains and our new clients will be tossing around acronyms and jargon that you will be hearing for the first time. A rescue is impossible without a decent understanding of the problem domain and the proposed solution. Our job is not to provide a good solution to the problem at hand. Our job is to make the proposed solution work as desired. Never lose focus of this. During the course of the rescue, 

Never be afraid to appear ignorant about domain specifics. Whenever you hear a term you don't understand, ask what it means. You'll be surprised how many times there are three answers in the same organisation. Also, better to be up front about what you do and don't know. Maybe the app involves calculating trajectories for spacecrafts and you haven't seen a differential equation in your whole life. Better to not be on this project, lest the rescuers need rescuing. Most domains though have their jargons and expansions which are not complicated to understand.

At the same time, reserve all your judgements on whether this app is going to work in this domain, whether the business model works, etc. etc. All these concerns are othagonal to the job at hand, which is anyways going to require all our attention and energy.

Like the proverbial blind men of Indostan, everyone you speak to will have a different way of describing the same beast. It is very useful to talk as much as you can with people before rolling up your sleeves and getting to work on code. There is a tendency to talk most with the senior people, but do spend time talking with the internal users of the system (customer support, content creators, etc.) leads to valuable insight.

Spend a day or so talking about the product in question, what its supposed to do, how many users are envisaged as being supported on the system, what the deadlines are, what are the motivations of the founders, CEO, tech team and so on. Usually there'll be a design document or something that talks about the expected behaviour of the system. Get a demo to see how far people have got, what people think of the delivered product so far and so on. As mentioned, Rails rescues often have a political angle to them and it is important to know where people publicly stand on the issue. Have tea or beers with each of the stakeholders and ask them about the history of the project, how they came to be involved, etc. Are some people transitioning out? is the tech lead close to burnout? how many resources are contractors? who has equity? etc are all valuable clues to achieving a successful result for everyone involved.


Step 1 - Code Analysis

Now, on to the code. Pull down the repository - by the way, if they aren't using any version control, make some apologies and turn the project down - and get started surveying the code.

``` rake stats ```

Oooh. Long controllers, long models, no tests. This is going to be a long hard slog. You can also use metrics tools like flog, flay, reek and so on. So what does a project in need of rescuing look like? Something like this

<github gists>

Once you've mapped out what the general topography of the code is, it's time to setup a working environment on your machine. Projects that are in trouble are usually in trouble everywhere, so the install will probably not work with a git pull, bundle install and rake db:migrate. There will be seed data scattered through the app and constants that don't get automatically loaded and so on and so forth. Installing the system also gives one a good feel for the general toolchain, the amount of gems in use (more is worse) and so on.

Lastly, one more demo. Get the product owner or architect to give you a demo of the system. Keep an eye out for slow loading pages and so on. Normally, wherever the reporting happens is going to be where the bad data model meets reality and so pay particular attention to the reporting section of the app. If possible, keep an eye out on the logs while the demo is in progress. The low hanging fruit for optimisation will be readily apparent from all the N+1 queries the ORM is no doubt making. Long joins and so on are also easy to spot from here. Turn on a slow query log if you can, for added convenience.

By the time this is done, you should have a broad idea of how much trouble the codebase is in. Take your time with this step.


Step 2 - Measurements and Low Hanging Fruit.

At this point, one is still poking around the codebase and data. Look through the logs and see where the system is hitting bottlenecks. Add profiling information throughout the system. Find out which are the user facing and internal facing parts of the app and concentrate on the user facing ones. It is a rare codebase in which this does not yield some easy pickings for optimisation. Perhaps a N+1 query somewhere gets fixed, a small piece of data gets cached and read from the cache instead of doing extensive computation, an aggregate is calculated by the database instead of looping through the array in Ruby, and so on.

It is important to pick the low hanging fruit early, essentially as a trust building measure. Delivering some value early on and communicating it clearly is essential to winning the confidence of those involved. It also sets the right mood for the bad news you're about to deliver them.

Step 2 - De-stress.

As I said, normally it is close to launch time and there is a lot of stress in the system. Stress is the startup killer. Startups do not end when they run out of money. Startups end when the cofounders no longer want to work on the startup. De-stressing the startup is of the utmost importance. There are a number of strategies to destressing the situation of which we will examine a few.

The first one of course, is to delay the launch. By now, you should have some data with which to argue your case. The slow queries and long joins can be useful here to communicate with management if the situation is serious. Tossing the code onto codeclimate and getting an unbiased, third party opinion is also useful in communicating the seriousness of the situation. Hopefully, your judicious picking of the low hanging fruit has given you some credibility, some of which you should spend here if required. However, this option is not reccommended unless the project is in real trouble. Delaying the launch is also not going to be an easy sell, so don't blow too much of your goodwill on this one. If it is easy to do, suggest a postponement, but good luck with that. Founders are usually a superstitious bunch and want to launch on auspicious days. Also, if you're unlucky, communications have gone out to inverstors, beta users and so on, so the chances that the launch can be postponed are slim to none.

So, what can we do with what we have at hand? Out of the various ways in which the above codebase fails, the most serious of these situations is of course the fact that there are no tests. Without automated testing, every deploy will be a major pain. Being close to launch, this is also around the time that many new features are hitting the codebase and the amount of work for testers grows exponentially. With no test coverage, developers will be breaking things that they had fixed earlier, leading to a load of frustration for developers and testers alike. It is most important to create a working integration test suite for the app at the earliest.

Get the devs to take some time off from banging more code into this codebase and learn how to write some capybara tests. Usually, once they see the browser popping up and doing all the pointing and clicking for them, you've made friends for life. Stressed devs take to automated testing like ducks to water. It's manna from heaven! Cover as much of your app with integration tests as possible.

Why integration tests? Because by now it is too late to write unit tests. The broken data model is often not worth covering with unit tests. With the behaviour scattered throughout the app, we will never be able to meaningfully test any functionality other than through an integration test. Once you're done with integration tests, write controller tests. Any test that becomes coupled with the implementation of the system will be fragile and brittle. Integration and controller specs, while not free from coupling with implementation, suffer less from this than the models.

Any bugs the manual testing brings to light must be integrated into the test suite in a disciplined manner. As the guy in charge of the rescue, incentivise people to only deploy on green. There probably isn't enough time to set up CI, but if you have a friend who does Jenkins well, get him on the job.

Infact, automating tests is just one example of automating stuff and getting it off the plate of humans and into the loving embrace of the machine. Write rake tasks to do whatever cleaning, massaging etc that needs to be done. Get the devs off anything that doesn't require them to be human. soon you will be launching and the bugfix requests will be coming in thick and fast. You need a team that is rested, destressed and with some time on their hands.

At this point, with the machines doing all the heavy lifting, you should start encouraging devs to leave on time and not work weekends. In my experience, there exists a severely inverse correlation between time spent and productivity for developers beyond a point and there is nothing to be gained by pushing on for another two hours on a problem that will almost certainly be fixed in 15 minutes by a well rested person. However, this is easier said than done. As an outside consultant, your "interference" into the culture of the company will only be tolerated upto certain limits and changes to work conditions of employees may not be well received. On this point though, you should blow some of your goodwill as tired devs on a production system are the basic ingredients of a spectacular failure.

Thirdly, constrict the scope of the application. Most entrepreneurs want to protect their business by investing in IP and there is almost always a particular part of the app that they believe is their secret sauce and what investors will pay for. Usually, in such an app, this secret sauce is poorly specified and behaves every which way but the intended one. Try and pry it from the vice-like grasp of the founders and try and convince them that engineering resources are better spent in providing the user with a robust if somewhat less sexy experience. Good luck with this one.

Step 4 - Data Model

Now that you've been fiddling with the app for a few weeks, it's time to take a step back and go for a long walk by the seaside, in the mountains, on the broken, potholed road outside your office, wherever you can let your thoughts wander freely. Don't push it. You're a smart guy and you've been thinking of nothing else for three weeks. Time to let that subconscious mind earn her keep for a change. Go play pool. Pay some attention to your significant other. Go to Goa and come back cross eyed from partying. Or something. when you get back to work, spend time staring out of the window. Hang out at the tea shop outside. Ask questions and then another one half way through the answer. Read a lot and watch loads of conference videos. Try and find the most abstract general description of the problem you are facing and then start doing some research. Be nonchalant when on the third day, a beautiful solution emerges. I remember once watching a talk on modelling trees in an RDBMS when the solution dropped out of the laptop screen and into my lap. In six days, we had report generation down from 8 seconds to 200ms.

While implementing your new data model, take baby steps. Write a rake task to migrate old data into the new shape. Work on writing the data in both forms into the database. Move one report from the old way to the new way. Feel the shimmering glow of computer theory solving your problems for you. When you make the reports blaze, advertise them. your client needs to know that they're getting value in return for your cheques. However, do not downplay the effort required to get there. Clients will soon expect all problems to be solved in linear time but the truth is you have fixed all the easy problems and are now working on decidedly harder ones. It is important to manage expectations around this one.

Ome of my favourite ways of finding a nice data model is to denormalise the data. If a report is making a bunch of joins and is getting dog slow, or if it is very difficult to specify all the calculations cleanly, then it is time to move the load from read time to write time. The code that generates the report should be very simple. Simple filters and aggregations at the max. Generally should try and come from one table, ok one join at max. Instead, when events happen that update the report, they should write a few more pieces of calculated data into the system. Having said this though, denormalising data is not without risks. It is possible to end up with databases where the data is constantly out of sync and then you fall into the slowly spinning vertex of endless rake tasks which will surely drag you down to your doom, so step carefully when denormalising data.

A simple guide to keep you out of trouble should be to iterate towards a situation where some tables are purely for performance and can be trivially regenerated from some other tables that hold the canonical data about the system. Once you get there, you will find that your data model on the core tables is quite nice and friendly.

Once you have moved most of the complexity to write time instead of read time, your app will be able to scale better as most apps get read more often than they get written to.


The Hairy Parts

Politics and Communication

Rescue projects, being political issues, require a lot of transparency if they are to succeed. Without transparency, they quickly devolve into cesspools of suspicion. Luckily, as the outsider, it is very easy for you to set this process in motion by communicating freely, frankly, openly and copying as many people as you want to on emails. Never ever assign blame to anyone in these communications. That is not your job and you will almost certainly not be in possession of all the facts when you do so. Simply present the situation, backed up by as much data as you can muster and always leave the option of choosing one way or another to the client. "Hey, we have an option. Either we can spend three days to a week researching the problem and get a reasonable solution, or I can put some more duct tape and keep this chugging along for a few weeks more like this."



Clear and data-driven communication is often the best way to assure people that you have very good reasons for doing what you are doing. Do not be aggressive in setting timelines. Aggressive timelines are probably half the reason for the current situation so it's important to communicate to everyone involved that doing more of the same may not yield better results. It is always preferable to solve problems before launching as deploying manually (as I am sure this project is) into an app with users, fixing bugs under stress, etc. are sure fire ways to ensure that one day your Angel Investor wnats to show off your app to a potential Series A buyer and is met with a "We're sorry, something has gone wrong", otherwise known as the kiss of death.

In the end, what everyone wants from you is for you to come in, wave a magic wand and make things well. Constricting the scope of the product, toning down aggressive expansion plans and adopting wholly new behaviour as an organisation are not what people are expecting. However, it is important to note that a broken Rails app is not the problem. It is a symptom of organisational and management failure. Most of this failure is perfectly justifiable due to the fact that almost no one has done this before but entrepreneurs often do not see it that way and with the "finish line" of the launch beckoning, they just want to get their baby out into the world as fast as possible.

Slow, considered steps at this point are not what people are paying you for. Unfortunately, it is the only way I know of to emerge from the dark night and so Rails Rescues are actually whole organisation consulting assignments. Tread carefully.


SCRAPS

Here's my number 1 pro tip to founders - DO NOT BURN OUT. Once the veil of grey descends and food starts tasing like paper, when everyone is an asshole, nothing works and life is a dreary fog, it means you are so tired that the brain has lost its entire store of life-affirming chemicals. You will not continue much longer in this state.
