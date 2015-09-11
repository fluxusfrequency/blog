# Raising The Dead: Bringing Your Legacy Application Back Into Development
## Or, How To Fix Old Code Faster

ramp up developers on code
quickest way to get developers up to speed
project ramp up plan
fix old code faster
refactoring code with new developers
onboarding developers
developer team onboarding

## Introduction

Remember the glory days? Your company had it all! New signups every day. Coverage on the hottest blogs. Money was rolling in hand over first. And it was all thanks to your hot app. You and your comrades really nailed it when you followed the an [MVP]() process and found just the thing the market was looking for.

But then, something happened. Maybe you lost your entire developer team. Maybe you made an agreement with another company and had to shift your attention to a new integration. Slowly, the vines began to grow over what was once a glorious app. The technology used to build it was growing outdated. Bugs accumulated, but there was no time to fix them. You shed a single tear when you thought back to the glory days.

Finally, a new day dawned! A new funding round came in. You've just hired a new team of developers, and they're starting next week. You can't wait to get them rolling in your darling app. But are you ready? Starting a new team on an old app isn't as easy as handing them a laptop and saying "clone down the repo". In this post, we'll take a look at what it takes to get a team up and running on a legacy application. When we're through, you'll be ready to let the good times roll again!



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

### Update Dependencies

#### Gems

#### Rails Upgrade By Version Trick

### Delete Dead Code



## Establish A Clear Development Workflow

### PRs

### Staging

### TDD

### Smoke Testing



## Where To Go From Here?

Focus on what made the business a success in the first place

### Consider Removing Features

### Consider Splitting Out Services

### Use MVP To Figure Out Where To Go Next



## Wrapping Up
