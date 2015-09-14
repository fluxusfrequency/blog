# Raising The Dead: Bringing Your Legacy Application Back Into Development
## Part 1: Ramping Up Developers On Code

ramping up developers on code
quickest way to get developers up to speed
project ramp up plan
fix old code faster
refactoring code with new developers
onboarding developers
developer team onboarding

## Introduction

Remember the glory days? Your company had it all! New signups every day. Coverage on the hottest blogs. Money was rolling in hand over first. And it was all thanks to your hot app. You and your comrades really nailed it when you followed the an [MVP]() process and found just the thing the market was looking for.

But then, something happened. Maybe you lost your entire developer team. Maybe you made an agreement with another company and had to shift your attention to a new integration. Slowly, the vines began to grow over what was once a glorious app. The technology used to build it was growing outdated. Bugs accumulated, but there was no time to fix them. You shed a single tear when you thought back to the glory days.

Finally, a new day dawned! A new funding round came in. You've just hired a new team of developers, and they're starting next week. You can't wait to get them rolling in your darling app. But are you ready? Starting a new team on an old app isn't as easy as handing them a laptop and saying "clone down the repo". In this post, we'll take a look at what it takes to ramp up developers on code in a legacy application. When we're through, you'll be ready to let the good times roll again!


## Have An Easy Setup Process

If you're about to bring several developers onto a code base that hasn't been worked on in a while, you'd better be ready for them.

Maybe you're planning to write off two days for each person to get their laptop set up, set the application and its dependencies installed, and familiarize themselves with the codebase. If it's just one or two developers, you can probably justify four days of lost time. But what if you're bringing on a team of ten? And what if they could get set up in one day instead? Or maybe even half a day? That would be 15 more developer days that could be spent on developing new features and bringing in more money!

So how can you get them up and running faster?

### Documentation

Documentation is so important, I can't possibly recommend it enough. It's also the most efficient way to get a new team member up and running. If you have up-to-date docs, new developers can read about the architecture and steps to set up the application, instead of having one of your more experienced developers take time out of development to sit down with them and explain it.

There are many effective ways to make documentation accessible to your team. I've seen companies have success with storing pages in the `wiki` part of their GitHub project, in Atlassian Confluence, a `docs` folder inside of their main app repo, or a `docs` repo in their GitHub organization.

Document as many things as you can. Your main application should have a succinct but thorough `README` in its repository, so that new hires will see it as soon as they visit the GitHub repository. If you're running a separate server and front-end app, there should be a way to understand what has to be done to get them both running and talking to each other in development mode. How do you install the dependencies? How do you run the tests?

Sometimes, project set up can get held up by weird system setup issues. Maybe the app used to run on a pre-Yosemite version of OS X, but when devs try to install it on Yosemite, they run into problems with libxml. I recommend creating a `troubleshooting` document and putting errors and their fixes into it. At [Quick Left](), we cut our dev setup time by several hours when we introduced a `troubleshooting` doc in the [Sprint.ly]() repo.

There are many other things that bear documenting. Here are some other good things to include: Language style guides, office culture, pull request norms, QA tasks, and deploy steps.

Putting good documentation into place will cut down on the hours your new team members spend getting set up, and save you money.

### Docker

