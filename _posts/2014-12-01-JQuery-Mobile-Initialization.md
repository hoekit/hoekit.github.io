---
layout: post
title: Proper Initialization of JQuery Mobile and Cordova
---

The Cordova library ships with a standard method for binding events to
deviceready. However, if JQuery Mobile is also used, it becomes tricky to bind
to the JQM Mobile ready "mobileinit" event.

Below is the template code that solves this problem.

### index.html

In index.html, add code to check whether "mobileinit" fired in the correct
sequence:

    <head>
        <title>Transfer</title>
        <meta charset="utf-8" />
        <meta name="format-detection" content="telephone=no" />    
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="stylesheet" href="css/jquery.mobile-1.4.5.min.css" />
        <script src="js/jquery-2.1.1.min.js"></script>
        <script>
            var jqmReadyDeferred = $.Deferred();
            $(document).on("mobileinit", function () {
                jqmReadyDeferred.resolve();
            });
        </script>
        <script src="js/jquery.mobile-1.4.5.min.js"></script>
    </head>
    <body>

        ...
        ...

    <script type="text/javascript" src="cordova.js"></script>
    <script type="text/javascript" src="js/index.js"></script>
    <script type="text/javascript">
        app.init();
    </script>

    </body>


Note that the sequence is: first load jquery, then the script to check
"mobileinit", and finally jquery mobile.

### index.js

And below is the template for index.js.

    var app = {

        init: function() {
            this.bindEvents();
        },

        bindEvents: function () {
            document.addEventListener('deviceready', this.onDeviceReady, false);
            this.bindJqmReady();
        },

        onDeviceReady: function() {
            // Device dependent stuff here e.g. push plugin, etc
        },

        // Runs or binds functions dependent on jQuery Mobile being ready
        bindJqmReady: function () {
             if (jqmReadyDeferred.state() === 'resolved') {
                app.onJqmReady();
            } else {
                $.when(jqmReadyDeferred).then(app.onJqmReady);
            }
        },

        // jqmReady Event Handler
        onJqmReady: function () {
            console.log('JQM is ready');
            // JQM dependent stuff here 
        },
    };


----
#### See Also:
1. http://api.jquerymobile.com/mobileinit/
2. http://stackoverflow.com/questions/10945643/correct-way-of-using-jquery-mobile-phonegap-together
