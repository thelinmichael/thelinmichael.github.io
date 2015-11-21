---
layout: post
title: Quick dive into flow-based programming
date: 2014-01-19 22:13:42.000000000 +01:00
type: post
published: true
status: publish
categories:
- CoffeeScript
- JavaScript
tags: []
---
**Some initial research**

*   [Excellent introduction to Flow-based Programming](http://www.jpaulmorrison.com/fbp/index.shtml "http://www.jpaulmorrison.com/fbp/index.shtml") by its creator/inventor J. Paul Morrison.
*   [NoFlo](http://noflojs.org/documentation/ "http://noflojs.org/documentation/") (documentation page). Flow-based programming implementation running on Node.js, and written in CoffeeScript. A lot of documentation available, and a growing amount of libraries hosted on npm. [The NoFlo UI](http://noflojs.org/noflo-ui/ "http://noflojs.org/noflo-ui/") is a work in progress, and has been successfully [crowdfunded on Kickstarter](http://www.kickstarter.com/projects/noflo/noflo-development-environment "http://www.kickstarter.com/projects/noflo/noflo-development-environment").
*   [Blockie.io](http://blockie.io/ "http://blockie.io/"). Not yet released, currently crowdfunding on [indiegogo.com](http://www.indiegogo.com/projects/blockie-io-visual-flow-based-programming-for-back-end-development "http://www.indiegogo.com/projects/blockie-io-visual-flow-based-programming-for-back-end-development").

[caption id="attachment_675" align="aligncenter" width="774"][![Screen Shot 2014-01-20 at 00.00.22]({{ site.baseurl }}/assets/Screen-Shot-2014-01-20-at-00.00.22.png "NoFlo with its UI.")](http://www.michaelthelin.se/wp-content/uploads/2014/01/Screen-Shot-2014-01-20-at-00.00.22.png) NoFlo with its UI.[/caption]

So, I'm going to create a little module in NoFlo. A discussion about NoFlo on Reddit's JavaScript subreddit had [this rude little comment,](http://www.reddit.com/r/javascript/comments/1jkp81/kickstarter_noflo_development_environment/cbg1168 "http://www.reddit.com/r/javascript/comments/1jkp81/kickstarter_noflo_development_environment/cbg1168") from someone demanding that the NoFlo poster demoed some simple applications, such a Fibonacci and FizzBuzz. A Fibonacci application sounded easy enough, so I'm going to try and create that.

I can't seem to get the NoFlo UI going, so I'll write the graph in [NoFlo's Domain-Specific Language](http://noflojs.org/documentation/fbp/ "http://noflojs.org/documentation/fbp/"). This may be because the UI is still in development.

I created two node packages, one to hold the Fibonacci NoFlo component (noflo-fibonacci), and one to hold a graph (noflo-fibonacci-demo) that was using the component. The work to make the graph use the component was huge compared to the time it took to calculate the output, mainly because I was fiddling around with CoffeeScript.

<pre class="noescape prettyprint"># /noflo-fibonacci-demo/graphs/Fibonacci.fbp
# Get the value of the 6th number in the Fibonacci series
'6' -> IN Fibonacci(fibonacci/Fibonacci)

# Output the value
Fibonacci() OUT -> IN Display(core/Output)</pre>

The graph above uses two components; Fibonacci and Display. Fibonacci has an IN port, to which I always send '6'. Fibonacci's OUT port is connected to Displays only port, an IN port.

The component itself looks like this:

<pre class="noescape prettyprint"># Fibonacci component
# /noflo-fibonacci/components/Fibonacci.coffee
noflo = require "noflo"

class Fibonacci extends noflo.Component
  description: "This component receives data on a single input
  port and sends the same data out to the output port"

  constructor: ->
    # Register ports
    @inPorts =
      in: new noflo.Port()
    @outPorts =
      out: new noflo.Port()

    @inPorts.in.on "data", (data) =>
      fibonacciValue = @fibonacci(parseInt(data))
      @outPorts.out.send fibonacciValue

    @inPorts.in.on "disconnect", =>
      @outPorts.out.disconnect()

  fibonacci: (number) ->
    if (number <= 1)
      return number
    else
      return @fibonacci(number-1) + @fibonacci(number-2)

exports.getComponent = -> new Fibonacci()</pre>

Since there's barely any logic needed in the component, it mostly consists of boilerplate stuff. You can see that the component contains two ports though. When data arrives at the input port, it simply calculates the Fibonacci number and sends it through the output port.

Running it from terminal: _Image Removed_

I'd really like to see how it is to work with NoFlo in a larger project. Will the NoFlo UI increase readability, or make it worse?  It's difficult to tell.

I put the [code on GitHub](https://github.com/thelinmichael/noflo-fibonacci-demo "https://github.com/thelinmichael/noflo-fibonacci-demo") in case someone has difficulties getting a simple component up and running.
