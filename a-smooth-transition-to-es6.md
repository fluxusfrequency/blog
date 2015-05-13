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

Before we get into the nitty-gritty of how to start using ES6, a quick note about compatibility. As of this writing, most JavaScript engines are in the process of implementing the features called for by the ES6 spec. To find out specifically what's available, check out this [compatibility table](http://kangax.github.io/compat-table/es6/).

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

### Let

The new `let` keyword is probably the easiest win that you can possibly get in using ES6. If you do nothing else, just start replacing `var` with `let` everywhere. What's the difference, you ask? Well, `var` is scoped to the closest enclosing _function_, while `let` is scoped to the closest enclosing _block_.

In essence, variables defined with `let` aren't visible outside of `if` blocks and `for` loops, so there's less likelihood for a naming collision. There are other benefits. See [this Stack Overflow answer](http://stackoverflow.com/questions/762011/javascript-let-keyword-vs-var-keyword) for more details.

You can use it pretty much everywhere, but here's a good example of somewhere that it actually makes a difference in preventing a naming collision. The `userName`s inside of the `for` loop don't clash with the current user's `userName` defined just above it.

```javascript
let UserList = React.createClass({
  ...
  // See the Shorthand Object Methods section below
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
// See the Classes section below
class MapView extends Backbone.View {
  const DEFAULT_MAP_CENTER = [48.1667, -100.1667];
}
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

### String & Array Sugar

```javascript
let phrase = 'supercalifragilisticexpialidocious';
phrase.startsWith('cali', 5); // true
phrase.endsWith('s', 33);
phrase.includes('listic');

let sound = 'na';
sound.repeat(10); // 'nananananananananana'
```

```javascript
['a', 'b', 'c', 'd'].find(c => c == 'b'); // 'b'

Array.from('a', 'b', 'c'); // ['a', 'b', 'c']
```

### Argument Defaults

```javascript
function foo(a, b='bar') {
  return b;
}
```

### Rest and Spread

```javascript
function foo(a, ...b) {
  return b;
}
foo('bar', 'baz', 'qux');

// returns ['baz', 'qux']
```

### Shorthand Object Methods

```javascript
let obj = {
  myMethod() {
  }
}
```

### Shorthand Object Properties

```javascript
let obj = {
  myProp
}
```

### The Fat Arrow

```javascript
var mapped = ['a', 'b', 'c'].map(c => c.toUpperCase());

var obj = {
  foo: 'bar',
  links: this.$el.find('a'),
  baz() {
    this.links.forEach(l =>
      console.log(this.foo));
  }
};

```

### Array Destructuring

```javascript
function doStuff(a, b, c) {
  return [a, b, c];
}

let foo, bar, baz = doStuff(1, 2, 3);

```

### For...Of

```javascript
let letters = ['a', 'b', 'c'];
for (var l of letters) {
  console.log(l.toUpperCase());
}
```

### Classes

Fantastic for Backbone

```javascript
class UserView extends Backbone.View {
  constructor(...args) {
    super(args)
  }
}
```

### Promises

```javascript
let firstPromise = new Promise((resolve, reject) => {
  setTimeout(resolve, 100);
};

let secondPromise = new Promise((resolve, reject) => {
  setTimeout(resolve, 200);
};

Promise.all([
  firstPromise(console.log('success!')),
  secondPromise(console.log('oh yeah!'))
])
```


## A Note On Modules

Not that hard to get started, but they have a lot of edge cases.

"So most of the issues are around the fact that ES6 modules have a default export and named exports. CommonJS, AMD just have a single export, and traditionally handle named exports by exporting a single object with the named exports a properties. So the different ES6 module libraries have different ways of reconciling the differences. I’ve mostly used Babel + Browserify which mostly just works… but you pretty much have to use only default exports or named exports if commonjs might use the module. I think a lot of it is just that module systems get complicated."

## Resources

[Babel ES6 Overview](https://babeljs.io/docs/learn-es6/)
[ES6 Features](http://es6-features.org/)
[You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/tree/master/es6%20%26%20beyond)
[Backbone with ES6](http://mikefowler.me/2014/06/11/backbone-with-es6/)
