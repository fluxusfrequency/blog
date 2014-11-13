# Setting Up An Ember Project With Gulp And Browserify

The JavaScript community has been having a conversation about "which framework or library will win" for a long time now. Every day I read articles advocating Backbone, React, Flux, Angular, Ember, Polymer, or no frameworks at all. Whether we'll ever settle on one technology that becomes widely used or continue with the current diversity of frameworks and libraries remains to be seen. As for me, I'm heding my bets, and dabbling in a little of each.

In that spirit, I recently started building my first project in Ember.js. Although Ember boasts a "convention over configuration" approach that makes it easy to get started, I actually found getting set up quite difficult. Although the Ember CLI project exists to make things easier, I wanted to maintain the same control over my project structure and build process that I'm accustomed to having when I build apps in other framworks. In this post, we'll look at building an Ember project using Gulp and Browserify, while avoiding snags that can crop up along the way.

## Defining the Project Structure

I like to build my JS client apps in a single folder one level down from the root of my project. This means that if I'm using Rails or Node to serve an API server for the same project, I can keep these parts of the apps separate. Let's imagine we're writing an app that helps you find where you parked your car. We'll start by creating the folder structure:

```
car-finder
|- client
|- public
|- server
|- test

```

The idea is to do all developent inside of `client`, then use a build task to compile the JS and copy it into the `public` folder` to be served by the backend.

## Configuring Dependencies

To get up and running, we'll need to pull in some dependencies.

Run `npm init` and follow the prompts, then add `browserify`, `browserify-shim`, and the `gulp` packages we'll
need with:

```
npm install --save-dev gulp gulp-browserify browserify-shim gulp-jshint gulp-rename
```

Now that we have `gulp` installed, we'll write a `gulpfile.js` so that the `gulp` command will lint and compile our code, then copy it into the `public` folder. We'll also set up a watch so that any further changes to JS files will trigger a recompile.

```
// gulpfile

var gulp = require('gulp');
var jshint = require('gulp-jshint');
var browserify = require('gulp-browserify');
var rename = require('gulp-rename');

gulp.task('lint', function() {
  return gulp.src('./client/**/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});

gulp.task('browserify', function() {
  return gulp.src('client/index.js')
    .pipe(browserify({
      insertGlobals: true
    }))
    .pipe(rename('car-finder.js'))
    .pipe(gulp.dest('public/javascripts'));
});

gulp.task('watch', function() {
  gulp.watch('client/**/*.js', ['lint', 'browserify']);
});

gulp.task('default', ['lint', 'browserify', 'watch']);

```

In a full-scale production app, I would add other tasks to this file for linting server and test files, compiling LESS, uglifying and minifying JS and CSS, and running tests. For an example of a testing setup, see [AngularJS Unit Testing, For Real Though](http://quickleft.com/blog/angularjs-unit-testing-for-real-though).

## Creating the Project

Let's start building the app. Inside of `public`, make an `index.html` file:

```
// public/index.html
<!DOCTYPE html>
<html>
  <head>
    <title>Car Finder</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <script src="javascripts/car-finder.js"></script>
  </head>
  <body>
    <script type="text/x-handlebars" data-template-name="application">
      <h1>HELLO, WORLD</h1>
    </script>
  </body>
</html>

```
If we boot a server, we want to see that HELLO, WORLD. Let's try it (for now we'll use Python's SimpleHTTPServer).

```
cd public && python -m SimpleHTTPServer 5000
```

Let's visit `localhost:5000` in your browser. Is it there? Nope. We have to hook up the Ember part still. We'll build the skeleton of an Ember app inside of `client`.

```
car-finder
|- client
   |- controllers
      |- index.js
   |- models
      |- index.js
   |- views
      |- index.js
   |- index.js
   |- router.js

```

Inside of index.js, we'll start to set things up. The `index.js` files in the child directories will allow us to require the entire directory with browserify. Then, as we add files to those directories, we can require them from their respective `index.js` files.

```
// client/index.js

window.CarFinder = Ember.Application.create();

require('./controllers');
require('./models');
require('./views');
require('./router');

```

Now if we run `gulp`, we see that a file was copied over to `public/javascripts/car-finder.js`. If we try to load it up in the browser, *BOOM*. One problem: we forgot to add Ember.

## Pulling In Ember

Ember requires jQuery and Handlebars. We'll need to add all three to our project to make it work. Here, we have a couple of options. We can try to install them with NPM, pull them in with Bower, or copy the source files to a `vendor` directory and pull them from there. It would be nice to not have to copy the source every time there's an upgrade, so we should probably use either NPM or Bower. Today, we'll use Bower.

```
npm install --save-dev bower && bower init && bower install --save-dev
ember
```

## Shimming Browserify

## Ember Data

## Up And Running

## Conclusion
