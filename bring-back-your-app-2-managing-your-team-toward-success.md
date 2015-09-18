# Bring Back Your App: Managing Your Team Toward Success

The tech world moves fast. It's not uncommon for a startup to scale from two people in a coworking space to a team of thirty in a matter of months. Just as often, the team shrinks again, due to sudden market changes or errant developers moving on to the next big thing.

When you're getting ready to rebuild, it can be hard to know where to begin. There are so many things to do, from marketing, to onboarding a new team, to deciding what to build next.

In [part one of this series](http://quickleft.com/blog/ramping-up-developers-on-code), we talked about some of things you can do on the technical side to get your team up and running on the code with a minimum of fuss.

In this, the second and final part of this series, we'll focus on managing your team toward success. We'll look at how to decide what to build next, how to make sure you're committing the best code possible, and some safeguards you can use to avoid shipping broken features.

## Where Do We Go From Here?

Assuming your development team is all ready to go, you might start to ask yourself: "what do we build next?"

But before you assume that you're ready to start shipping new features, you should take a look at what's already there and ask yourself if you shouldn't do a little housekeeping first.

### Take An Inventory

It's a good idea to take an inventory of the workflows and features that are already in your application. Make a list of ways that users interact with it. Maybe even [write user stories](https://blog.engineyard.com/2015/happy-sad-evil-weird-feature-planning) for the functionality that's already there.

If you have analytics in place, you can use them to find out which parts of your application get the most use.

### Consider Removing Features

Once you've gathered this data, go through and assign a value to each of the features. Are there secondary features that aren't seeing much traffic? Maybe they were failed attempts to grow your user base. Maybe they used to be popular, but a competing technology rendered them obsolete. Can you identify any parts of the app that require a lot of upkeep, but aren't really serving your users?

Think about [Basecamp](https://basecamp.com/). They used to be [37Signals](http://37signals.com/), but in February 2014, they announced that they were dropping support for all of their applications aside from Basecamp. They whittled it down to what they knew was successful so that they could focus all of their resources on it.

You can do the same thing on a smaller scale. Focus on the part(s) of your application that made the business a success in the first place. Does your marketing and landing page drive users to interact with that feature? Is it easy to get to and use it? Are there other features or workflows that are getting in the way of users finding their way there?

If you can identify some features that you can lose, you can focus more on the things that drive users to your site, refining them and making sure that they are solid and bug-proof. It also gives you room to experiment with _new_ secondary features without overwhelming your users. Think of it as refactoring at an application level.

### Consider Splitting Out Services

In [part one of this series](http://quickleft.com/blog/ramping-up-developers-on-code), we talked a bit about refactoring your application code. If you find yourself getting into this whole code extraction thing, maybe you want to go whole hog. You can talk with your engineers about whether it makes sense to extract an entire part of your app into a separate service. In many cases, starting a whole new code base can take a lot of time, and might not be worth it.

On the other hand, if it's hard to make changes in what you've already got built, sometimes it can actually speed things up. With a smaller surface area, and code written by people who _currently_ work for your company, it can be easier for folks to grok what's going on. This leads to faster development.

If you do go down the road of extracting services, make sure that your team is clear about the boundaries of the old app and the new one and how they will interface. For example, if you're pulling out a [JSON API](https://blog.engineyard.com/2015/active-model-serializers), you might want to come up with a sample JSON response and the HTTP Status Codes that will be used _before_ you build it. Defining the limitations and structure beforehand can keep a project like this from dragging on endlessly.

### Use MVP To Figure Out Where To Go Next

A lot of people don't realize it, but a Minimum Viable Product isn't just _a thing you think up and build_. It's actually a process. A way of testing out the market to find out whether things you _think_ that people will like are _actually_ things that people will like.

If you haven't tried driving your business this way, you should! Instead of just guessing what you should build next, or [building whatever customers ask for](https://gettingreal.37signals.com/ch05_Start_With_No.php), you can actually find out what to build using research and numbers. Customer requests are great, but spiking on new features and getting information from analytics and customer feedback can really help you hit a home run.

Assuming you're getting ready to scale up your app, you probably already have some ideas about what you want to build. Stop for a second, and consider adding a couple of months of [MVP-driven development](https://blog.engineyard.com/2015/actually-mvp) to your roadmap instead.


## Establish A Clear Development Workflow

### Code Style Guidelines

### TDD

### PRs

### CI

### Staging

### Smoke Testing


## Wrapping Up

Managing your team toward success
