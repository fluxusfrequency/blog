# Bring Back Your App: Managing Your Team Toward Success

The tech world moves fast. It's not uncommon for a startup to scale from two people in a coworking space to a team of thirty in a matter of months. Just as often, the team shrinks again, due to sudden market changes or errant developers moving on to the next big thing. When you're getting ready to rebuild, there can

In [part one of this series](http://quickleft.com/blog/ramping-up-developers-on-code), we talked about some of the technical things you can do to get your team up and running on the code with a minimum of fuss. In this, the second and final part of this series, we'll focus on managing your team toward success. We'll look at how to decide what to build next, how to make sure you're committing the best code possible, and some safeguards you can use to avoid shipping broken features.

## Where Do We Go From Here?

Focus on what made the business a success in the first place

### Consider Removing Features

### Consider Splitting Out Services

In [part one](http://quickleft.com/blog/ramping-up-developers-on-code), we talked a bit about refactoring your application code. If you find yourself getting into this whole code extraction thing, maybe you want to go whole hog. You can talk with your engineers about whether it makes sense to extract an entire part of your app into a separate service. In many cases, starting a whole new code base can take a lot of time, and might not be worth it.

On the other hand, if it's hard to make changes in what you've already got built, sometimes it can actually speed things up. With a smaller surface area, and code written by people who _currently_ work for your company, it can be easier for folks to grok what's going on. This leads to faster development.

If you do go down the road of extracting services, make sure that your team is clear about the boundaries of the old app and the new one and how they will interface. For example, if you're pulling out a [JSON API](http://fluxusfrequency.github.io/blog/2015/03/01/serving-custom-json-from-your-rails-api-with-activemodel-serializers/), you might want to come up with a sample JSON response and the HTTP Status Codes that will be used _before_ you build it. Defining the limitations and structure beforehand can keep a project like this from dragging on endlessly.

### Use MVP To Figure Out Where To Go Next


## Establish A Clear Development Workflow

### Code Style Guidelines

### TDD

### PRs

### CI

### Staging

### Smoke Testing


## Wrapping Up

Managing your team toward success
