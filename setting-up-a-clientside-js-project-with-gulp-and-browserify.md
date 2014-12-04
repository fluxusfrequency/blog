# Setting Up A Clientside JavaScript Project With Gulp And Browserify

It's hard to keep up.

Although Grunt and Requirejs remain viable and useful tools, it's good to explore the new kids on the block as well. Have you haven't had a chance to try Gulp or Browserify yet? If not, let's get our feet wet! These tools are great for use with Backbone, Angular, Ember, React, or your own hand-rolled JavaScript project.

In this post, we'll explore how to set up a clientside JavaScript project for success using Gulp and Browserify.

## Defining the Project Structure

Let's imagine we're writing an app that helps you find where you parked your car. If you want to follow along, check out the [code on GitHub](https://github.com/fluxusfrequency/car-finder).

When I'm building a full application that includes both an API server and a clientside JavaScript app, there's a certain project structure that I've found works well for me. I like to put my clientside code in a single folder one level down from the root of my project, called `client`. This folder will have sibling folders named `server`, `test`, `public`, and `build`. Most of these are self-explanatory. The `build` folder is where we'll put the resulting files created by Browserify builds. Here's how this would look:

```
car-finder
|- build
|- client
   |- less
|- public
   |- javascripts
   |- stylesheets
|- server
|- test

```

The idea is to do all developent inside of `client`, then use a build task to compile the JS and copy it into the `public` folder` to be served by the backend.

## Configuring Dependencies

To get up and running, we'll need to pull in some dependencies.

Run `npm init` and follow the prompts, then add `browserify`, `browserify-shim`, and the `gulp` packages we'll
need with:

```bash
npm install --save-dev gulp gulp-browserify browserify-shim gulp-jshint gulp-mocha-phantomjs \
gulp-rename gulp-uglify gulp-less gulp-autoprefixer gulp-minify-css mocha chai
```

If you're using git, you may want to ignore your `node_modules` folder with `echo "node_modules" >> .gitignore`.

You'll probably want to use `browserify-shim` to shim jQuery and your JavaScript framework. To do that, you just set up your `package.json` like so. Now you can do `var $ = require('jquery')`. The process is the same to add any other library.

```json
{
  "name": "car-finder",
  "author": "Ben Lewis",
  "devDependencies": {
    "gulp-rename": "^1.2.0",
    "gulp": "^3.8.10",
    "gulp-mocha-phantomjs": "^0.5.1",
    "gulp-jshint": "^1.9.0",
    "gulp-browserify": "^0.5.0",
    "browserify": "^6.3.4",
    "browserify-shim": "^3.8.0",
    "mocha": "^2.0.1",
    "gulp-minify-css": "^0.3.11",
    "gulp-uglify": "^1.0.1",
    "gulp-autoprefixer": "^2.0.0",
    "gulp-less": "^1.3.6",
    "chai": "^1.10.0"
  },
  "browserify-shim": {
    "jquery": "$"
  },
  "browserify": {
    "transform": [
      "browserify-shim"
    ]
  }
}
```

If you're getting JSHint errors in your editor for this file, you can
turn them off with `echo "package.json" >> .jshintignore`.

## Setting Up Gulp

Now that we have `gulp` installed, we'll configure `gulp` commands to lint our code, test it, run a compilation process, and copy our minified JS into the `public` folder.  We'll also set up a watch so that any changes to our project will trigger a lint and recompile.

We'll start by requiring the gulp packages we want in a `gulpfile.js` that lives in the root of the project.

```javascript
// Gulp Dependencies
var gulp = require('gulp');
var rename = require('gulp-rename');

// Build Dependencies
var browserify = require('gulp-browserify');
var uglify = require('gulp-uglify');

// Style Dependencies
var less = require('gulp-less');
var prefix = require('gulp-autoprefixer');
var minifyCSS = require('gulp-minify-css');

// Development Dependencies
var jshint = require('gulp-jshint');

// Test Dependencies
var mochaPhantomjs = require('gulp-mocha-phantomjs');
```

Now we can start defining some gulp tasks.

### JSHint

To set up linting for our clientside code as well as our test code, we'll add the following to the `gulpfile`:

```javascript
gulp.task('lint-client', function() {
  return gulp.src('./client/**/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});

gulp.task('lint-test', function() {
  return gulp.src('./test/**/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});
``

We'll also need to define a `.jshintrc` in the root of our project, so that JSHint will know which rules to apply. If you have a JSHint plugin turned on in your editor, it will show you any linting errors as well. I use [jshint.vim](https://github.com/wookiehangover/jshint.vim). Here's an example of a typical `.jshintrc` for one of my projects. You'll notice that it has some predefined globals that we'll need to have defined for our testing environment to work.

```json
{
  "camelcase": true,
  "curly": true,
  "eqeqeq": true,
  "expr" : true,
  "forin": true,
  "immed": true,
  "indent": 2,
  "latedef": "nofunc",
  "newcap": false,
  "noarg": true,
  "node": true,
  "nonbsp": true,
  "quotmark": "single",
  "undef": true,
  "unused": "vars",
  "trailing": true,
  "globals": {
    "after"      : false,
    "afterEach"  : false,
    "before"     : false,
    "beforeEach" : false,
    "browser"    : false,
    "context"    : false,
    "describe"   : false,
    "it"         : false,
    "window"     : false
  }
}
```

### Mocha

I'm a firm believer in Test-Driven Development, so one of the first things I always do when setting up a project is to make sure I have a working testing framework. For clientside unit testing, I like to use `gulp-mocha-phantomjs`. We'll need to define a few things in the test folder.

We'll need to create a `test/client/index.html` file for Mocha to load up in the headless PhantomJS browser environment. It will pull Mocha in from our `node_modules` folder, require a `build/client-test.js` file (more on this in a minute), then run the scripts:

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mocha Test Runner</title>
    <link rel="stylesheet" href="../../node_modules/mocha/mocha.css">
  </head>
  <body>
    <div id="mocha"></div>
    <script src="../../node_modules/mocha/mocha.js"></script>
    <script>mocha.setup('bdd')</script>
    <script src="../../build/client-test.js"></script>
    <script>
      if (window.mochaPhantomJS) {
        mochaPhantomJS.run();
      } else {
        mocha.run();
      }
    </script>
  </body>
</html>
```

## Setting Up Browserify

Now we need to set up browserify to work the way we want. First, we'll define a couple of Gulp tasks: one to build the app, and one to build the tests. We'll copy the compiled app file to `public` so we can serve it unminified in development, and we'll also put a copy into `build`, where we'll grab it for minification. The test file will also be put into `build`. Finally, we'll set up a `watch` task to trigger rebuilds of the app and test builds when one of the source files changes.

```javascript
gulp.task('browserify-client', ['lint-client'], function() {
  return gulp.src('client/index.js')
    .pipe(browserify({
      insertGlobals: true
    }))
    .pipe(rename('car-finder.js'))
    .pipe(gulp.dest('build'));
    .pipe(gulp.dest('public/javascripts'));
});

gulp.task('browserify-test', ['lint-test'], function() {
  return gulp.src('test/client/index.js')
    .pipe(browserify({
      insertGlobals: true
    }))
    .pipe(rename('client-test.js'))
    .pipe(gulp.dest('build'));
});

gulp.task('watch', function() {
  gulp.watch('client/**/*.js', ['browserify-client']);
  gulp.watch('test/client/**/*.js', ['browserify-test']);
});

```

There's one more thing we'll need to do before we can run our Gulp tasks, which is to make sure we actually have `index.js` files in each of the folders we've told Gulp to look at. Add one to the `client` and `test/client` folders.

Now, we should be able to run `gulp browserify-client` and see new `build/car-finder.js` and `public/javascripts/car-finder.js` files. In the same way, `gulp browserify-test` should create a `build/client-test.js` file.

## More Testing

Now that we have Browserify set up, we can finish getting our test environment up and running. Let's define a `test` Gulp task and add it to our `watch`. Since we can now add the `browserify-test` task as a dependency for the `test` task, our `watch` will just run `test`, and the test files will be browserified automatically. We should also update our watch to run the tests whenever we change any of the app _or_ test files.

```javascript
gulp.task('test', ['lint-test', 'browserify-test'], function() {
  return gulp.src('test/client/index.html')
    .pipe(mochaPhantomjs());
});

gulp.task('watch', function() {
  gulp.watch('client/**/*.js', ['browserify-client', 'test']);
  gulp.watch('test/client/**/*.js', ['test']);
});
```

To verify that this is working, let's write a simple test in `test/client/index.js`:

```javascript
var expect = require('chai').expect;

describe('test setup', function() {
  it('should work', function() {
    expect(true).to.be.true;
  });
});
```

Now, when we run `gulp test`, we should see Gulp run the `lint-test`, `browserify-test`, and `test` tasks and exit with one passing example. We can also test the `watch` task by running `gulp watch`, then making changes to `test/client/index.js` or `client/index.js`. Changes to any of the files in those folders should trigger the tests.

## Building Assets

Now that we have our app and our tests up and running, let's turn our attention to the rest of our build process. I like to use `less` for styling. We'll need a `styles` task to compile it down to CSS. In the process, we'll use [gulp-autoprefixer]() so that we don't have to write vendor prefixes in our CSS3 rules. As we did with the app, we'll create a development copy and a build copy, and place them in `public/stylesheets` and `build`, respectively. We'll also add this as to our `watch`, so changes to our styles will get picked up.

We should probably uglify our JavaScript files too. We'll write tasks for minification and uglification, then copy the minified production versions of the files to `public/stylesheets` and `public/javascripts`. Finally, we'll wrap it all up into a `build` task.

Here are the changes to the `gulpfile`:

```javascript
gulp.task('styles', function() {
  return gulp.src('client/less/index.less')
    .pipe(less())
    .pipe(prefix({ cascade: true }))
    .pipe(rename('car-finder.css'))
    .pipe(gulp.dest('build'))
    .pipe(gulp.dest('public/stylesheets'));
});

gulp.task('minify', ['styles'], function() {
  return gulp.src('build/car-finder.css')
    .pipe(minifyCSS())
    .pipe(rename('car-finder.min.css'))
    .pipe(gulp.dest('public/stylesheets'));
});

gulp.task('uglify', ['browserify-client'], function() {
  return gulp.src('build/car-finder.js')
    .pipe(uglify())
    .pipe(rename('car-finder.min.js'))
    .pipe(gulp.dest('public/javascripts'));
});

gulp.task('build', ['uglify', 'minify']);
```

If we now run `gulp build`, we should see these new files appear:
- `build/car-finder.css`
- `public/javascripts/car-finder.min.js`
- `public/stylesheets/car-finder.css`
- `public/stylesheets/car-finder.min.css`


## Did It Work?

We'll want to check that what we've built is actually going to work.  Let's add a little bit of styling and JS code to make sure it's all getting compiled and served the way we hope it is. We'll start with an `index.html` file in the `public` folder. It will load up the development versions of our CSS and JS files, for better debugging in development. We would want to change these to use the minified versions for production.

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Car Finder</title>
    <link rel="stylesheet" href="stylesheets/car-finder.css">
  </head>
  <body>
    <script src="javascripts/car-finder.js"></script>
  </body>
</html>
```

We'll add some styling in `client/less/index.less`:

```less
body {
  background-color: DarkOliveGreen;
}
```

Now we'll write our million dollar app in `client/index.js`:

```javascript
alert('I found your car!');
```

Let's put it all together. Run `grunt build`, then `open public/index.html`. Our default browser opens a beautiful olive green screen with an alert box. Profit!

## Wrapping Up

We've now set up our project to use Browserify to take the headache out of requiring modules nad dependencies, and Gulp to make linting, testing, `less` compilation, minification, and uglification a breeze. I like to tie it all together with a `default` Gulp task, so all I have to do is run `gulp` to check that everything's going together the way I expect, and start watching for changes. Since `test` already does the linting and browserifying, and we'll want to run the tests, all we really need here is `test`, `build`, and `watch`.

```javascript
gulp.task('default', ['test', 'build', 'watch']);
```

I hope that this exploration of Gulp and Browserify has been enlightening. I personally love these tools, and at the moment they're my defaults when creating a personal project. Hopefully this post will help you set up a successful project.

P.S. What do you think? Going to make the switch from Grunt and/or Requirejs? Think these tools are inferior? Leave a comment below.
