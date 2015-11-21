---
layout: post
title: Private methods - Test or not?
date: 2014-01-12 15:36:08.000000000 +01:00
type: post
published: true
status: publish
categories:
- JavaScript
- Testing
tags: []
---
While working on a JavaScript project that I've been putting a lot of time into recently, I've been using the bad practice of making methods not used outside of the class public so that I'll be able to test them. This has forced me to think about when to test private methods.

I've been battling with myself over if it's worth testing private methods at all, since they're very likely to change and should be considered implementation detail.

**The case against testing private methods**

The maintenance overhead of testing private methods is high since they're constantly up for refactoring.

Depending on what language and frameworks are available, the method may need to be accessible publicly in order to test them.

**The case for testing private methods**

Testing public methods is indirectly testing the private methods. A problem occurs when private methods are refactored out of the class and become public. If these untested methods are in turn used by other untested methods, the code suddenly contains two layers of untested method calls. A possible nightmare emerges. Having tested the private methods from the start would have avoided this.

Testing private methods makes it a lot easier to write the public facing methods since there are no unexpected errors since private methods work as intended.

**Conclusions**

If a public facing method takes a long time to write because of unexpected issues in private methods, the private methods obviously need testing. In order to solve this without breaking encapsulation, the private method is broken out of the class and turned public. Since it's public, it should be tested.

It can be difficult to know which methods that will become complex enough that they need testing. If a private function seems simple, it shouldn't be tested since it will not cause public facing functionality to break unexpectedly. If a private function is complex, or starts becoming complex, a test should be retrofitted for it.

Thus, don't test private methods. If necessary, break them out into new classes and test them there. Retrofit tests if it didn't have tests previously.