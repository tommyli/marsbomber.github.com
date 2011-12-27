---
layout: post
title: Ruby basics - String
---
One night, I was studying some code written by [Aleksey](https://twitter.com/spyromus). I spotted his usage of Ruby string's "%" notation. It shows me how elegant Ruby can be, given knowing all those Ruby basics.

To confess, I learned (and still learning) Ruby from learning Rails. At this moment, I defintely think it's a bad idea! Basics should always be taught/learned before the magical frameworks. So, I'll start a series of posts to go through some of these basics.

## Ruby string % notation

**%Q** - Interpolated string. Character used after %Q will be used as the new string delimeter, hence it should be balanced. This makes normal string delimeters such as " and ' become auto-escaped. If the new delimeter also appears in the string, it only needs to be manually escaped if it's unbalanced.
{% highlight bash %}
%Q[Don't need to escape " and '. Need to escape \[ manually, but not [ and ]]
{% endhighlight %}

**%r** - Similar to %Q, but constructs a Regexp object. No need to escape those slashes!

**%W** - Feed it with a string of words separated by spaces, get back an array of words.

**%x** - Interpolated shell commands. Same as using backticks.