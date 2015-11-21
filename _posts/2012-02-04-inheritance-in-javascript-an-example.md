---
layout: post
title: 'Inheritance in JavaScript: An example'
date: 2012-02-04 23:30:53.000000000 +01:00
type: post
published: true
status: publish
categories:
- JavaScript
tags: []
---
JavaScript is a prototypal language. It is object-oriented, but with a different style than Java or C++ (which are class-based, as opposed to object-based). Inheritance in prototypal languages isn't a relationship between classes, but between objects. The object which corresponds to the superclass acts as a prototype, which the other object clones.

Here's a short example of using inheritance to attain polymorphism in JavaScript.

~~~ java
// Ball will serve as our prototype
function Ball() {
  // Ball's prototype is Object, JavaScripts root prototype
  Ball.prototype = new Object();
  // Setting the constructor method of Ball to the Ball function
  Ball.prototype.constructor = Ball;
  // Adding another method to the prototype
  Ball.prototype.throw = function() {
  console.log("Throw not implemented.");
}
~~~

~~~
function TennisBall() {
  // TennisBall's prototype is Ball
  TennisBall.prototype = new Ball();
  TennisBall.prototype.constructor = TennisBall;
  TennisBall.prototype.throw = function() {
  console.log("Threw tennis ball 56 meters.");
}
~~~

~~~
function BowlingBall() {
  // BowlingBall's prototype is also Ball
  BowlingBall.prototype = new Ball();
  BowlingBall.prototype.constructor = BowlingBall;
  BowlingBall.prototype.throw = function() {
  console.log("Threw bowling ball 2 meters.");
}
~~~

~~~
// Get any kind of ball
function getRandomBall() {
  var randomNumber = Math.random(1);
  var ball;

  if (randomNumber < 0.5) {
    ball = new TennisBall();
  } else {
    ball = new BowlingBall();
  }
  return ball;
}
~~~

~~~
getRandomBall().throw();
~~~