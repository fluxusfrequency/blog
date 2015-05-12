# A Smooth Transition to Ecmascript 6

## Introduction

I'm really excited about the newest version of JavaScript, ECMAScript 6 (ES6). But I'm also terrified. There's already so much to do between mentoring, contributing to open source, and working on the projects that pay the bills.

But as developers, it's our blessing and curse to always be learning. When I think about getting ready to adopt ES6, I feel some anxiety of the thought of having to figure out all of the new patterns and APIs it exposes.

In this post, we'll take a look at XXXXX quick ways to start using ES6 patterns today, in the projects you're already working on. By adding just a little big of ES6 into your coding at a time, it feels a lot less stressful, and you'll become familiar with the new API before you know it.

## Looking Ahead to ES6

First update since 2011 (ES5.1). Was expected to come out in 2013, then pushed back another year twice.
Rollout schedule
Built into Ember CLI

It's a superset of ES5, so just write code the way you always do, until you see a place where you can use one of these new patterns.

## Compatibility

http://kangax.github.io/compat-table/es6/

Mostly unsupported features: WeakMap, WeakSet, Proxy, Reflect, Symbol, new.target, subclassing built-ins
Comprehensions are talked about in ES6, but are more likely to land in ES7 (citation).

## Getting ES6 Into Your Build Process

Instead of waiting of browsers to stop using previous versions of JS (e.g. ES3), so we're transpiling to ES5.

Babel works great with React, features a [built-in JSX transformer](https://babeljs.io/docs/usage/jsx/).

[Babel](https://babeljs.io/) 5.0 was released on Mar 31, 2015.
[babel-runtime](https://www.npmjs.com/package/babel-runtime) Optional transformer that prevents duplication of common functions and sandboxes your code to avoid requiring a global polyfill.

### With Broswerify + NPM Scripts

[Babelify](https://github.com/babel/babelify) for use with Browserify

In `package.json`:

```javascript
{
  "scripts": {
    "postinstall": "browserify --debug --standalong MyApp assets/js/index.js --transform [ babelify --optional babel-runtime ] --outfile build/my-app.js"
  },
  "dependencies": {
    "babel-runtime": "^5.0.12",
    "babelify": "^6.0.2",
    "browserify": "^9.0.7"
  },
  "devDependencies": {
    "eslint": "^0.19.0",
    "eslint-plugin-react": "^2.1.0"
  }
}
```

### Other Build Setups

Grunt, Gulp, Broccoli, RequireJS
[Find them here](https://babeljs.io/docs/using-babel).

### Linting

[eslint](http://eslint.org/)

```javascript
{
  "ecmaFeatures": {
    "modules": true,
    "jsx": true
  },
  "env": {
    "es6": true
  }
}
```

## Small Wins


### Importing and Exporting

### Let

```javascript
let foo = 'bar';
```

### Const

```javascript
const foo = 'bar';
```

### Template Strings

String interpolation

```javascript
let color = 'purple';
`My favorite color is ${color}`
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

### Shorthand Methods

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


## More Complex Stuff

### Iterators

### Generators

### Proxies
Not implemented

### Subclassing Built-Ins
Transpilers mostly don't support

### Tail Calls
Not implemented


## Other



### Misc

Unicode Support, octal and binary literals,


## Resources

[Babel ES6 Overview](https://babeljs.io/docs/learn-es6/)
[ES6 Features](http://es6-features.org/)
[You Don't Know JS](https://github.com/getify/You-Dont-Know-JS/tree/master/es6%20%26%20beyond)
[Backbone with ES6](http://mikefowler.me/2014/06/11/backbone-with-es6/)
