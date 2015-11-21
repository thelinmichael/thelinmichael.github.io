---
layout: post
title: Trying out Sikuli - Setting up
date: 2012-12-07 20:57:26.000000000 +01:00
type: post
published: true
status: publish
categories:
- Sikuli
- Testing
tags: []
---
I'll be spending the weekend looking into [Sikuli](http://www.sikuli.org/ "http://www.sikuli.org/"), a test automation tool for GUIs.

Sikuli locates objects by comparing a reference image to objects on a specified region of the screen using [OpenCV](http://opencv.willowgarage.com/wiki/ "http://opencv.willowgarage.com/wiki/"). This is unlike tools like [Selenium](http://seleniumhq.org/ "http://seleniumhq.org/"), which finds objects by referring to attributes set on a target, such as the id of a web element in a website's DOM.

Sikuli has both its own IDE and a [Java API](http://code.google.com/p/sikuli-api/ "http://code.google.com/p/sikuli-api/") (1.0.2), and I'll be using the latter.

I'll be posting examples of the API as I go.

**Setting up the development environment  **

I'm running  Windows 7 (64 bit), [Eclipse Juno](http://www.eclipse.org/downloads/ "http://www.eclipse.org/downloads/"), [Maven 2.2.1](http://maven.apache.org/download.html "http://maven.apache.org/download.html"), [Java SE 6.37 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html "http://www.oracle.com/technetwork/java/javase/downloads/index.html"). Sikuli doesn't run on a 64-bit version of the JRE, so make sure your JRE is 32-bit. This means that Eclipse also needs to be 32-bit.

Output from command:

> java -version
java version "1.6.0_37"
Java(TM) SE Runtime Environment (build 1.6.0_37-b06)
Java Hotspot(TM) Client VM (build 20.12-b01, mixed mode, sharing)

> mvn -version
Apache Maven 2.2.1 (r801777; 2009-08-06 19:16:01 +0000)
Java version: 1.6.0_37
Java home: C:\Program Files (x86)\Java\jdk1.6.0_37\jre
Default locale: sv_SE, platform encoding: Cp1252
OS name: "windows 7" version "6.1" arch: "x86" Family: "windows"

If your Eclipse isn't already set up to use Maven, you'll need to install the m2eclipse plugin. Open Eclipse and go to _Help > Install New Software..._ and type _http://download.eclipse.org/technology/m2e/releases_ into the _Work with:_ input box, and follow the instructions.

Proceed by restarting Eclipse and creating a new Maven project.

Open the project's POM-file and add Sikuli as a dependency.

~~~
<pre><dependency>
        <groupId>org.sikuli</groupId>
        <artifactId>sikuli-api</artifactId>
        <version>1.0.2</version>
</dependency></pre>
~~~

Right-click on your project and do Maven > Update Snapshots.

All done!