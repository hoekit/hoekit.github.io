---
layout: post
title: A simple method to make the JS client apps deployment environment-aware
---

When writing client apps, we usually need to connect to the data server.
However, this connection is typically different for production and test
environments. This post describes a simple way I've come up with to solve this
little problem. 

The client scripts are expecting to read in the environment somehow.
A JS file reads it in as some variable say:

    var env;

Okay, the client read in the configuration from a config file that we access
using jQuery like so:

    var env;
    $.getJSON( 'config.js', function( json ) {
           env = json;
    });

And in the configuration file, is a JSON structure for example:

    file: config.json
    {
        data_server : 192.168.1.31:4005
    }

However, that value is different for different deployment environments. So
let's say we save all the different configurations in another file e.g:

    file: config_all.json
    { 
        TEST : { data_server : 192.168.1.31:4005 },
        PROD : { data_server : 192.168.1.31:3005 }
    }

And we'll need a simple script that can generate the config.json from the
config_all.json file. Something like:

    $ configure_env PROD
    or
    $ configure_env TEST

From a project directory standpoint, the config.json file needs to be accessible
within the browser while the config_all.json file should be outside. So we end
up something like the following structure:

    project/tools/configure_env
    project/tools/config_all.json
    project/www/js/config.json

Here's a simple implementation of configure_env in perl:

    #!/usr/bin/env perl

    use strict;
    use warnings;
    use Path::Tiny qw/path/;
    use JSON;

    my $env = $ARGV[0] || 'TEST';   # Defaults to TEST environment
    my $source_file = 'tools/config_all.js';
    my $target_file = 'www/js/config.js';

    my $config_all = decode_json path($source_file)->slurp();
    path($target_file)->spew(encode_json $config_all->{$env});

Now, whenever we need to add an environment-specific value, we just open up
config_all.js and put in the value we need and on the client, it becomes
accessible.

