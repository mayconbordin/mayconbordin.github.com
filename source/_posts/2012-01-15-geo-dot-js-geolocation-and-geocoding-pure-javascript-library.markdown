---
layout: post
title: "geo.js: Geolocation and Geocoding (pure) JavaScript library"
date: 2012-01-15 15:02
comments: true
categories: [JavaScript, Geolocation]
---

Some people may know the [geo-location-javascript](http://code.google.com/p/geo-location-javascript/), it supports, besides W3C Geolocation API, several other devices and platforms. I've used it before, but I always thought it could do better. I mean, it could be more flexible, support more alternatives, like GeoIP.

So I started to search for alternative libraries, until I found [locate_me](https://github.com/rmoriz/locate_me), this library doesn't support so many platforms as geo-location-javascript, but it has support for providers, in the way that I can write my own geolocation providers. But it has some drawbracks, like not following the W3C Geolocation interface and not offering the polling functions (watchPosition and clearWatch). It also relies on Prototype, although some people don't see this as a disadvantage.

As I could not find any other library that could fulfill my needs, I built my own. It has support for all the platforms supported by geo-location-javascript, plus geoip services, and it also support custom location providers. In addition, it support geocoding providers, so that when you receive the device's location, you can geocode it to obtain its address.

This library has the same interface that of W3C Geolocation API, including the polling functionality. The polling functions are provided by the native device's APIs, when enabled, and when the API has no support for it, the library uses his own implementation of watchPosition and clearWatch.

<!-- more -->

## Supported platforms

- W3C Geolocation API
- iOS
- Android
- BlackBerry OS
- Browsers with Google Gears support
- Nokia Web Run-Time
- webOS Application Platform
- Torch Mobile Iris Browser
- Mozilla Geode

**GeoIP Services**

- freegeoip.net
- geoip.pidgets.com
- geoplugin.com

**Reverse Geocoding Service**

- Google Geocoding API
- GeoNames
- Nominatim OpenStreetMap

## Getting Started

Include the gears_init.js, util.js and geo.js libraries in your html:

``` html
<script src="http://code.google.com/apis/gears/gears_init.js"></script>
<script src="js/util.js"></script>
<script src="js/geo.js"></script>
```

Now you can start the library:

``` javascript
Geo.init();
```

This is the default starter, the library will try to find the first available location provider to use. But you can give a provider to the initializer, as a string or object:

``` javascript
Geo.init('W3C');

//or

Geo.init(new Geo.LocationProvider.W3C());
```

To get the user location you just do this:

``` javascript
if (Geo.init()) {
    Geo.getCurrentPosition(function(p) {
        console.log(p);
    }, function(e) {
        console.log("Error " + e.code + ": " + e.message);
    });
}
```

## Methods

The Geo object will provide, besides the init(), the following methods:

- ```getCurrentPosition(successCallback, errorCallback, options)``` try to get the current position of the device, passing it to the successCallback. In case of error, errorCcallback is called.
- ```watchPosition(successCallback, errorCallback, options)``` the arguments work the same way as before. But this method will call the successCallback every time the device's position changes. It also returns an ID corresponding to the watcher. Some APIs have this method natively implemented, in these cases this library just redirects the call, but if the API has no support, the library uses an own implementation.
- ```clearWatch(watchId)``` stops the watcher created with the method above.

The options argument, is an object that can have one or more of those attributes:

- boolean enableHighAccuracy
- long timeout
- long maximumAge

Other methods:

- ```getIPAddress()``` returns the device's IP address.
- ```autoSetLocationProvider()``` automatically chooses the location provider.
- ```setLocationProvider(p)``` set the location provider to be used. Can be, as in the init() a string or object instance.

## Create your own Location Provider

This library has been built to enable the usage of custom location providers, the following example shows how to do that:

``` javascript
// This is just a dummy provider
Geo.LocationProvider.MyProvider = Geo.LocationProvider.Base.extend({
getCurrentPosition: function(successCallback, errorCallback, options) {
       successCallback(this.parseResult({
          latitude: -37,
          longitude: 11
       }));
    }
});
Geo.LocationProvider.MyProvider.available = function() {
    return true;
};

Geo.init('MyProvider');
```

Your location providers must be placed before the Geo.init() in order to be included in the provider auto selection. They also need to be created as an attribute of Geo.LocationProvider object to be registered.

## Default Providers

This is the list of the default providers:

- W3C
- Gears
- Bondi
- Mojo
- Nokia
- FreeGeoIp
- GeoIpPidgets
- GeoPlugin

## Reverse Geocoding

This library also support (reverse) geocoding providers. The Geo.Position object has a method for reversing geocoding his coordinates:

``` javascript
Geo.init();
Geo.getCurrentPosition(success_callback);

function success_callback(p) {
p.geocode(function(data) {
       console.log(p);
    });
}
```

When you call the geocode() method, the callback is optional, because the function will set the address retrieved by geocoding in the p object. Although there is a Geo.Address object, it is not used when doing geocoding because the Google Geocoding API can return several address components ([see](http://code.google.com/intl/en/apis/maps/documentation/geocoding/#Types)).

Currently there is three geocoding providers available ('Google', 'GeoNames', 'Nominatim'). When calling the geocode() method, you can also provide to the function the geocoding provider that you would like to use:

``` javascript
p.geocode(function(data) {
console.log(p);
}, 'Google');

// or

p.geocode(function(data) {
console.log(p);
}, new Geo.CodingProvider.GeoNames());
```

In order to use the Google Geocoding API you need to include the GMaps library in your html:

``` html
<script src="http://maps.google.com/maps/api/js?sensor=false"></script>
```

And if you're using the GeoNames service, you need to [create an account](http://www.geonames.org/login), login into your account and [enable free web services](http://www.geonames.org/manageaccount), and then inform the library what's your username:

``` javascript
if (Geo.init()) {
    // provide your username to use the geonames web service
    Geo.CodingProvider.GeoNames.username = 'myusername';
    Geo.getCurrentPosition(success_callback, error_callback);

    function success_callback(p) {
       p.geocode(function(data) {
          console.log(data);
       }, 'GeoNames');
    }

    function error_callback(p) {
       console.log(p);
    }
}
```

You can also write your own geocoder:

``` javascript
Geo.CodingProvider.MyGeocoder = Class.extend({
// this is the method called when geocode the Geo.Position object p
    geocode: function(p, callback) {
       // call the geocoding service using jsonp or through a proxy (ajax)
       // and then if there is a callback return the result
       if (callback) callback(results);

       // you need also to set the address in the Geo.Position object p
       p.address = results;
    }
});

// here you check if the service is available
Geo.CodingProvider.MyGeocoder.available = function() {
    return true;
};
```

Like the location providers, the geocoding providers must be placed before the Geo.init() call.

[Download](https://github.com/mayconbordin/geo.js/zipball/master) | [Github](https://github.com/mayconbordin/geo.js)
