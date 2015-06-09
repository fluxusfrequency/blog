# A Smooth Transition to Ecmascript 6

## Introduction

I'm really excited about the newest version of JavaScript, ECMAScript 6 (ES6). But I'm also terrified. There's already so much to do between mentoring, contributing to open source, and working on the projects that pay the bills. When will I ever find the time to learn a whole new version of JavaScript?

As developers, it's our blessing and curse to always be learning. When I think about getting ready to adopt ES6, I feel some anxiety of the thought of having to figure out all of the new patterns and APIs it exposes.

In this post, we'll take a look at some quick and painless ways to integrate ES6 into what you're working on today. Hopefully, by adding ES6 patterns into our coding practice just a little at a time, we'll be able to avoid spending a weekend learning the new API when we could be playing outside.

## Looking Ahead to ES6

Ecmascript 6 will be the first update to JavaScript since ES5 was finalized in 2009. ES6 was originally slated to come out in 2013, but was then pushed back a couple more times, and is now expected to be finalized next month, in June 2015.

There's a lot of JavaScript in programming these days. It's found on the server as [Node](https://nodejs.org/), in [OS X Automation](https://developer.apple.com/library/mac/releasenotes/InterapplicationCommunication/RN-JavaScriptForAutomation/), and of course, in all of the web browsers -- where many of us spend most of our time writing JS apps. We'll be writing ES6 in all of these locations before you know it. It's already standard in [Ember CLI](http://www.ember-cli.com/), [Angular 2.0 will be based on it](https://www.airpair.com/angularjs/posts/preparing-for-the-future-of-angularjs), and is [making its way into Node](https://github.com/joyent/node/wiki/ES6-%28a.k.a.-Harmony%29-Features-Implemented-in-V8-and-Available-in-Node) bit by bit.

Thinking ahead, it's clear that JavaScript developers will need to start learning ES6 sooner or later. The good news is, it's a superset of ES5, which means all the ways we currently write code will still work. We can do a slow rollout. We'll just write code the way we always do, until we see places where we can use one of these new patterns.

## Compatibility

Before we get into the nitty-gritty of how to start using ES6, a quick note about compatibility. As of this writing, most JavaScript engines are in the process of implementing the features called for by the ES6 spec. To see a list of the features that are slated for release in ES6 (and how they compare with ES5), check out [this link](http://www.es6features.org). If you want to read about specifically what's available right now, check out this [compatibility table](http://kangax.github.io/compat-table/es6/). In this post, we're going to be focusing on the features that are currently available in the major ES6 engines listed in the table.

There are many features that haven't been rolled out yet, but can be easily _transpiled_(transformed and compiled) to ES5 for immediate use. There are several compilers and polyfills available to help with transpiling. My favorite is [Babel](https://babeljs.io/), formerly called 6to5. Babel 5.0 was released on Mar 31, 2015, and is currently leading other options, with 76% of the spec in place.

Regardless of which transpiler you use, there are several features that are still mostly unsupported across all JS engines. These include: tail calls, WeakMap, WeakSet, Proxy, Reflect, Symbol, new.target, and subclassing built-ins, among others.

Some of these features are available in Babel, but only with experimental mode turned on. For the purposes of this post, we'll be looking at some of the most widely supported, easy-to-use features, so we won't be covering experimental features like these.

We're also going to skip over some of the features that are a little more difficult to get started with, such as iterators, generators, and proxies.

## Getting ES6 Into Your Build Process

Since we can transpile ES6 code to ES5 and start using it everywhere now, it doesn't hurt to get it set up in our build process, so that we can just start using it without having to think twice about it. Luckily, this is really easy to do with Babel.

It's beyond the scope of this post to get into all the variations of what you might encounter in getting ES6 into your build process, but you probably won't have much trouble adding it as a step in your existing build process.

There's a Babel plugin available for just about every build setup, including Grunt, Gulp, and Broccoli, and whether you're using Browserify or RequireJS. It even has a built in [JSX transpiler](https://babeljs.io/docs/usage/jsx/), making it really easy to use with React. There's a full list of build tools and how to use them [on the Babel website] (http://babeljs.io/docs/using-babel).

You may also want to make use of the [babel-runtime](https://babeljs.io/docs/usage/runtime/) package. This is an optional transformer that prevents duplication of common functions during compilation. It also sandboxes your code, aliasing many globals to `core-js` to avoid polluting the global namespace.

### With Browswerify + NPM Scripts

Here's a look at how you might add Babel into an existing client-side JS project, using [Browserify](http://browserify.org/) and the [Babelify](https://github.com/babel/babelify) transform module.

In `package.json`:

```javascript
{
  "scripts": {
    "postinstall": "browserify --debug --standalone MyApp assets/js/index.js --transform [ babelify --optional babel-runtime ] --outfile build/my-app.js"
  },
  "dependencies": {
    "babel-runtime": "^5.0.12",
    "babelify": "^6.0.2",
    "browserify": "^9.0.7"
  }
}
```

Note that by defining the build script under the `postinstall` property, it will be run automatically after the package is installed. This can be nice when uploading your project to your production on [Engine Yard](https://www.engineyard.com/), as it will prevent you from having to explicitly call a build in your deploy script.

## Let's Do It

Now that you've gotten ES6 set up in your build, you can just start writing it whenever you're developing. Or, if you don't feel like it, you can just fall back to ES5. Remember, it's all valid ES6!

I've tried to identify some of the easiest places in a typical front-end [Backbone + React](http://fluxusfrequency.github.io/blog/2015/04/01/integrating-react-with-backbone/) project that you can start using the new patterns right away. Even if that's not your stack, read on! There's something for everyone here.

If you want to try out the examples, you can open up a sandboxed ES6 environment at [http://www.es6fiddle.net/](http://www.es6fiddle.net/).


### Classes, Shorthand Methods and Shorthand Properties

A lot of client-side JS code is object-oriented. If you're [using Backbone](http://mikefowler.me/2014/06/11/backbone-with-es6/), just about every Model, Collection, View or Router you ever write will be a subclass of a core library Class. With ES6, extending these objects is a breeze. We can just call `class MySubclass extends MyClass` and we get object inheritance. We get access to a `constructor` method, and we can call `super` from within any method to apply the parent class's method of the same name. This prevents us from having to write things like `Backbone.Collection.prototype.initialize.apply(this, arguments)`.

We also get some handy shorthands for defining methods and properties. Note the pattern I'm using to call `initialize` instead of `initialize: function(args) {}`:

```javascript
class UserView extends Backbone.View {
  initialize(options) {
    this.options = options;
    super(options);
  }
}
```

We can also define properties using a nice new shorthand. The code below sets an `app` property on the `router` that points to the instance of `App` we create on the second line. In other words, it's the same as doing `this.app = app;`.

```javascript
let App = function() {};   // we'll look at 'let' in just a second.
let app = new App();

class AppRouter extends Backbone.Router {
  app
}

let router = new AppRouter();
```

### Let

The new `let` keyword is probably the easiest win that you can possibly get in using ES6. If you do nothing else, just start replacing `var` with `let` everywhere. What's the difference, you ask? Well, `var` is scoped to the closest enclosing _function_, while `let` is scoped to the closest enclosing _block_.

In essence, variables defined with `let` aren't visible outside of `if` blocks and `for` loops, so there's less likelihood for a naming collision. There are other benefits. See [this Stack Overflow answer](http://stackoverflow.com/questions/762011/javascript-let-keyword-vs-var-keyword) for more details.

You can use it pretty much everywhere, but here's a good example of somewhere that it actually makes a difference in preventing a naming collision. The `userName`s inside of the `for` loop don't clash with the current user's `userName` defined just above it.

```javascript
let UserList = React.createClass({
  ...
  render() {
    let userName = this.props.current
    // See the Fat Arrow section below
    let userComponents = this.props.users.map(user => {
      let userName = user.get('userName');
      return <UserComponent displayName={userName} />;
    });
    return (
    <div className="user-list">
      <h1>Welcome back, {userName}!</h1>
      {userComponents}
    </div>
  }
});
```

### Const

As you might guess from the name, `const` defines a read-only (constant) variable. It should be pretty easy to guess where to use this. For example:

```javascript
const DEFAULT_MAP_CENTER = [48.1667, -100.1667];

class MapView extends Backbone.View {
  centerMap() {
    map.panTo(DEFAULT_MAP_CENTER);
  }
}
```

### The Fat Arrow

You've probably already heard about the fat arrow, or used it before if you've written any CoffeeScript. The fat arrow, written as `=>`, is a new way to define a function. It preserves the value of `this` from the surrounding context, so you don't have to use workarounds like `var self = this;` or `bind`. It comes in really handy when dealing with nested functions, plus it looks really cool.

```javascript

let Toggle = React.createClass({
  componentDidMount() {
    // iOS
    setTimeout(() => {
      var $el = $('#' + this.props.id + '_label');
      $el.on('touchstart', e => {
        let $checkbox = $el.find('input[type="checkbox"]');
        $checkbox.prop("checked", !$checkbox.prop("checked"));
      });
    }, 0);
  },
```

### Template Strings

Do you ever get sick of doing string concatenation in JavaScript? I sure do! Well, good news! We can finally do string interpolation. This will come in very handy all over the place. I'm especially excited about using it in React `render` calls like this:

```javascript
let ProductList = React.createClass({
  ...
  render() {
    let links = this.props.products.map(product => {
      return (
        <li>
          <a href="/products/${product.id}">{product.get('name')}</a>
        </li>
      );
    });

    return(
      <div className="product-list">
        <ul>
          {links}
        </ul>
      </div>
    );
  }
});
```

### String Sugar

It's always been kind of a pain to check for substrings in JS. `if (myString.indexOf(mySubstring) !== -1)`? Give me a break! ES6 finally gives us some sugar to make this a little easier. We can call `startsWith`, `endsWith`, `includes`, and `repeat`.

```javascript
// Clean up all the AngularJS elements

$('body').forEach(node => {
  let $node = $(node);
  if ($node.attr('class').startsWith('ng')) {
    $node.remove();
  }
});

// Pluralize

function Pluralize(word) {
  return word.endsWith('s') ? word : `${word}s`;
}

// Check for spam

let spamMessages = [];

$.get('/messages', function(messages) {
  spamMessages = messages.filter(message => {
    !(message.toLowerCase().includes('viagra'));
  });
});

// Sing the theme song

let sound = 'na';
sound.repeat(10); // 'nananananananananana'

```

### Argument Defaults

Languages like Ruby and Python have long allowed you to define argument defaults in your method/function signatures. With the addition of this feature to ES6, writing Backbone views requires one less line of boilerplate. By setting options to an argument default, we don't have to worry about cases where nothing is passed in. No more `options = options || {};`!

```javascript
class BaseView extends Backbone.View {
  initialize(options={}) {
    this.options = options;
  }
}
```

### Spread and Rest

Sometimes function calls that take multiple arguments can get really messy to deal with. Like when you're calling them from a `bind` that's being triggered by an event listener. For example, check out this event listener from a Backbone view in a recent project I was working on. Because of the method signature of `_resizeProductBox`, I have to pass all those null arguments into `bind` and it gets kind of ugly.

```javascript
class ProductView extends BaseView {
  initialize() {
    this.listenTo(options.breakpointEvents, 'all', _.bind(this._resizeProductBox, this, null, null, true));
  }

  _resizeProductBox(height, width, shouldRefresh) {
    ...
  }
}
```

In ES6, we can clean this up a bit with _spread_. We'll just prepend an array of default arguments with a `...`  to send them through as arguments to the method call.

Here's how you'd do it using _spread_:
```javascript
const BREAKPOINT_RESIZE_ARGUMENTS = [null, null, true];

class ProductView extends BaseView {
  initialize() {
    this.listenTo(options.breakpointEvents, 'all', _.bind(this._resizeProductBox, this, ...BREAKPONT_RESIZE_ARGUMENTS);
  }

  _resizeProductBox(height, width, shouldRefresh) {
    ...
  }
}
```

On the other side of the coin is _rest_, which lets us accept any number of arguments in a method signature instead of at invocation time, as you can do with the _splat_ in Ruby. For example:

```javascript
function cleanupViews(...views) {
  views.forEach(function(view) {
    view.remove();
  });
}
```

### Array Destructuring

Sometimes I find myself having to access all of the elements of an array-like object with square brackets. It's kind of a bummer. Luckily, ES6 lets me use array destructuring instead. It makes it easy to do things like splitting latitude and longitude from an array into two variables, as I do here (note that I also could have used _spread_).

```javascript
let markers = [];

listings.forEach(function (listing, index) {
  let lat, lng = listing.latlng; // looks like: [39.719121, -105.191969]
  let listingMarker = new google.maps.Marker({
    position: new google.maps.LatLng(lat, lng)
  });
  markers.push(listingMarker);
});
```

### Promises

It seems like every project I work on these days uses quite a few promises. Native promises have landed in ES6, so we can all rely on the same API from here on out. Both promise instances and static methods like `Promise.all` are provided. Here's an example of a `userService` from an Angular app that returns a promise from `$http` if the user is online, and a native promise otherwise.

```javascript
angular.module('myApp').factory('userService', function($http, offlineStorage) {
  return {
    updateSettings: function(user) {
      var promise;

      if (offlineStorage.isOffline()) {
        promise = new Promise();
        promise.resolve(user.toJSON());
      } else {
        promise = $http.put('/api/users/' + user.id, user.settings)
        .then(function(result) {
          return result.data;
        });
      }

      return promise.then(data => {
        return new User(data);
      });
    }
  };
});
```

## A Note On Modules

You may have noticed that I didn't cover modules, importing or exporting in this post. Although modules are one of the higher profile features in ES6, and they're easy to get started with, they still seem to have a lot of edge cases that will need to be worked out as ES6 is rolled out.

Specifically, ES6 modules have a `default` export and named exports. CommonJS and AMD only support a single export, and traditionally handle named exports by exporting an object with the named exports as properties. The different ES6 module libraries have different ways of reconciling the differences, so you have to use only default exports or named exports if CommonJS might use the module. How these differences will be reconciled remains to be seen as ES6 sees wider adoption.

If you want to learn more about the module pattern in ES6, take a look at [this overview](https://babeljs.io/docs/learn-es6/#modules).

## Conclusion

In this post, we took a look at some real-world examples of how you would use ES6 in a client-side JavaScript app. We took a quick look at setting up an ES6 transpile step in your build process, and examined many of the easy-to-use features that you can start using right away.

Before you know it, ES6 will be the standard language in use across the web and on servers and personal computers everywhere. Hopefully, this walkthrough will help you get started with a minimum of stress.

If you're feeling excited about ES6, and you want to learn more, I would suggest reading through [this overview](https://babeljs.io/docs/learn-es6/). Or if you're feeling really enthusiastic, try [this book](https://github.com/getify/You-Dont-Know-JS/tree/master/es6%20%26%20beyond).

Until next time, happy coding!

P.S. How do you plan to use these features in your app? Have you discovered a trick made possible by the new features that speed up your workflow? Leave us a comment!


