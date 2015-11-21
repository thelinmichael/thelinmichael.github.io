---
layout: post
title: Ways to resolve unstable Webdriver tests
date: 2012-11-13 14:20:21.000000000 +01:00
type: post
published: true
status: publish
categories:
- Testing
tags: []
---
We're constantly having issues with unstable [Webdriver](http://code.google.com/p/selenium/ "http://code.google.com/p/selenium/") tests for different reasons. Most times, it seems to come down to the way the test is written and not the way Webdriver works. Below, I'll list some of the things that I try to have in mind while writing a test. I'll begin with the stuff that are general test principles, and continue with what's Webdriver specific. I'll use Webdriver code to illustrate examples.

### 1\. General testing practices

#### 1.1\. Mock external services

When going through a test regression suit that took 30 minutes to run, a colleague and I found that every webpage was making a call to Google Analytics, causing the test suite to take much longer time than necessary due to network latency. (Another unwanted side effect was that the analytics got watered down by the artificial hits from the tests). Mocking the Google Analytics response shaved several minutes off of the time it took to run the regression suite. It also made the tests more stable. In case our own connection went down, or indeed if Google's did, the tests would take even longer to run in the best case, or in the worst case they would fail completely.

In essence, avoid this kind of flakiness by **never making calls to an external services unless you really have to**, like in an end-to-end-tests. In most cases you can **mock the response**, and it will make the test suite run faster as you don't have any network latency, and run more reliably since they don't depend on a network connection to the external service.

Using external services in a unit test is also an indication that you're testing more than the actual unit since you cannot really control the output from the external service. Avoid writing tests that are hybrids of unit tests and end-to-end tests.

Each web page contained an include of a JavaScript file that made a call to Google Analytics, so that we could track visitors around the site.

`
<script language="javascript" src="/javascript/googleAnalytics.js"></script>
`

