---
layout: post
title: 'Webdriver: Reading the value of a Javascript variable (spoiler: weirdness)'
date: 2013-02-14 20:49:07.000000000 +01:00
type: post
published: true
status: publish
categories:
- JavaScript
- Testing
- Webdriver
tags: []
---
[Selenium Webdriver](http://docs.seleniumhq.org/projects/webdriver/ "http://docs.seleniumhq.org/projects/webdriver/") gives you the opportunity to execute Javascript through its [JavascriptExecutor](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/JavascriptExecutor.html "http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/JavascriptExecutor.html"). Webdriver's own functions are usually enough to interact with the browser though.

When using it today, a colleague and I stumbled upon something a bit strange that I can't explain (but would love to know the reason for). We basically wanted to take the value of a Javascript variable called _sessionId_ and make assertions on it.

Our first naïve attempt.

~~~ java
JavascriptExecutor js = (JavascriptExecutor) webdriver;

String sessionId = (String) js.executeScript("sessionId");

assertThat(sessionId, is(notNullValue());
~~~

No luck, the sessionId String object is null even though it's set.

Maybe we need to return the value through a function? Second attempt:

~~~ java
JavascriptExecutor js = (JavascriptExecutor) webdriver;

String sessionId = (String) js.executeScript("var getSessionId = function() { return sessionId }; getSessionId(); ");

assertThat(sessionId, is(notNullValue());
~~~

Still `null`. After searching a bit on the Interwebs, we found an [excellent post](http://stackoverflow.com/questions/13994393/reading-javascript-variables-using-selenium-webdriver "http://stackoverflow.com/questions/13994393/reading-javascript-variables-using-selenium-webdriver") on Stack Overflow on the same subject. We confirmed that in order to get the value of the variable, you'd need to

1.  create a Javascript function that returns the variable, and
2.  outputting it through and **alert**. (?!)

~~~ java
JavascriptExecutor js = (JavascriptExecutor) driver;

String sessionId = (String) js.executeScript("var getSessionId = function() { return sessionId }; alert(getSessionId());");

assertThat(sessionId, is(expectedSessionId));
~~~

What what?

This of course keeps an alert box open which needs to be closed by the test. Not too nice.

We ended up inserting the sessionId into an attribute on the body-tag.

~~~ java
String expectedSessionId = "612ba6cb-59aa1b6";

JavascriptExecutor js = (JavascriptExecutor) webdriver;

js.executeScript("document.body.setAttribute("\"sessionId\", sessionId)");

String sessionId = webdriver.findElement(By.tagName("body")).getAttribute("sessionId");

assertThat(sessionId, is(expectedSessionId));
~~~

_Side note:_ One could argue that we should've been using QUnit or a similar testing tool. The test's purpose was the verify that the sessionId was passed from a server-side controller class to the view, where it was inserted into a Javascript variable. Two tests, one for the controller (Selenium Webdriver, for example), and one for the Javascript (QUnit), could have been a cleaner solution.

**Edit:** Expanding on the elegant, and possibly fairly obvious, comment from Jose Sutilo. It's enough to simply use `executeScript("return <variablename>")` inside the JavascriptExecutor. As the [**official documentation**](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/JavascriptExecutor.html "http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/JavascriptExecutor.html") says,

> #### executeScript
>
> Executes JavaScript in the context of the currently selected frame or window. The script fragment provided will be executed as the body of an anonymous function.

This is why you couldn't just do `executeScript("sessionId")`, since it would mean that the anonymous function executed by Webdriver would look something like this: 

~~~
(function() { sessionId })(); // This returns nothing.
~~~

Treating the argument in executeScript as the body of an anonymous function on the other hand..

~~~
(function() { return sessionId; })(); // returns the value of sessionId.
~~~

**What still confuses me a bit**, is that the Javascript alert function successfully returns a value to the JavascriptExecutor, even though the anonymous function with an alert in it returns nothing.

~~~
(function() { alert(sessionId) })(); // returns nothing.
~~~

~~~
String sessionId = (String) js.executeScript("var getSessionId = function() { return sessionId }; alert(getSessionId());"); // WORKS!

assertThat(sessionId, is(expectedSessionId));
~~~