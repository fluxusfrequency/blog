# Caching Backbone Model Queries

## The Need Arose
I was recently working on a Backbone project with fellow Quick Left-er
[Bob Bonifield](https://twitter.com/bbonifield), when we came across
a problem. We were building an administration panel to be used
internally by our client, and there were a couple of views that needed
to display the same information about users in a slightly different way.
We decided to cache the result `Backbone.Model#fetch`. Here's how we did
it.

## Caching the Controller Call

A lot of the JavaScript developers in the office like to use our
colleague [Brent Ertz](https://twitter.com/brentertz)'s
[backbone-route-control](https://www.npmjs.org/package/backbone-route-control)
package. It makes it easy to separate concerns in the Backbone router
by splitting the methods associated with each route into controllers.

Bob and I were using backbone-route-control for this project. I'll show how we set up the UsersController to handle the first level of caching, though you could accomplish the same thing in a plain Backbone router as well.

First, we set up the app view and initialized a new Router on it,
passing in the controllers we wanted to use:

```
// Main

var UsersController = require('controllers/users');

var app = new AppView();

app.router = new Router({
  controllers: {
    users: new UsersController(app)
  }
});
```

Next, we defined the router and set it up to use the UsersController to
handle the appropriate routes.

```
// Router

var BackboneRouteControl = require('backbone-route-control')

var Router = BackboneRouteControl.extend({
  routes: {
    'users/:id': 'users#show',
    'users/:id/dashboard': 'users#dashboard'
  },

  initialize: function(options) {
    this.app = options.app;
  }
});

return Router;
```

## It's Magic

Finally, we defined the UsersController and created the appropriate
route methods. We opted to cache the ID of the last user that was
accessed by either the `show` or `dashboard` methods, so that we wouldn't
repeat the fetch call when we didn't need to. We set the result of the
call to `Backbone.Model#fetch` (a promise) to a variable called `userLoadedDeferred`, and passed it down the the views themselves.

```
// UsersController

var UsersController = function(app) {
  var lastUserID,
      userLoadedDeferred,
      user,
      lastUser;

  return {
    show: function(id) {
      this._checkLastUser();

      var usersView = new UserShowView({
        app: app,
        user: lastUser,
        userLoadedDeferred: userLoadedDeferred
      });

      app.render(usersView);
    },

    dashboard: function(id) {
      this._checkLastUser();

      var usersView = new UserDashboardView({
        app: app,
        user: lastUser,
        userLoadedDeferred: userLoadedDeferred
      });

      app.render(usersView);
    },

    _checkLastUser: function(id) {
      if (lastUserId != id) {
        lastUserId = id;
        lastUser = new User({ id: id });
        userLoadedDeferred = lastUser.fetch();
      }
    }
  }
});

```

## Model Level

Although our UsersController was now caching the result of a fetch for
a given user, we also needed to refetch the user to display her
information in a sidebar view as well. Since the UsersController
and the SidebarView were making two separate calls to the User model
`fetch` method, we decided to do some more caching there. We opted to
cache the results of the `fetch` call for 30 seconds, only making a new
server request if the time had expired. Here's what the code
looked like in the model:

```
// User Model

var Backbone = require('backbone');
// Set the timeout length at 30 seconds.
var FETCH_CACHE_TIMEOUT = 30000;

var User = Backbone.Model.extend({
  fetch: function() {

    // set a flag to bust the cache if we have ever set it
    // before, and it's been more than 30 seconds
    var bustCache = !(this.lastFetched && new Date() - this.lastFetched < FETCH_CACHE_TIMEOUT);

    // if we've never cached the call to `fetch`, or if we're busting
    // the cache, make a note of the current time, hit the server, and
    // set the cache to this.lastFetchDeferred.
    if (!this.lastFetchDeferred || bustCache) {
      this.lastFetched = new Date();
      this.lastFetchDeferred = Backbone.Model.prototype.fetch.apply(this, arguments);
    }

    // return the promise object in the cache
    return this.lastFetchDeferred;
  }
});

return User;

```

## Overriding It

Later on in our development, we came across a situation where we needed
to force a new fetch of User, right after updating some of her
attributes. Because we were caching the result for 30 seconds, the newly
updated attributes were not getting pulled from the server on our next
fetch call. To overcome this difficulty, we changed the `fetch` method
to take an option that allowed us to force a refetch.

```
// User Model

  ...

  fetch: function(options) {
    // check if we passed the forceRefetch flag in the options
    var forceRefetch = options && options.forceRefetch;

    ...

    // updated the check to if the flag was passed
    if (!this.lastFetchDeferred || bustCache || forceRefetch) {
    ...
```

## Conclusion

Caching our User model made our code a lot more efficient in this app.
Initially, we were making _two_ server calls per route, because we had
to fetch the user to display some things in both the main view and the
sidebar. After saving the result in the controller, we were now only
having to fetch the user data once per ID, until we tried to access
a user with a different ID. With the addition of model-level caching, we
were also able to remove the duplicated call between the main views and
the sidebar view, by saving the results of the `fetch` call for 30
seconds.

What are some of the tricks you use to make Backbone more efficient?
Please leave a comment or tweet at me @fluxusfrequency.

Code on!

