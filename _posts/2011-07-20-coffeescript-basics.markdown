--- 
layout: post
title: CoffeeScript Basics
wordpress_id: 394
wordpress_url: http://blog.elinkmedia.net.au/?p=394
date: 2011-07-20 14:35:35 +10:00
---
Rails 3.1 will support CoffeeScript out of the box. I gave the new boy in town a little test. I think it'll be a great tool for web developers writing javascript code. CoffeeScript is short and precise, generated JavaScript variables are properly scoped, class inheritance is made super easy, love the syntax sugar. Oh and yes, mixing in jQuery is easy too.

Here's a little play script I wrote to illustrate how a JavaScript class can be written in CoffeeScript

{% highlight javascript %}
class Person
  ### private var ###
  my_name = null

  ### constructor ###
  constructor: (name) -&gt;
    my_name = name

  ### public instance method ###
  speak: (saying)-&gt;
    alert "#{saying} #{fancy_name()}!"
 
  ### private instance method ###
  fancy_name = -&gt;
    if my_name == "Jim"
      "Jimbob"
    else if my_name == "Dan"
      "DanDaMan"
    else
      my_name

  ### class method ###
  @feature: -&gt;
    alert "1 Person has only 1 head"

Person.feature()
jim = new Person("Jim")
jim.speak("Hi")
{% endhighlight %}

The above CoffeeScript will be translated into the following JavaScript

{% highlight javascript %}
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
    return alert("" + saying + " " + (fancy_name()) + "!");
  };
  /* private instance method */
  fancy_name = function() {
    if (my_name === "Jim") {
      return "Jimbob";
    } else if (my_name === "Dan") {
      return "DanDaMan";
    } else {
      return my_name;
    }
  };
  /* class method */
  Person.feature = function() {
    return alert("1 Person has only 1 head");
  };
  return Person;
})();
Person.feature();
jim = new Person("Jim");
jim.speak("Hi");
{% endhighlight %}
