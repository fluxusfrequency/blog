# Testing Angular.js - Deal With It

## Introduction

When it comes to contemporary web development, AngularJS is the new hotness. Its
unique approach to HTML compilation and two-way data binding make it
an effective tool for efficiently building client-side web apps. When
I found out I would be using it to build a production app for one of our
clients here at Quick Left, I was excited to learn as much about it as
I could. I scoured the interwebs for every tutorial and walkthrough
I could find. They were really helpful in understanding directives,
template compilation, and the event loop. But when it came to the testing, I
found that the topic was often hand-waved.

I was trained to practice Test-Driven Development, and I feel like
something's out of place whenever I'm out of the "Red-Green-Refactor" flow.
Since we had no idea how to do effective TDD in Angular, the first month
of the project we were practicing "test-after" development. At about this point,
I started to feel itchy, so I focused on figuring out testing.
I sprinted on it for a week, and we had soon gone from about 40% test coverage
to 86% (By the way, if you haven't tried it yet, check out [Istabul]()
for checking out your test coverage in JS apps).

Today I'd like to share some things I learned along the way.
As good as the [Angular docs]() are, testing a production app is rarely as
simple as the examples you'll find there. There are a lot of gotchas that pop
up along the way, and I had to struggle my way through figuring out how
to make things work. I found several workarounds that came in handy time
and time again. In this article, we're going to look at some of them:

1. Reusable e2e pages
2. Dealing with functions that return a promise
3. Mocking controller and directive dependencies
4. Accessing child and isolate scopes

This article is written for intermediate to advanced developers using
AngularJS to build production applications, who would like to reduce some
of the pain of testing. It is my hope that feeling secure in testing workflow
will enable the reader to practice a TDD workflow and build a more solid app.

## Test Tools

There are many test frameworks and tools available to the Angular
developer, and you may already have preferences around tooling. Here's
the setup that we chose, and we'll be using for the rest of this article:

- *Karma*: The official AngularJS team test runner. We'll use it to launch
  Chrome, Firefox, and PhantomJS.

- *AngularMocks*: Provides support for injecting and mocking Angular services in
  unit tests.

- *Protractor*: The feature testing tool for AngularJS, which launches
  your app in a browser and interacts with it via Selenium.

- *Mocha*: A node.js based test framework. Gives us the ability to write
  `describe` blocks and make assertions.

- *Chai*: Assertion library that hooks into Mocha, and gives access to
  Behavior-Driven Development assertions like `expect`, `should`, and
  `assert`. We'll use `expect`.

- *Chai-as-promised*: This Chai plugin is really helpful for dealing with
  function calls that return a promise. It gives us the ability to say
  things like: `expect(foo).to.be.fulfilled`, or
  `expect(foo).to.eventually.equal(bar)`.

- *Sinon*: Stubbing and mocking library. We'll use it to mock out directive
  and controller dependencies in unit tests, and check that functions
  were being called with the correct arguments.

- *Browserify*: Allows us to easily require modules of code between files
  in the project.

- *Partialify*: Allows us to require HTML templates inline in our
  AngularJS directives.

- *Lodash*: Utilities used to extend JavaScript and make it easier to work
  with.

## Setting Up A Test Helper

We'll start by creating a test helper that will load in the necessary
dependencies. Here, I'm pulling in Angular Mocks, Chai,
Chai-as-promised, and Sinon. In the `beforeEach` and `afterEach`
functions, I'm using Sinon.sandbox to make sure I have a clean slate, empty of
mocks, before each test.

```test/test-helper.js
require('widgetProject');
require('angular-mocks');

var chai = require('chai');
chai.use('sinon-chai');
chai.use('chai-as-promised');

var sinon = require('sinon');

beforeEach(function() {
  this.sinon = sinon.sandbox.create();
});

afterEach(function() {
  this.sinon.restore();
});

module.exports = {
  rootUrl: 'http://localhost:9000',
  expect: chai.expect
}
```

## Getting Started: Top Down Testing

I'm a big proponent of a top-down testing style. Starting with a feature
that I know I want to build, I like to write a pseudo-gherkin scenario
describing the desired behavior and translate it into a feature test. I run
that test and let it fail. I then begin building all the parts of the system
that I need to make the feature work, using unit tests to guide me as I go.

Given this workflow, I thought I would start here with a discussion of
e2e testing. To start things off, I'll describe a pattern we found useful in e2e
testing: creating a reusable "page" file. For this example, we'll
imagine that we're working on a form to create a new widget.

