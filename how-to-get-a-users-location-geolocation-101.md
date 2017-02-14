# How To Get A User's Location - Geolocation 101

## Introduction

If you work on a web application, you're in the business of attracting people to your site and providing an engaging experience that will keep them coming back. To make users feel like you care about them, a good approach is to customize the way your site behaves based on them. One way to get an easy win in catering to a user's specific needs is to ask for their location and use it to show them things that might interest them nearby - stores, for example.

Luckily, this is a pretty easy thing to achieve with HTML5 Geolocation. The Geolocation API is getting better all the time. For example, you can now use it to track a user's location as they move, which is great for mobile web applications.

In this post, we'll go over the basics of how to get a user's location, place a pin on a map, and show them coffee shops nearby using JavaScript. We'll also cover some of the gotchas that you'll need to work around when integrating geolocation into your app. If you want to follow along, you can check out my sample project [code](https://github.com/fluxusfrequency/geolocation).

## The HTML5 Geolocation API

To find out where a user is located, we'll need to ask them using their browser's implementation of the HTML Geolocation API. Providing your location is always optional, so we'll see a dialog box pop up in the browser with a message like: "Example website wants to: Know your location", and buttons to either block or accept this request.

Assuming the user clicks yes, the browser will somehow figure out the current location and provide it back to our JavaScript code. Under the hood, desktop Firefox and Chrome use local Wi-Fi network information and then send it to Google's Location Service database, while mobile browsers might use Wi-Fi or cellular triangulation or GPS [(source)](http://stackoverflow.com/questions/4213410/how-does-html5-geolocation-work).

Let's take a look at how to achieve this. Note: we'll be using ES6 throughout this tutorial.

### Usage

Imagine that we've built a page with a button on it, and we want to find a user's coordinates when they click it. How would we go about doing that?

First, we set up an event handler on the button that triggers a function called `geolocate`.

```js
const $geolocateButton = document.getElementById('geolocation-button');
$geolocateButton.addEventListener('click', geolocate);
```

Next, we'll define the actual `geolocate` function. It's important that we check that the browser actually supports geolocation before we move forward, so that we don't end up erroring out all over the place. Even though it's supported in all modern browsers except Opera Mini, it's still a good idea to check.

Our handler will call the `getCurrentPosition` method of the `window.navigator.geolocation` object. This method is the magic sauce that pops the dialog box prompting the user to block or accept our request. It takes two callbacks, for success and error outcomes.

```js
function geolocate() {
  if (window.navigator && window.navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(onGeolocateSuccess, onGeolocateError);
  }
}
```

Now that we've asked to get the user's location, what will we do with it? Also, what should we do if something goes wrong? Let's define our success and error callbacks. For now, we'll just log the coordinates to the console if we succeed. They'll be found in the `coords` property of the response object. If there's an error, we'll also log its code and message. I've left some comments showing what the error codes mean too, in case we want to handle these different cases later.

```js
function onGeolocateSuccess(coordinates) {
  const { latitude, longitude } = coordinates.coords;
  console.log('Found coordinates: ', latitude, longitude);
}

function onGeolocateError(error) {
  console.warn(error.code, error.message);

  if (error.code === 1) {
    // they said no
  } else if (error.code === 2) {
    // position unavailable
  } else if (error.code === 3) }
    // timeout
  }
}
```

Boom! Location found. That was easier than it looked!

### Gotchas

Before we go any futher, it's worth pointing out that you can only use these features over HTTPS [in Chrome](https://www.chromium.org/Home/chromium-security/prefer-secure-origins-for-powerful-new-features). Localhost is considered secure of HTTP, so you'll be able to play with these features in a local development environment, but if you're planning to use them on a live site, you'll need to set up HTTPS.

If you were thinking about asking the user for their location as soon as the page loads, there's something that you should note.

In the past, if a user declined geolocation on the first go-round, it was [not possible to ask them again](http://stackoverflow.com/questions/26765638/in-browser-geolocation-feature-how-can-i-prompt-the-user-to-reenable-after-they). The proper way to do this would be to use the HTML5 `Permissions.request()` method, but it's not supported in any browser but Firefox 47 and greater, and even here the user has to explicitly enable it.

Because of this problem, it's typically been best practice to NOT prompt users to allow geolocation on page load. Many users will typically click block without even reading when they see the unexpected dialog popup. Then they wouldn't have a future chance to accept future requests to geolocate. The only way around it was to show them a message asking them to go digging through their browser settings and re-enable geolocation for your site. For example, in Chrome, you can visit [chrome://settings/contentExceptions#location](chrome://settings/contentExceptions#location) to clear your selection.

This is no longer true in Chrome. Now, the user's geolocation decision is reset on page reload. There's also a little icon that appears in the address bar after a selection  has been made that allows you to clear your choice. Thus, you can now ask visitors to geolocate each time they load the page if you like. However, it still may not be a good idea if you expect them to be using browsers other than Chrome.

## Integrating with Google Geocoder and Google Maps

Now that we've looked at how to get a user's location, it would be nice to show it on a map. Luckily, this is pretty straightforward using any of the off-the-shelf map solutions available on the web today. For today's example, we'll use Google Maps.

### Showing the User's Location

Before you can load a Google Map on your site, you'll need to get an API key from them. You can do this on the [developer's console](console.developers.google.com). Create a new project and enable the JavaScript Maps API for it. Once you've done that, copy the API key. You'll need it to be able to load the Maps API script.

Also note that Google [rate limits](https://developers.google.com/maps/documentation/javascript/usage) requests to this API. For the free tier, you get 25,000 map loads per day.

Add the following line, substituting your API key where indicated.

```html
<script async defer src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&callback=enableButtons"></script>
```

I added it at the bottom of the `<body>` section of my page. Note the `callback=enableButtons` portion of that URL. You can enter whatever you want as the name of the callback. When it's loaded, the script will look for a function by that name on the window object and call it. For this example, we'll just use this callback to enable a button once Google maps has loaded.

```js

window.enableButtons = () => {
  const $geolocateButton = document.getElementById('geolocation-button');
  $geolocateButton.disabled = false;

  console.log('Google Maps API loaded');
}

```

Now that we've loaded the Google Maps library, we can use it to display a map with the user's location once the button is clicked. First, we'll add an event handler to trigger when the button is clicked.

```js
document.addEventListener('DOMContentLoaded', () => {
  const $geolocateButton = document.getElementById('geolocation-button');
  $geolocateButton.addEventListener('click', geolocate);
});

```

Then, we'll set up the geolocate handler to create a map, and show it in a `div` with an id of "map".

```js
function geolocate() {
  navigator.geolocation.getCurrentPosition(onGeolocateSuccess, onGeolocateError);
}

function onGeolocateSuccess(coordinates) {
  const { latitude, longitude } = coordinates.coords;
  showMap(latitude, longitude);
}

function showMap(lat, lng) {
  const $map = document.getElementById('map');
  const position = { lat, lng };
  const map = new google.maps.Map($map, {
    center: position,
    zoom: 6
  });
});
```

If you were to run this code now, you would see a map appear centered on the location found when the user agreed to geolocation. We're only one step away from our goal of showing the user's location. All that's left is to create a marker and add it to the map at those coordinates. We'll add that code to the `showMap` function as well:

```js

function showMap(lat, lng) {
  const $map = document.getElementById('map');
  const position = { lat, lng };
  const map = new google.maps.Map($map, {
    center: position,
    zoom: 6
    });
  const marker = new google.maps.Marker({ map, position });
}

```

Voila! Now, when the user clicks on the `#geolocate` button, we'll ask them to geolocate, then show a map centered on their location! You could instead show the map as soon as the Google Maps script is loaded in the initial callback (ours was called enableButtons, but you could just as easily make it createMap). Once the script is loaded, you have a lot of flexibility.

To see it working, [download the source](https://github.com/fluxusfrequency/geolocation), add an API key to the script in `index.html` and try it out!

You can take a look at [Google's documentation]( https://developers.google.com/maps/documentation/javascript/examples/map-geolocation) to get a sense of how to interface with their API.

### Tracking A User's Location

If you're working on a site that you expect your users to visit on their phone, you can also track their location.

To do so, you pretty much follow the same steps as you would to request the user's location once, only using the `geolocation.watchPosition` function instead. There's also a `geolocation.clearWatch` function  that will remove the position watcher. Let's look at how to work with that, too.

First, add an event listener on a button to turn on geolocation tracking, and another on a button we'll use to clear the position watcher.

```js
document.addEventListener('DOMContentLoaded', () => {
  const $geolocateButton = document.getElementById('geolocation-button');
  const $watchButton =  document.getElementById('watch-button');
  const $clearWatchButton = document.getElementById('clear-watch-button');

  $geolocateButton.addEventListener('click', geolocate);
  $watchButton.addEventListener('click', watchLocation);
  $clearWatchButton.addEventListener('click', clearWatch);
});
```

Next, write the handler function for the watch button. It takes a callback to be fired whenever the device's position changes, and an error handler. We'll write an `onLocationChange` handler for the first of these, and reuse the `onGeolocateError` handler from above for the other case.

When you call `watchPosition`, you get back an id for the watch. We'll store it in localStorage for simplicity's sake.

```js
function watchLocation() {
  const watchId = navigator.geolocation.watchPosition(onLocationChange, onGeolocateError);
  window.localStorage.setItem('lastWatch', watchId);
  console.log('Set watchId', watchId);
}

function onLocationChange(coordinates) {
  const { latitude, longitude } = coordinates.coords;
  console.log('Changed coordinates: ', latitude, longitude);
}
```

Now we'll set up the function to clear the watch and remove its id from localStorage.

```js
function clearWatch() {
  const watchId = window.localStorage.getItem('lastWatch');
  navigator.geolocation.clearWatch(watchId);
  console.log('Cleared watchId: ', watchId);
}
```

That's pretty much all there is to it. In a real application, you would use the `onLocationChange` callback as an entry point to executing the business logic of your app. Just don't forget to clean up your watches when you're done!

### Showing things nearby

Now that we've shown our user's location, let's show them things nearby that they may be interested in. Imagine that you're building a site for finding coffee shops. You have a database full of coffee shop addresses, and you want to show the ones that are close by in the map.

To find out where the shops are by latitude and longitude, you will need to geocode them. It's really better to do that server-side, so we'll leave the details out of this post, but here are [the docs](https://developers.google.com/maps/documentation/javascript/geocoding) for how you could do it on the front-end if you're interested in playing around with it.

Suffice to say, if you wanted to fetch nearby locations given a user's latitude and longitude, you'd pass them to your API server in some kind of POST request containing those coordinates. The server would then do some kind of query to find locations that match in the database. For example, with [Ruby Geocoder](http://www.rubygeocoder.com/) you would call something like `Shop.near(params[:latitude], params[:longitude])`.

Let's assume that you've already geocoded your coffee shops, and you made a request to your server, which gave back a JSON object with an array of nearby shops that looks like this when it's parsed:

```js
{
  shops: [
    {
      id: 1,
      name: "The Laughing Goat",
      lat: 40.0195,
      lng: -105.2731
    },
    {
      id: 2,
      name: "Tee and Cakes",
      lat: 40.0181,
      lng: -105.2771
    },
    {
      id: 3,
      name: "The Cup",
      lat: 40.0190,
      lng: -105.2753
    },
    {
      id: 4,
      name: "Boxcar",
      lat: 40.0198,
      lng: -105.2714
    }
  ]
}
```

Since we've got a collection of latitudes and longitudes here, all we have to do is loop through them and add markers with their location to the map. But first, we'll make a couple of small changes to our `showMap` function from above, making the Google maps `map` instance into a global so we can reuse it. We'll also set a global `window.markers` array in which we can store our markers (or remove them later).

```js
function showMap(latitude, longitude) {
  const $map = document.getElementById('map');
  const position = { lat, lng };
  const marker;

  window.map = new google.maps.Map($map, {
    center: position,
    zoom: 6
  });
  window.markers = window.markers || [];
  marker = new google.maps.Marker({ map, position });
  window.markers.push(marker);
}
```

Now we simply loop through the shops and create a marker on the map for each one.

```js
function showNearbyShops() {
  if (!window.map || !window.markers) { return; }

  mockShopResponse.shops.forEach((shop) => {
    const { lat, lng, name } = shop;
    const position = { lat, lng };
    const title = name;
    const marker = new google.maps.Marker({ map, position, title });
    window.markers.push(marker);
  });
}
```

We'll create another button to show the shops, and set up a click handler on it to call `showNearbyShops`.

```js
document.addEventListener('DOMContentLoaded', () => {
  ...
  const $showNearbyButton = document.getElementById('show-nearby-button');

  ...
  $showNearbyButton.addEventListener('click', showNearbyShops);
});
```

Then reload, click the button, and there they are!

## Conclusion

Although you might suspect it would be difficult to get a user's location from the browser, it's actually a pretty straightforward process. Although this feature is still evolving as browsers provide more aspects of geolcation, it's becoming more and more important (ever notice how often Google asks for your location?).

In this post, we looked at how to ask a user for their location, use it to put a pin on a Google map, how to track a user's location as they move, and how to show nearby locations as well. If you're interested in learning more about geolocation, there are some good resources [here]( https://developers.google.com/web/fundamentals/native-hardware/user-location/user-consent?hl=en), [here](http://diveinto.html5doctor.com/geolocation.html), and [here](https://www.sitepoint.com/geolocation-in-the-browser/).

I hope you enjoyed this post. If you decide to give it a try, tweet me a link and show me what you've made! Until next time, happy hacking.
