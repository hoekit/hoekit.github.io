---
layout: post
title: Storing and retrieving dates in SQLite
---

### Storing dates

In Javascript, the following returns the UTC in ISO format:

    x = new Date()
    x.toISOString()     // 2014-11-30T23:51:12.287Z
    x.toUTCString()     // Sun, 30 Nov 2014 23:51:12 GMT
    x.valueOf()         // 1417391472287

valueOf() is the number of milliseconds since midnight 01 January, 1970 UTC.
Which should we store? I personally prefer x.valueOf() because it's standard
and lots of functions exists to convert it to local time.

Store that value in an SQLite table like so:

    CREATE TABLE t ( d INTEGER );
    INSERT INTO t VALUES ( 1417391472287 );

### Retrieving and displaying dates

SQLite recognizes unix timestamps i.e. number of seconds (not miliseconds)
since midnight of 01 January, 1970 UTC. So to get date times, we divide our
value by 1000:

    SELECT date( d/1000,'unixepoch' );      -- 2014-11-30
    SELECT datetime( d/1000,'unixepoch' );  -- 2014-11-30 23:51:12

And to get GMT+7:00 time (because I'm in Thailand) we do:

    SELECT date( d/1000 + 7*3600, 'unixepoch' );      -- 2014-12-01
    SELECT datetime( d/1000 + 7*3600, 'unixepoch' );  -- 2014-12-01 06:51:12

SQLite provides something simpler and more localised, use the SQLite
'localtime' keyword:

    SELECT date( d/1000,'unixepoch','localtime' );      -- 2014-12-01
    SELECT datetime( d/1000,'unixepoch','localtime' );  -- 2014-12-01 06:51:12

### Perl handling of date time

Perl handles date time in seconds (not milliseconds). The localtime() function
returns an array in the local timezone. The corresponding gmtime() function
will return UTC time.

   my $dttm = 1417391472287;
   my @date = localtime($dttm/1000);

   my $YMD = sprintf("%04d-%02d-%02d", $date[5]+1900, $date[4]+1, $date[3]);
   my $hms = sprintf("%02d:%02d:%02d", $date[2], $date[1], $date[0]);

   print "$YMD $hms";       # 2014-12-01 06:51:12

----
See also: 

1. https://www.sqlite.org/lang_datefunc.html
2. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/valueOf
3. http://en.m.wikipedia.org/wiki/Unix_time