We live in an exciting time. The dawn of containers has made it easier than ever to make sure that each person working on a project is working with the same system setup. I like to use [Docker]() to get a snapshot that will make it easy for an application to run across all environments, from development to CI to production. If you've never created a container before, I recommend checking out my colleague Alex Johnson's [Sailing Past Dependency Hell With Docker](https://quickleft.com/blog/sailing-past-dependency-hell-with-docker-distributed-systems/) post.

Docker isn't right for every team, but if you have the knowledge and time to get it set up, it can save hours of time that would otherwise be spent googling errors and entering arcane Linux commands. I'd recommend looking into it.



## Pay Off Technical Debt

Sometimes, even great documentation isn't enough to ramp up developers on code in a reasonable amount of time. If your app has accrued significant amounts of technical debt, this can present a major milestone. There are basically two kinds of tech debt to look for: dependency debt and native debt. Here are a couple of ways to pay down each.

### Dependency Debt

If your app was built back in the heyday of _(name your tech stack here)_, you probably made use of a lot of open source packages that were popular and well-maintained at the time. But fast-forward a few years, and suddenly some of your dependencies are outdated, and others a deprecated. This can be not only frustrating, but also a potential security concern.

If it's been a year or two, you've probably missed some patches that were released to deal with widely publicized security issues. This is especially important when it comes to your language. For example, running Ruby 2.1.1, you might find that you're vulnerable to DNS Hijack attacks and [other security holes](http://www.cvedetails.com/vulnerability-list/vendor_id-7252/product_id-12215/version_id-164073/Ruby-lang-Ruby-2.1.1.html).

In my opinion, the best course of action is to invest a developer day or two getting your language version and dependencies updated _before_ bringing on a bunch of new developers. This approach will save you tons of time down the road.

#### Gems & Modules

If your app is built in Ruby or JavaScript, you're probably making use of [Bundler]() or [NPM]() to resolve dependency versions and pull them down. In your `Gemfile` or `package.json`, you may be specifying the specific versions of packages you want to use, like this:

```ruby
gem 'rails',                  '~> 3.2.17'
gem 'jquery-rails',           '~> 2.1.3'
gem 'mysql2',                 '~> 0.3.11'
gem 'devise',                 '~> 2.2.0'
gem 'cancan',                 '~> 1.6.8'
gem 'nokogiri',               '~> 1.5.6'
```

```javascript
"dependencies": {
  "config": "^1.10.0",
  "hapi": "8.1.0",
  "hapi-auth-cookie": "^2.0.0",
  "superagent": "^1.2.0"
}
```

So your new developers pull down the repo, run `bundle install` or `npm install`, and find out that you actually _can't_ install some things on the latest version of OS X. Or maybe you realize that the authentication gem you're using has to be updated by a major version. Fine, you think, and update the version specification. But then, when you try to run the install, you have version conflicts with another gem.

There's no one great way to resolve all of these problems at once. The best approach I've found is to start with the gems that you most need to update. Bump their versions, then try running the `install`. If there's a dependency version conflict, change that. Keep following this pattern until you've got everything fixed. If you run into issues, sometimes a `bundle update` or deleting the `node_modules` folder completely can get you closer to success.

#### Rails Upgrade By Version Trick

When it comes to updating Ruby gems, I've come across nothing more painful than updating [Rails]() across multiple versions. I've recently been working on upgrading an old Rails 3.1 app to Rails 4.2 for a Quick Left client. If you try updating the whole gem version at once, you're pretty much guaranteed to into so many regressions and new bugs that you won't even know where to start.

There is a better way. Begin by upgrading Rails one _minor_ version at a time. So, if you're on 3.1, upgrade to 3.2 before attempting to go to 4.0. There a [list of Rails releases]() that you can use to drive this process. I've also found this [Rails Upgrade Checklist](http://www.rails-upgrade-checklist.com/) site pretty helpful. It lets you know all the things to consider at each version along the way.

### Native Debt

So you've got all of your dependencies updated, and installing your app is a breeze. You can get a new developer from zero to sixty in just an hour. Great! But now, do they actually understand anything about what's going on in the code base?

If you're lucky, your last team wrote clear, understandable code with lots of comments and no duplication. In reality, the odds of that being true are pretty low. Because of business pressures, just about every code base ends up with some cruft, duplication, and just plain cryptic parts.

Take the time before your new team rolls on to clean things up a little bit. It will be invaluable in getting them up and running, ready to be part of the conversation on architectural decisions.

#### Refactor

You've heard it before: if you practice agile development, you need to refactor. It's usually something you should be doing along the way if you're practicing Test Driven Development. It goes `red, green, REFACTOR`. Nevertheless, there are probably dozens of places that your code could benefit from some cleaning up. If your team is new to refactoring, I highly recommend the [Refactoring book](http://martinfowler.com/books/refactoring.html) or its [Ruby counterpart](http://martinfowler.com/books/refactoringRubyEd.html).

Generally speaking, the easiest wins are to `extract` and `inline` code. This works at any level of scope. If you have a big, unweildy method that's doing a lot, you can [extract a method](http://fluxusfrequency.github.io/blog/2014/01/10/refactoring-1-extract-method/) from it. If you have a class calling a method that doesn't make any sense, you might want to [extract an object](http://fluxusfrequency.github.io/blog/2014/01/17/refactoring-2-replace-method-with-method-object/). You can also go the other way: if there's some unnecessary indirection, you can just pull distant code right into the class or method that needs it.

Spending even a day or two refactoring the hairiest parts of you application can yeild huge wins for understandability. That makes a big difference in your team's  efficiency.

#### Delete Dead Code

Related to refactoring, another great way to reduce the mental overhead involved in starting with a new app is to remove dead code.

One easy win is to "snipe" unused gems or packages. In Ruby, you can do this by following my colleague [Meeka Gayhart's instructions]().

Aside from dependencies, you should also look through the codebase and remove anything that's old and not being used anymore. It can be hard to determine where exactly those spots are. If you have someone who's familiar with the code, asking them can be a good place to start. If you have a good test suite, you can also try removing suspect bits of code and seeing if anything breaks.

If you take the time to clean up your code base before new developers roll on, it can save huge amounts of time. New devs don't have to work as hard to understand what's going on, so they can spend those mental cycles shipping new features instead.


## Wrapping Up

