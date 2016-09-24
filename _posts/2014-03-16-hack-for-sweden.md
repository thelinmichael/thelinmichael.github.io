---
layout: post
title: Hack for Sweden
date: 2014-03-16 16:43:57.000000000 +01:00
type: post
published: true
status: publish
categories:
- Events
- Project
tags: [event]
---
[![Hack For Sweden](http://hackforsweden.se/wp-content/uploads/2015/12/hack_logo.png)](https://hackforsweden.se)

It was time for me and some colleagues to be hacking during an event instead of supporting.

Due to some [EU directives](http://ec.europa.eu/digital-agenda/en/pillar-i-digital-single-market/action-3-open-public-data-resources-re-use "http://ec.europa.eu/digital-agenda/en/pillar-i-digital-single-market/action-3-open-public-data-resources-re-use"), countries in the union are forced to open up public data so that it can be used for things like new innovative products. Type of data can differ widely, but some examples are weather, demographics and geological data. [Hack for Sweden](http://www.hackforsweden.se "http://www.hackforsweden.se") is a hackathon sponsored by a number of  large governmental institutes in Sweden who are currently working to opening up their data through web APIs. There were several award categories, including best visualisation, best app to use in a crisis, and best combination of governmental data.

We built a trip planning application that creates a itinerary for tourists that are in need of suggestions.

The backend is written in Python using Flask. Activities were fetched from [Foursquare](https://developer.foursquare.com/ "https://developer.foursquare.com/"), and scored against its popularity. Scores for outdoors activities are affected by weather data retrieved from the [Swedish Meteorological and Hydrological Institute's API](http://www.smhi.se/nyhetsarkiv/oppet-api-for-smhis-prognosdata-1.34326 "http://www.smhi.se/nyhetsarkiv/oppet-api-for-smhis-prognosdata-1.34326") so that no-one would be suggested to take a walk in the park in a -30 hailstorm. The frontend uses Google's [Maps API](https://developers.google.com/maps/ "https://developers.google.com/maps/") to display the coordinates where the activities take place.

Hack for Sweden felt like a really well-organised event. Several representatives of different functions at each sponsor were available during the whole hack, so there was never a problem getting help. Every team (about 25-30) was placed in well-ventilated rooms and food, snacks and drinks were served as often as anyone would need.

The event could've been better if talks, signs, and website were held, written and available in English, as several times had non-Swedish participants.

My personal learnings around the event was coding Python since I haven't touched it a lot. And to at least have spiked the project idea before the hackathon actually begins..