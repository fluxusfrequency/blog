# Integrating React With Backbone

So many JS frameworks! [It can get tiring](http://www.allenpike.com/2015/javascript-framework-fatigue/).

Like any developer who writes JavaScript, I try to keep abreast of the trends. I like to tinker with new things, and rebuild [TodoMVC]() as often as possible.

When it comes to choosing frameworks for a project, though most of the things coming out just haven't been battle-tested enough for me to recommend to clients. There are very few that I will recommend.

Like much of the community, I feel pretty confident in the future of [React](http://facebook.github.io/react/index.html). It makes reasoning about data easy, forces me to keep logic out of my templates, and it's performant.

Since React only provides the "view" part of an [MVC application](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller), I still have to find a way to wrap the rest of the application. When it comes to building a project that I'm confident in, I still reach for [BackboneJS](http://backbonejs.org/). A company that bets on Backbone won't have trouble finding people who can work on their code base. It's been around for a long time, is unopionated enough to be adaptable to many different situations. As an added bonus, it plays well with React.

In this post, we'll take a look at one way to structure a Backbone app with React handling the logic of the view layer.

## A Note About Setting Up Dependencies

I won't go over setting up all of the package dependencies for the project here, since I've covered this sort of thing in [a previous post](http://fluxusfrequency.github.io/blog/2015/02/04/setting-up-a-client-side-javascript-project-with-gulp-and-browserify/). For the purposes of this article, you can assume that we're using [Browserify]().

One package that is worth noting here is [ReactBackbone](https://github.com/clayallsopp/react.backbone). We'll use it to trigger an automatic update of our components when Backbone model or collection data changes. You can get it with `npm install --save react.backbone`.

## Project Structure

There are many ways to structure the directories for a client-side JS application, and every project lends itself to a slightly different setup. Today we'll be basing our directory structure in a fairly typical fashion for a Backbone project. But, we'll also be introducing the concept of _screens_ to our application, so things will look slightly different than usual. Here's what we'll need:

```
assets/
  |- js/
     |- collections/
     |- components/
     |- controllers/
     |- models/
     |- screens/
     |- vendor/
     |- app.js
     |- base-view.js
     |- index.js
     |- router.js
```

Much of this is standard Backbone boilerplate. The `collections/`, `models/`, and `vendor/` directories are self-explanatory, as is the router. We'll store reusable UI components, such as pagination, pills, and toggles, in`components/`.