So we just changed the path of this file to be configurable, and created an empty JavaScript file that can be used when running tests. [Play Framework 1.x](http://www.playframework.org/ "http://www.playframework.org/") uses Groovy as a template language, so the code would look like this:

`
<script language="javascript" src="${googleAnalyticsFilepath}"></script></pre>
`

In application.conf, Play Framework project's main configuration file, we'd set the path to the regular Google Analytics JavaScript file.

`googleAnalyticsFilepath=/javascript/googleAnalytics.js`

In test.conf, which is run after application.conf and overwrites any configuration that has been set earlier, we'd set the property to point at the empty test file.

`googleAnalyticsFilepath=/test/javascript/googleAnalytics.js`

The bad part is that you add to the amount of configuration needed to set up the project. But it's better than polluting your code with test code, like this.

~~~
#{if play.Play.runingInTestMode()}
   <script language="javascript" src="/javascript/googleAnalytics.js"></script>
#{/if}
#{else}
   <script language="javascript" src="/test/javascript/googleAnalytics.js"></script>
#{/else}</pre>
~~~

_By the way, that isn't a typo. runingInTestMode() is actually a function in Play Framework 1.x. _

#### **1.2\. Poll for asynchronous responses**

Most websites these days contain Ajax calls, and if you're making an end-to-end test in Webdriver, you are likely to have issues with it. The problem with asynchronus functions is that you never know how long it takes before the they're completed and change whatever it is you're testing.

Here's a few lines of code that checks for changes that should occur when a button is clicked.

~~~
driver.get("http://www.example.com/");

WebElement hasChanged = driver.findElement(By.id("somethingThatShouldChange"));
assertThat(hasChanged, is(false));

driver.findElement(By.id("buttonThatCallsAsynchronousFunction")).click(); // Ajax call that will change the DOM on successful return.

hasChanged = driver.findElement(By.id("somethingThatShouldChange"));
assertThat(hasChanged, is(true));
~~~

Woops. _hasChanged_ is modified when the Ajax function called by clicking _buttonThatCallsAsynchronousFunction_ returns. But there wasn't enough time between the click and the assertion.

**A very poor solution is to use** **sleep** to make sure that the asynchronous call has had enough time to return**.** The issue with sleep is that the sleep value needs to be as high as the highest value you can imagine the function could possibly need. If it's lower than this, the test might fail even if it was still working. If the asynchronous function finishes a lot quicker than in the worst case scenario, the test will just be hanging around wasting time.

Setting the sleep to 10 seconds to make sure _hasChanged_ has changed value. If the asynchronous function triggered by _buttonThatCallsAsynchronousFunction_ has finished before the sleep has, it just sits there waiting unnecessarily.

~~~
driver.findElement(By.id("buttonThatCallsAsynchronousFunction")).click();
Thread.sleep(10);
~~~

Instead, use **polling** to see if the hasChanged element has changed. Webdriver can wait for changes [explicitly or implicitly](http://seleniumhq.org/docs/04_webdriver_advanced.html "http://seleniumhq.org/docs/04_webdriver_advanced.html"). In explicit waits, shown below, Webdriver polls the expected condition every 0.5 seconds.

~~~
driver.findElement(By.id("buttonThatCallsAsynchronousFunction")).click();

hasChanged = (new WebDriverWait(driver, 10))
   .until(new ExpectedCondition<WebElement>() {
   @Override public WebElement apply(WebDriver d) {
     WebElement hasChangedPolled = d.findElement(By.id("somethingThatShouldChange");
         return (hasChangedPolled != null && hasChangedPolled.getText() != null && hasChangedPolled.getText().equals("true"));
}});
~~~

There's lots of convenience methods for in [ExpectedConditions](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/support/ui/ExpectedConditions.html "http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/support/ui/ExpectedConditions.html"), use them when possible to make your code more readable.The above use of WebdriverWait could've used this instead:

`hasChanged = wait.until(ExpectedConditions.textToBePresentInElement(By.id("somethingThatShouldChange"), "true"));`

#### 1.3\. Isolate tests

Basically, any test must be able to run several times in a row, and the whole suite must be able to run successfully regardless of order. Any changes made to anything that is shared must be changed back when the test has completed. Try using @BeforeClass, @Before, @After and @AfterClass for setup and tear downs. Things to think about during tear down are changes to the database, and static variables.

### 2\. Webdriver specific

#### 2.1\. Understanding Native events

A project my team was working on recently had several flaky Webdriver tests. 99% of the time, they ran perfectly on any local machine, but failed every other time when running on a specific Selenium node. It had been doing so for a while when we were running the tests locally, and we noticed by coincidence that the test **would only fail if we stole focus from the browser** running it. This is exactly what happened when we ran it on our Selenium nodes, which sometimes has multiple regression suits running at the same time.

There's two types of events in Webdriver - [native ones and synthetic ones](http://code.google.com/p/selenium/wiki/AdvancedUserInteractions#Introduction "http://code.google.com/p/selenium/wiki/AdvancedUserInteractions#Introduction"). **Native events should be used whenever possible**, as synthetic events are simulated Javascript actions as opposed to operating system events sent to the browser. Synthetic events may do unexpected things, such as being able to click on hidden objects. Some advanced Webdriver interactions require native events to be used.

[Native Events](http://code.google.com/p/selenium/wiki/NativeEventsOnLinux "http://code.google.com/p/selenium/wiki/TipsAndTricks") needs to be [turned on manually](http://code.google.com/p/selenium/wiki/TipsAndTricks "http://code.google.com/p/selenium/wiki/TipsAndTricks") in **Firefox for Linux** because of its reliability issues [relating to which window manager](http://code.google.com/p/selenium/wiki/NativeEventsOnLinux "http://code.google.com/p/selenium/wiki/NativeEventsOnLinux") is used. In essence, native events shouldn't need the browser to be focused to be able to run successfully. This is great since it enables us to run more than one Webdriver concurrently, as well as use other applications on the same machine while the test is running (if you're quick!). Some window managers still demand focus to allow for native events to work, and combined with issues of swapping focus between browsers, it renders it very difficult to run tests without turning to [synthetic events](http://my.safaribooksonline.com/book/programming/javascript/0596101996/events-and-event-handling/jscript5-chp-17-sect-7 "http://my.safaribooksonline.com/book/programming/javascript/0596101996/events-and-event-handling/jscript5-chp-17-sect-7") instead.

In essence, be aware when running Firefox on Linux.

#### 2.2\. Using JavaScriptExecutor when necessary

There's occasions when Webdriver doesn't interact with DOM elements as we're expecting. This might be because **we believe an event will be triggered**, for example the [onchange event when using sendKeys](http://code.google.com/p/selenium/wiki/FrequentlyAskedQuestions#Q:_The_%22onchange%22_event_doesn%27t_fire_after_a_call "http://code.google.com/p/selenium/wiki/FrequentlyAskedQuestions#Q:_The_%22onchange%22_event_doesn%27t_fire_after_a_call") on an inputbox. It may also be that we're using a **Javascript library** such as [Knockout](http://www.knockoutjs.com "http://www.knockoutjs.com") with its own set of triggers and handlers that doesn't mix with Webdriver very well. It may also be that the **browser version we're running isn't yet supported** by Webdriver, which happens a lot with Firefox. In this case, it can be necessary to run Javascript directly.

Changing the value of an input element with Javascript instead of using Webdriver's [sendKeys](http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebElement.html#sendKeys(java.lang.CharSequence...) "http://selenium.googlecode.com/svn/trunk/docs/api/java/org/openqa/selenium/WebElement.html#sendKeys(java.lang.CharSequence...)").

~~~
WebElement inputBox = driver.findElement(By.id("inputBox"));
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executorScript("jQuery('#' + inputBox.getAttribute('id')").val("changed value"));
~~~

#### 2.3\. Be aware of any environmental differences between test slaves

If you're running the regression test suite on several different machines (for example on a bunch of different [Selenium nodes](http://selenium-grid.seleniumhq.org/ "http://selenium-grid.seleniumhq.org/")), instability could be that the tests are running fine on all machines but one. On one occasion, we noticed that one of the test nodes constantly failed due to **an automatically updated version of** **Firefox which wasn't compatible** with Selenium.

As mentioned earlier, there's issues with using native events on Linux Firefox in combination with some window managers. This caused tests to fail on the Linux slave while running perfectly on a Windows slave. So the browser versions didn't even matter, they could've just as well been the same.

I think this is a decent beginning, but I'm sure there are more things that needs to be covered.