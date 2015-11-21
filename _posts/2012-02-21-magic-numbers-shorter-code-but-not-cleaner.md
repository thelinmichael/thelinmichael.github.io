---
layout: post
title: 'Magic numbers: Shorter code, but not cleaner'
date: 2012-02-21 14:20:02.000000000 +01:00
type: post
published: true
status: publish
categories:
- Clean code
- Java
- JavaScript
tags: []
---
Magic numbers can mean several things, but the one I'm going to address is the sort of number which doesn't give the reader any idea of why it has a certain value.

This piece of Java code gives an example of a magic numbers:

~~~
World world = new World();
world.addCountry(46);
~~~

Without looking further into the code, it would be very difficult to tell why the parameter sent into the `addCountry`-method is `46`.

~~~
final int COUNTRYCODE_SWEDEN = 46;
World world = new World();
world.addCountry(COUNTRYCODE_SWEDEN);
~~~

The code takes up more lines, but becomes both easier to read and more flexible. Sweden's country code might be needed again, and by making it a variable it only needs changing at a single place.