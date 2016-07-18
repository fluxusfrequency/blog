# Browser Geolocation 101

Introduction

## The HTML5 Geolocation API

### Usage

```
function geolocate() {
  if (window.navigator && window.navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(onGeolocateSuccess, onGeolocateError);
  }
}
```

```
function onGeolocateSuccess(coordinates) {
  const lat = position.coords.latitude;
  const long = position.coords.longitude;
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

### Gotchas

If a user declines geolocation on the first go-round, it's currently [not possible to ask them again](http://stackoverflow.com/questions/26765638/in-browser-geolocation-feature-how-can-i-prompt-the-user-to-reenable-after-they) because the HTML5 Permissions.request() method is not supported in any browser. Because of this, I consider it best practice to NOT prompt users to allow geolocation on page load. If you do, many users will say no and you will have a hard time getting them to turn it back on later.

If you're testing in Chrome, you can visit [chrome://settings/contentExceptions#location](chrome://settings/contentExceptions#location) to clear your selection. That way you can try the sad path if you want to.

## Integrating with Google Geocoder and Google Maps

API Key
Rate Limiting

Reverse Geocoding