## Reusable e2e Test Pages

When working on a one-page app, it makes sense to DRY up the feature tests by
writing a reusable "page" that you can reference from within multiple e2e tests.

There are many ways to structure the tests in an Angular project. Today,
we'll go with this setup:

```
widgets-project
|-test
|  |
|  |-e2e
|  |  |-pages
|  |
|  |-unit
```

Inside of the `pages` folder, we'll create a `WidgetsPage` function that
we can require into our e2e tests. It has references to a list of
widgets in an Angular repeater, a `firstWidget` property that we can use
to check out the data in the first widget, and the fields and submit
button in a new widget form. In the end, it looks like this:

```test/e2e/pages/widgets-page.js

var helpers = require('../../test-helper');

function WidgetsPage() {
  this.get = function() {
    browser.get(helpers.rootUrl + '/widgets');
  }

  this.widgetRepeater = by.repeater('widget in widgets');
  this.firstWidget = element(this.widgetRepeater.row(0));

  this.widgetCreateForm = element(by.css('.widget-create-form'));
  this.widgetCreateNameField = this.widgetCreateForm.element(by.model('widget.name');
  this.widgetCreateSubmit = this.widgetCreateForm.element(by.buttonText('Create');
}

module.exports = WidgetsPage

```

From within my e2e tests, I can now load up this page and interact with
the elements on it. Here's how I would use it in a test for the widget
create form:

```e2e/widgets_test.js

var helpers = require('../test-helper');
var expect = helpers.expect;
var WidgetsPage = require('./pages/widgets-page');

describe('creating widgets', function() {
  beforeEach(function() {
    this.page = new WidgetsPage();
    this.page.get();
  });

  it('should create a new widget', function() {
    expect(this.page.firstWidget).to.be.undefined;
    expect(this.page.widgetCreateForm.isDisplayed()).to.eventually.be.true;
    this.page.widgetCreateNameField.sendKeys('New Widget');
    this.page.widgetCreateSubmit.click();
    expect(this.page.firstWidget.getText()).to.eventually.equal('Name: New Widget');
  });
});

```

Let's step through what's happening here. First, we load up the test
helpers and get `expect`and the reusable `WidgetsPage` from them.
In the `beforeEach`, we load up the page in the browser.
Then, in the example, we use the page elements we defined in the
`WidgetsPage` to interact with the page. We check that there
are no widgets, then fill out the form to create one named "New Widget",
and check that it is displayed on the page.

## Dealing With Functions That Return a Promise

The assertions we get from protractor in the test above return promises, so we use
Chai-as-promised to check that functions like `isDisplayed` and
`getText` return what we expect _after_ they're resolved.

By splitting the logic for the form out into a reusable "page", we can
now reuse it to test form validations or custom form directives later on.

We can also deal with promises inside of unit tests. Take a look at this
example, in which we test a modal that can be used to edit an existing widget.
It makes use of the UI Bootstrap `$modal` service. When a user opens the
modal, this service returns a promise. When she saves or cancels the modal,
the promise is resolved or rejected. Here, we'll test that the `save`
and `cancel` methods are properly hooked up, again using
Chai-as-promised.

```widget-editor-service.js
var angular = require('angular');
var _ = require('lodash');

angular.module('widgetProject.widgetEditor').service('widgetEditor', function (
  $modal,
  $q,
  $templateCache
) {
  return function(widgetObject) {
    var deferred = $q.defer();

    var templateId = _.uniqueId('widgetEditortemplate');
    $templateCache.put(templateId, require('./widget-editor-template.html'));

    var dialog = $modal({
      template: templateId
    });

    dialog.$scope.widget = widgetObject;

    dialog.$scope.save = function() {
      // Do some saving things
      deferred.resolve();
      dialog.destroy();
    });

    dialog.$scope.cancel = function() {
      deferred.reject();
      dialog.destroy();
    });

    return deferred.promise;
  };
});

```

This service loads the widget editor template into the template cache,
loads a widget into it, and and sets up a deferred object that will be
resolved or rejected depending on whether the user saves or cancels from
the editor. It returns a promise from the deferred.

Here's how you might test something like this:

