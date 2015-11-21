---
layout: post
title: Rethinking client libraries for Spotify's Web API
date: 2015-05-17 15:06:12.000000000 +02:00
type: post
published: true
status: publish
categories:
- API
- Spotify
tags: []
---
If there's any change that I'd like to make to the way we create clients for [Spotify's Web API](https://developer.spotify.com/web-api/ "Spotify's Web API"), it's that they should be generated from a single definition that's available in a version controlled repository.

When we built the Web API at Spotify, we also built a couple of clients. The most popular languages at the time were covered - Python, Javascript, and Java - but since we didn't have knowledge or time to cover all languages, we encouraged the community to let us know of any third-party clients that they've built. This resulted in not only additional languages such as Go, Ruby, .NET and PHP, but also pull requests to our own clients as well as other clients in languages already covered. We link out to them from the Developer Site's [Libraries page](https://developer.spotify.com/web-api/code-examples/ "https://developer.spotify.com/web-api/code-examples/").

Although this has worked out alright, I would still make changes if I had access to a time machine.

The issue we're seeing now is that a lot of clients, mainly those built by third parties, are not keeping up with newly released Web API features. The major reason for this is that they're more or less awkward to update, especially if you're not the client's main contributor.

**Generate the clients from a single point of truth.**

Some time after the Web API release, we also released the [Web API Console](https://developer.spotify.com/web-api/console/ "https://developer.spotify.com/web-api/console/"). It used RESTful API Modeling Language (RAML) to model Spotify's Web API, and used it to generate the entire console. This means that there's minimal work required to update the console when we've deployed new features for the Web API. Basically, any time the RAML specification would be updated, a job could run to generate the new API Console and deploy it to production. This automatic generation could and should be done with clients as well.

[![]({{ site.baseurl }}/assets/swagger.png)](https://swagger.io)

It should be mentioned that there are several popular API modeling languages except [RAML](http://raml.org/ "RAML"), such as [WADL](https://wadl.java.net/ "https://wadl.java.net/"), [API Blueprint](https://apiblueprint.org/ "https://apiblueprint.org/") and [Swagger](http://swagger.io/ "http://swagger.io/").

In a best case scenario, the Spotify's Web API's model would be hosted on Github and libraries would simply re-build and deploy whenever the model was updated.