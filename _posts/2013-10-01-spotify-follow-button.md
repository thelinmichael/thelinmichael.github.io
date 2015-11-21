---
layout: post
title: Spotify Follow Button
date: 2013-10-01 11:31:55.000000000 +02:00
type: post
published: true
status: publish
categories:
- Spotify
tags: []
---
Trying out [Spotify's new Follow Button](https://developer.spotify.com/technologies/widgets/spotify-follow-button/ "FollowButton") that's accessible through an iframe and not just in the Web Player and Desktop clients.

My contribution to this project has been to create browser tests that verify that the common use cases run successfully. They're written in both JavaScript using the [Mocha](http://visionmedia.github.io/mocha/ "Mocha") test framework and [WebDriverJs](https://code.google.com/p/selenium/wiki/WebDriverJs "WebDriverJs"), and Java using [TestNG](http://testng.org/doc/index.html "TestNG") and [WebDriver](https://code.google.com/p/selenium/wiki/GettingStarted "WebDriver").

<iframe style="border: none; overflow: hidden;" src="https://embed.spotify.com/follow/1/?uri=spotify:user:thelinmichael&amp;size=detail&amp;theme=light" height="56" width="300" frameborder="0" scrolling="no"></iframe>