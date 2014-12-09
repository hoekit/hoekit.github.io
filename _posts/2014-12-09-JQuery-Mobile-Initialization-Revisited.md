---
layout: post
title: JQuery Mobile and Cordova Initialization with Promises
---

An (earlier post)[1] described the proper initialization of JQuery Mobile and
Cordova using $.Deferred of JQuery.

This post describes the initialization with Promises using Forbes Lindesay's
(implementation)[2]. It turns out that using promises results in cleaner,
simpler code.

### File: index.html

In index.html, add code to check whether “mobileinit” fired in the correct
sequence:

    <!DOCTYPE html>
    <html>
    <head>
        <title>Transfer</title>
        <meta charset="utf-8" />
        <meta name="format-detection" content="telephone=no" />    
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="css/jquery.mobile-1.4.5.min.css" />
        <link rel="stylesheet" href="css/index.css">
        <script src="js/jquery-2.1.1.min.js"></script>
        <script src="js/promise-6.0.0.min.js"></script>
        <script>
            var jqmReady = new Promise(function (fulfill) {
                $(document).on("mobileinit", function () {
                    fulfill();
                });
            });
        </script>
        <script src="js/jquery.mobile-1.4.5.min.js"></script>
    </head>

Note that promise-6.0.0.min.js is loaded before the inline script that creates
the promise. 

### File: index.js

Below is the template for index.js:

    var app = {

        init: function() {
            this.bindEvents();
        },

        bindEvents: function () {
            document.addEventListener('deviceready', this.onDeviceReady, false);
            jqmReady.then(app.onJqmReady);
        },

        onDeviceReady: function() {
            // Device dependent stuff here e.g. push plugin, etc
        },

        onJqmReady: function () {
            console.log('JQM is ready');
            // JQM dependent stuff here 
        },
    };


### References

1. http://hoekit.github.io/JQuery-Mobile-Initialization/
2. https://www.promisejs.org/
3. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