```test/unit/widget-editor-directive_test.js

var angular = require('angular');
var helpers = require('../test_helper');
var expect = helpers.expect;

describe('widget storage service', function() {
  beforeEach(function() {
    var self = this;

    self.modal = function() {
      return {
        $scope: {},
        destroy: self.sinon.stub()
      }
    }

    angular.mock.module('widgetProject.widgetEditor', { $modal: self.modal });
  });

  it('should persist changes when the user saves', function(done) {
    var self = this;

    angular.mock.inject(function(widgetModal, $rootScope) {
      var widget = { name: 'Widget' };
      var promise = widgetModal(widget);

      self.modal.$scope.save();

      // Somehow test that the widget was saved
      expect(self.modal.destroy).to.have.been.called;
      expect(promise).to.be.fulfilled.and.notify(done);

      $rootScope.$digest();
    });
  });

  it('should not save when the user cancels', function(done) {
    var self = this;

    angular.mock.inject(function(widgetModal, $rootScope) {
      var widget = { name: 'Widget' };
      var promise = widgetModal(widget);

      self.modal.$scope.cancel();
      expect(self.modal.destroy).to.have.been.called;
      expect(promise).to.be.rejected.and.notify(done);

      $rootScope.$digest();
    });
  });
});
```

In the test for the widget editor, there are a few things we have to do
to deal with the complexity of the promise that the modal returns.
First, we build a mock `$modal` service in the `beforeEach` function,
replacing it with a function that returns `$scope` as an empty object,
and stubs `destroy`. In `angular.mock.module`, we pass this modal double
into the options to get Angular Mocks to use it instead of the real
`$modal` service. This pattern is extremely useful in stubbing out
dependencies, as we'll discover shortly.

There are two examples here, and each has to wait for the promise
returned by the widget editor to be resolved before it can be completed.
Because of this, we have to pass `done` as parameter to the example
itself, and `notify(done)` when the test is complete.

Within the tests, we use Angular Mocks again to inject the widget modal
and the AngularJS`$rootScope` service into the test. Having `$rootScope` gives
us the ability to trigger a `$digest` loop. In each of the tests, we load up
the modal, cancel or reject it, and use Chai-as-expected to test whether the
promise returned was `rejected` or `resolved`. To trigger the actual promise
resolution and call `destroy`, we have to have a `$digest` loop, so we
do that at the end of each assertion as well.

We've now looked at how to deal with promises in both e2e and unit
tests, using these assertions:
- `expect(foo).to.eventually.equal(bar)`
- `expect(foo).to.be.fulfilled`
- `expect(foo).to.be.rejected`

## Mocking Controller and Directive Dependencies

In the previous example, we had a service that relied on the `$modal`
service, which we mocked out so that we could ensure that `destroy` was
being called. The pattern we used to get that hooked up is very useful in
getting unit tests to work properly in Angular.

The pattern is as follows:

1. Set `var self = this` in the beforeEach block.
2. Build a double and stub its methods, then make it a property of the
  `self` object:

  ```
  self.dependency = {
    dependencyMethod: self.sinon.stub()
  }
  ```
3. Pass your doubles into the module under test:

  ```
  angular.mock.module('mymodule', {
    dependency: self.dependecy,
    otherDependency: self.otherDependency
  });
  ```
4. Check that the mocked methods within your test examples. You can use
   `expect(foo).to.have.been.called.withArgs`, passing in the arguments
   you expect, for more precise coverage.

Sometimes directives or controllers depend on many external and internal
dependencies, and you need to mock them out. Here's a more complicated example,
in which directive watches a `widgetStorage` service and updates the
widgets in its scope whenever the collection changes. There's also
an `edit` method that opens the `widgetEditor` we created above.

```widget-viewer-directive.js

var angular = require('angular');

angular.module('widgetProject.widgetViewer').directive('widgetViewer', function(
  widgetStorage,
  widgetEditor
) {
  return {
    restrict: 'E',
    template: require('./widget-viewer-template.html'),
    link: function($scope, $element, $attributes) {
      $scope.$watch(function() {
        return widgetStorage.notify;
      }, function(widgets) {
        $scope.widgets = widgets;
      });

      $scope.edit = function(widget) {
        widgetEditor(widget);
      });
    }
  };
});

```

Here's how we might test something like this, mocking out the
dependencies on `widgetStorage` and the `widgetEditor`:

