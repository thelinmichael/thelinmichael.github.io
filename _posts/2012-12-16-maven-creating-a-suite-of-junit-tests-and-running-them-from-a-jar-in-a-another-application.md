---
layout: post
title: 'Maven: Creating a suite of JUnit tests and running them from a jar in a another
  application'
date: 2012-12-16 21:40:15.000000000 +01:00
type: post
published: true
status: publish
categories:
- Java
- Maven
tags: []
---
I've spent a part of the day struggling with the what should be concidered best practice when wanting to run a Maven built suite of JUnit tests in another project.

Say that you want to test an application. You create a Maven project to be a master project, that uses other Maven built projects filled with tests as dependencies. How would you structure and compile the test projects, and how would you drag them into the master project?

I started out by creating my JUnit tests, placing them in the `/src/test/java` folder, and compiling them using the [maven-jar plugin](http://maven.apache.org/plugins/maven-jar-plugin/ "http://maven.apache.org/plugins/maven-jar-plugin/"). That would create a `<artifactId>-<version>-tests.jar` containing all the tests classes.  After setting the master project to use the test jar file as a dependency, I still found myself unable to run the tests as the test project's dependencies were missing. No transitive dependencies are downloaded by the master project. [Other people](http://jira.codehaus.org/browse/MNG-1378) seemed to have the same issue.

You could solve this by adding all the test project's dependencies in the master project, but that's not a good solution. Instead, as recommended by Apache [here](http://maven.apache.org/plugins/maven-jar-plugin/usage.html "http://maven.apache.org/plugins/maven-jar-plugin/usage.html") (scroll to "the preferred way" close to the bottom of the page):

1.  Create a new Maven project.
2.  Copy all tests and test resources to the /src/main/java/ and /src/main/resources/ folders.
3.  Copy the dependencies but remove the test scope.
4.  mvn install.

In the master project, you'd simply add the new project's artifactId and other information, but in **test scope**.

After doing this, you'll be able to to run the jar file as JUnit tests without any problems with transitive dependencies.