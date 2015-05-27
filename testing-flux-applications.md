# Testing Flux Applications

## Introduction

## Setup

### Project Structure

Tests next to their components

### Use Mocha

### Use Sinon

### Use Karma

karma.conf
```javascript

module.exports = function(config) {
  config.set({
    frameworks: ['mocha', 'browserify'],

    files: [ 'app/**/*-test.js' ],

    preprocessors: {
      'app/**/*.js': [ 'browserify' ]
    },

    browserify: {
      debug: true,
      files: [
        'app/**/*-test.js'
      ],
      transform: [
        ['babelify', { sourceMapRelative: './app' }]
      ]
    },

    browsers: [ 'Chrome' ],

    singleRun: true
  });
};
```

### Use NPM Scripts

package.json:

```json
"scripts": {
  "test": "NODE_ENV=test ./node_modules/karma/bin/karma start"
}
```

`npm test`

### Setting Up Coverage

https://quickleft.com/blog/measuring-clientside-javascript-test-coverage-istanbul/



## Actually Writing Tests

### Testing Actions

- Use Sinon Sandboxes
- Test event dispatching

```javascript
import MyAction from './my-action';
import AppDispatcher from '../dispatchers/app-dispatcher';

it('dispatches an event', function(done) {
  this.spy = this.sinon.spy(AppDispatcher, 'dispatch');

  this.collection.fetch = function() {
    return new Promise(function(resolve) {
      resolve({ length: 16 });
    });
  };

  MyAction.loadModels(this.collection);
  setTimeout(function() {
    sinon.assert.calledOnce(this.spy);
    done();
  }, 0)
});
```

- Get associated constants using `__get__`

```javascript
 beforeEach(function() {
    this.todos = MyAction.__get__('todos');
 });
```

- Test promises with native Promises (ES6)

```javascript
 describe('search', function() {
  beforeEach(function() {
    this.success = new Promise(function(resolve) {
      resolve('results');
    });
    this.failure = new Promise(function(resolve, reject) {
      reject()
    });
    this.searchStub = this.sinon.stub(this.searchClient, 'search')
  });

  context('success', function() {
    beforeEach(function() {
      this.searchStub.returns(this.success);
    });
  });
});
```

### Testing Stores



### Testing Components

#### Wrap Components With StubRouterContext

https://github.com/rackt/react-router/blob/master/docs/guides/testing.md

```javascript
import React from 'react';
import stubRouterContext from '../lib/stub-router-context';
import Search from './search';
let TestUtils = React.addons.TestUtils;

beforeEach(function () {
  let Component = stubRouterContext(Search);
  this.component = TestUtils.renderIntoDocument(<Component/>);
});

it('does the thing when the state is such', function() {
  this.component.refs.stub.setState({
    isLoading: false
  });
});

```
#### Use the React TestUtils

```javascript
it('renders a progress bar', function () {
  let progressBar = TestUtils.scryRenderedDOMComponentsWithClass(this.component, 'progress-bar')
  assert.lengthOf(progressBar, 1);
});

it('updates the search bar query', function () {
  let searchBar = TestUtils.findRenderedDOMComponentWithClass(this.component, 'search-bar');
  let searchQuery = searchBar.getDOMNode().value;

  assert.equal(searchQuery, 'type:story');
});
```