```test/unit/widget-viewer-directive_test.js

var angular = require('angular');
var helpers = require('../test_helper');
var expect = helpers.expect;

describe('widget viewer directive, function() {
  beforeEach(function() {
    var self = this;

    self.widgetStorage = {
      notify: self.sinon.stub()
    };

    self.widgetEditor = self.sinon.stub();

    angular.mock.module('widgetProject.widgetViewer', {
      widgetStorage: self.widgetStorage,
      widgetEditor: self.widgetEditor
    });
  });

  // The rest of the test...
});
```

## Accessing Child and Isolate Scopes

Sometimes you want to write a directive that has an isolate or child
scope. It can be a pain to try to access these from within your tests.
Knowing about `self.element.isolateScope()` is the key to solving this
problem. Here's an example that relies on the Angular Strap `$dropdown`
service, which creates an isolate scope:

```nested-widget-directive.js
var angular = require('angular');

angular.module('widgetSidebar.nestedWidget').directive('nestedSidebar', function(
  $dropdown,
  widgetStorage,
  widgetEditor
) {
  return {
    restrict: 'E',
    template: require('./widget-sidebar-template.html'),
    scope: {
      widget: '='
    },
    link: function($scope, $element, $attributes) {
      $scope.actions = [{
        text: 'Edit',
        click: 'edit()'
      }, {
        text: 'Delete',
        click: 'delete()'
      }]

      $scope.edit = function() {
        widgetEditor($scope.widget);
      });

      $scope.delete = function() {
        widgetStorage.destroy($scope.widget);
      });
    }
  };
});

```

Assuming this directive inherits the widget from a parent directive that
has a collection of widgets, it can be tough to get ahold of the child scope
to check its properties. But it can be done. Here's how:

```test/unit/nested-widget-directive_test.js
var angular = require('angular');
var helpers = require('../test_helper');
var expect = helpers.expect;

describe('nested widget directive', function() {
  beforeEach(function() {
    var self = this;

    self.widgetStorage = {
      destroy: self.sinon.stub()
    };

    self.widgetEditor = self.sinon.stub();

    angular.mock.module('widgetProject.widgetViewer', {
      widgetStorage: self.widgetStorage,
      widgetEditor: self.widgetEditor
    });

    angular.mock.inject(function($rootScope, $compile, $controller) {
      self.parentScope = $rootScope.new();
      self.childScope = $rootScope.new();

      self.compile = function() {
        self.childScope.widget = { id: 1, name: 'widget1' };
        self.parentElement
        = $compile('<widget-organizer></widget-organizer>')(self.parentScope);

        self.parentScope.$digest();

        self.childElement = angular.element('<nested-widget
        widget="widget"></nested-widget>');

        self.parentElement.append(self.childElement);

        self.element = $compile(self.childElement)(self.childScope);
        self.childScope.$digest();
      });
    });

    self.compile();
    self.isolateScope = self.element.isolateScope();
  });

  it('edits the widget', function() {
    var self = this;
    self.isolateScope.edit();
    self.rootScope.$digest();
    expect(self.widgetEditor).to.have.been.calledWith(self.childScope.widget);
  });
```

Craziness, right? First we mock out the `widgetStorage` and `widgetEditor` again, then we proceed to create a `compile` function. This function will new up two scopes, a `parentScope` and a `childScope`, stub out a widget, and put it on the child scope. Then it goes on to do some complicated template and scope setup: first, compiling a parent element called `widget-organizer`, which gets the parent scope passed into it.  Once that's all set up, we add a `nested-widget` child element to it, pass it the child scope, and trigger the `$digest` loop.

Finally, we get to the magic: we call the `compile` function, then hook
into the compiled template's isolate scope (which is the `$dropdown`
scope), with `self.element.isolateScope()`. When we actually get to the
assertion at the end, we can hook into the isolate scope to call `edit`,
and finally check that the stubbed out `widgetEditor` was called with
the stubbed widget.

## Conclusion

Testing can get painful. I know that there were several times in our
project where the pain of figuring out what to do was so great that it
was easier to just move on, writing code and falling back to the "click test" to
make sure everything was working. Once you get out of that flow, though,
the feeling of uncertainty begins to grow and grow.

Once we took the time to figure out how to deal with these difficult
cases, it became a lot easier to know what to do when similar
complicated cases presented themselves. Armed with the patterns
described in this article, we were able to get back to a TDD workflow,
restore our confidence, and move forward with confidence.

I hope that the testing patterns we've looked at today prove useful in
your own development practice. AngularJS is still a young, growing framework.
What other patterns have you found to make it easier to test? Please
share them below!
