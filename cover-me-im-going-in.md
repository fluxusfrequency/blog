# Cover Me, I'm Going In! Using Istanbul To Measure Test Coverage In Your JavaScript Project

Testing is a vital part of any application. Whether a project's authors are hoping for scalability, fewer bugs, or just code that stands the test of time, a solid test suite is essential to making those dreams come true.

In the past, I've covered [how to set up a testing framework](PENDING gulp/browserify article PENDING) and [how to test Angular apps](https://quickleft.com/blog/angularjs-unit-testing-for-real-though/).

Today I want to talk about coverage. It's all well and good to write tests, but how do you know that you've tested the code that matters? Did you cover the most important components? Did you test the sad path? The edge cases? What about the zero states?

Using [Istanbul](http://gotwarlost.github.io/istanbul/), you can do a quick sanity check to make sure that you've covered everything you care about. In this article, we'll look at how to get it set up in a typical JS project with a `Node` server and a JS frontend.

## A Note About Metrics

Before we get into the process of setting up Istanbul, I'd like to talk about code coverage as a metric. At Quick Left, we've talked many times about the effects of metrics in programming. We've often reached the conclusion that metrics can become self-fullfilling prophecies, of the most negative kind.

For example, if a project manager or tech lead measures her programmers' effectiveness by counting the lines of code they write, she will not find that those who wrote the most lines are the ones that wrote the best code. In fact, there's a danger that all of the programmers will adopt a style that uses far more lines than necessary to get the same thing done, in order to bump their ranking.

There's a similar danger when a company places a strong emphasis on test coverage as a metric. Imagine that a company adopts a policy that any pull request must increase or maintain the percentage of lines tested. What will be the result? I imagine that in many cases, developers will write good tests to accompany their features an bugfixes.

But what happens if there is a rush and they just want to get the code in? Test coverage tools only count the lines that were hit when the test suite was run, so the developer can just execute the line in question and assert something trivial. The coverage tool will still reflect that line of code as being covered.

Rather than using test coverage as a metric that is required or to measure developer thoroughness, it makes a lot more sense to use coverage as a way of seeing which code and logic branches _aren't_ covered. That information can then be used to prioritize testing goals.

TL;DR: Don't use code coverage to measure what's tested, use it to find out what isn't.

## Time to Test

Let's imagine that we have a clientside JavaScript application written in Angular, Ember, or Backbone. It talks to an API written in Node in the backend. It's compiled with Browserify and built with NPM scripts. This application has been around for a couple of years, and due to business pressures, its authors have only managed to write a handful of tests in both the backend and the frontend. At this point, the app is well-established, and there _is_ a testing setup in place. There's also a lot of code that's untested.

Recently, they closed a funding round, and they're feeling flush. We've been brought in to write some tests. How do we know which code to test first? Well, I would probably talk to the CTO or other technical leader, as they'll probably have the best idea of which workflows are vital to the business. For example, maybe user account upgrades and accepting payments are two things that they are really afraid will fail. I would probably begin by writing integration tests for these workflows using Selenium.

Knowing that integration tests often touch many lines of code, and  because I want to show off, I would first take a snapshot of the current code coverage, so I could brag about how many percentage points I added to the coverage after I wrote those tests.

## Setting up Istanbul

This is where [Istanbul](http://gotwarlost.github.io/istanbul/) comes in. Istanbul is a code coverage tool written in JavaScript by [Krishnan Anantheswaran](https://github.com/gotwarlost) of Yahoo!. It can be a little tricky to set up, so let's take a look at how to do it.

There are basically four steps:

1. Instrument the source with Istanbul while building.
2. Run `mocha-phantomjs`, passing in a hooks argument.
3. Use a `phantomjs` hook file to write out the results when it's complete.
4. Run the `Istanbul` cli to generate the full report.

Let's get started.

### 1. Instrument the Source

We'll need to find a way to run our code through Istanbul after it's been compiled, so the first step is to set up an NPM task that will pipe compiled code into a tool like [browserify-istanbul](https://github.com/devongovett/browserify-istanbul).

A panacea of other Istanbul NPM packages exist for instrumenting code (and many other purposes), including [browserify-gulp](https://github.com/SBoudrias/gulp-istanbul), [grunt-istanbul-reporter](https://github.com/justspamjustin/grunt-istanbul-reporter), and [borschik-tech-istanbul](https://github.com/bem/borschik-tech-istanbul).

Since we are using Browserify, we already have NPM tasks in place to compile our code for development/production or test. Here's what they look like. Note that the `-o` option to `browserify` specifies the output file for the build and the `-d` option turns on debugging.

In `package.json`:

```javascript
{
  ...
  "scripts": {
    ...
    "build": "browserify ./js/main.js -o html/static/build/js/myapp.js",
    "build-test": "browserify -r handlebars:hbsfy/runtime ./js/test/index.js -o html/static/build/js/test.js -d --verbose"
  }
  ...
}
```

We can use the `build-test` task we've already defined as a template for a new `build-test-coverage` task. First, make sure you pull in `browserify-istanbul` with `npm install --save-dev browserify-istanbul`. Then, write the task in `package.json`.

We'll ignore the Handlebars templates and Node modules when we load everything into Istanbul. We'll also use the `-t` option to Browserify, which causes it to use a [transform module](https://github.com/substack/module-deps#transforms).

```javascript
{
  ...
  "scripts": {
    ...
    "build-test-coverage": "mkdir -p html/static/build/js/ && browserify -r handlebars:hbsfy/runtime -t [ browserify-istanbul --ignore **/*.hbs **/bower_components/** ] ./js/test/index.js -o html/static/build/js/test.js -d"
  }
  ...
}
```

With the `browserify-istanbul` package and the `build-test-coverage` script in place, we've got our code instrumented, and we're ready to move on to step two.

### 2. Run mocha-phantomjs, Passing in a Hooks Argument

Now that the code is all built and ready to go, we need to pass it into a test framework. We'll use [mocha-phantomjs](https://github.com/metaskills/mocha-phantomjs), spawning a child process in a cli script that we'll create in a cli script.

This would also be a good place to use [gulp-mocha-phantomjs](https://github.com/mrhooray/gulp-mocha-phantomjs) or [grunt-mocha-phantomjs](https://github.com/jdcataldo/grunt-mocha-phantomjs) if you're using those build tools instead of a script.

We'll pass in a hooks argument that specifies a `phantom_hooks.js` file we've yet to write.

In `js/test/cli.js`:

```javascipt
#!/usr/bin/env node

var spawn = require('child_process').spawn;

var child = spawn('mocha-phantomjs', [
  'http://localhost:9000/static/js/test/index.html',
  '--timeout', '25000',
  '--hooks', './js/test/phantom_hooks.js'
]);

child.on('close', function (code) {
  console.log('Mocha process exited with code ' + code);
  if (code > 0) {
    process.exit(1);
  }
});

```

Now we've got a way to put our Istanbulified code into PhantomJS, so we'll move to step 3.

### 3. Use a PhantomJS Hook File to Write the Results

In `js/test/phantom_hooks.js`:

```javascript
module.exports = {
  afterEnd: function(runner) {
    var fs = require('fs');
    var coverage = runner.page.evaluate(function() {
      return window.__coverage__;
    });
    if (coverage) {
      console.log('Writing coverage to coverage/coverage.json');
      fs.write('coverage/coverage.json', JSON.stringify(coverage), 'w');
    } else {
      console.log('No coverage data generated');
    }
  }
};
```

### 4. Run the Istanbul CLI to Generate the Full Report

```javascript
{
  ...
  "scripts": {
    ...
    "coverage-report": "istanbul report --root coverage lcov"
  }
  ...
}
```

## Wrapping Up
