---
layout: post
title: Small project in Play!
date: 2012-05-11 22:30:47.000000000 +02:00
type: post
published: true
status: publish
categories:
- Java
- Play!
- Project
tags: []
---
This is a screendump of a small project I did at work to get more comfortable with both [Play! Framework](http://www.playframework.org "http://www.playframework.org")  and our own products. One of our products is an email lookup, and this project is simply a front-end for it built in Play.

To use the product, the user simply uses the query parameter `emailAddress` to specify which email address to look up. For example, `http://www.some-domain.com/emailAddress=michael@yahoo.co.uk.` Using the form just hits the site again, and adds the form value to the query parameter on the end.

_Image removed_

Result page for `/emailAddress=michael@yahoo.co.uk`.

I did not take the screen dump at that time on purpose.. I really enjoy working in Play. It gives you a clear definition of your models, views and controllers, and is very easy to get up and running. It also comes with a great test functionality, and lots more. The Play website's tutorial is great if you want to take it for a spin.