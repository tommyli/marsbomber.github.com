--- 
layout: post
title: CoffeeScript Basics
wordpress_id: 394
wordpress_url: http://blog.elinkmedia.net.au/?p=394
date: 2011-07-20 14:35:35 +10:00
---
Rails 3.1 will support CoffeeScript out of the box. I gave the new boy in town a little test. I think it'll be a great tool for web developers writing javascript code. CoffeeScript is short and precise, generated JavaScript variables are properly scoped, class inheritance is made super easy, love the syntax sugar. Oh and yes, mixing in jQuery is easy too.

Here's a little play script I wrote to illustrate how a JavaScript class can be written in CoffeeScript

[javascript]
class Person
  ### private var ###
  my_name = null

  ### constructor ###
  constructor: (name) -&gt;
    my_name = name

  ### public instance method ###
  speak: (saying)-&gt;
    alert &quot;#{saying} #{fancy_name()}!&quot;
 
  ### private instance method ###
  fancy_name = -&gt;
    if my_name == &quot;Jim&quot;
      &quot;Jimbob&quot;
    else if my_name == &quot;Dan&quot;
      &quot;DanDaMan&quot;
    else
      my_name

  ### class method ###
  @feature: -&gt;
    alert &quot;1 Person has only 1 head&quot;

Person.feature()
jim = new Person(&quot;Jim&quot;)
jim.speak(&quot;Hi&quot;)
[/javascript]

The above CoffeeScript will be translated into the following JavaScript

[javascript]
var Person, jim;
Person = (function() {
  /* private var */  var fancy_name, my_name;
  my_name = null;
  /* constructor */
  function Person(name) {
    my_name = name;
  }
  /* public instance method */
  Person.prototype.speak = function(saying) {
    return alert(&quot;&quot; + saying + &quot; &quot; + (fancy_name()) + &quot;!&quot;);
  };
  /* private instance method */
  fancy_name = function() {
    if (my_name === &quot;Jim&quot;) {
      return &quot;Jimbob&quot;;
    } else if (my_name === &quot;Dan&quot;) {
      return &quot;DanDaMan&quot;;
    } else {
      return my_name;
    }
  };
  /* class method */
  Person.feature = function() {
    return alert(&quot;1 Person has only 1 head&quot;);
  };
  return Person;
})();
Person.feature();
jim = new Person(&quot;Jim&quot;);
jim.speak(&quot;Hi&quot;);
[/javascript]
