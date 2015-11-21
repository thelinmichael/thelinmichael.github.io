---
layout: post
title: API wrapper project for SMHI
date: 2014-04-11 23:05:55.000000000 +02:00
type: post
published: true
status: publish
categories:
- JavaScript
- Project
- Testing
tags: []
---
While preparing for Hack for Sweden mentioned in the previous post, I figured I'd try to write a wrapper for a [weather forecast API](http://www.smhi.se/klimatdata/Oppna-data/Meteorologiska-data/api-for-vaderprognosdata-1.34233 "http://www.smhi.se/klimatdata/Oppna-data/Meteorologiska-data/api-for-vaderprognosdata-1.34233") (Swedish) that we'd use so that it would take less time to utilise it. I hadn't dedicated myself to write a API wrapper previously, and figured I mention a couple of things that I found important and useful.

A wrapper isn't a lot of work, but figuring out beforehand how much you want to abstract away from the API is still a good thing to do to save time. I'm thinking of abstractions such as wrapping the response from the API with an object that has helper methods to extract information from it instead of being passed just the raw JSON.

I can imagine that building classes dedicated to caching and URL building would be very reusable between wrapper projects.

Although I find it very useful to have a test that runs against the API that you're wrapping, I'd avoid making it mandatory. For another wrapper project I've started since, I have a locally running web server responding with mocks and I find this is much more reliable to test against. You'll also avoid things like rate limiting the real API. However, an optional end-to-end-test against the API is a must to be certain that the wrapper still works.

[@npm](https://www.npmjs.org/package/smhi-node "https://www.npmjs.org/package/smhi-node"), [@github](https://github.com/thelinmichael/smhi-node "https://github.com/thelinmichael/smhi-node")