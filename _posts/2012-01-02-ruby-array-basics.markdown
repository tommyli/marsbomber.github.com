---
layout: post
title: Ruby basics - Array
---
Here are some basic Ruby array methods that I should learn to use.

**& and |** Array intersection and union
{% highlight ruby %}
a = [1, 2, 3]
b = [2, 3, 4]
a & b               #=> [2, 3]
a | b               #=> [1, 2, 3, 4]
{% endhighlight %}

**collect and select**
{% highlight ruby %}
a = [1, 2, 3, 4]
a.collect(&:odd?)   #=> [true, false, true, false]
a.select(&:odd?)    #=> [1, 3]
{% endhighlight %}

**delete_if and reject**
{% highlight ruby %}
a = [1, 2, 3, 4]
a.reject(&:odd?)    #=> [2, 4]
a                   #=> [1, 2, 3, 4]
a.delete_if(&:odd?) #=> [2, 4]
a                   #=> [2, 4]
{% endhighlight %}

**push, pop, shift and unshift**
{% highlight ruby %}
a = [1, 2, 3, 4]
a.push 5            #=> [1, 2, 3, 4, 5]
a                   #=> [1, 2, 3, 4, 5]
a.pop               #=> 5
a                   #=> [1, 2, 3, 4]
a.unshift 0         #=> [0, 1, 2, 3, 4]
a                   #=> [0, 1, 2, 3, 4]
a.shift             #=> 0
a                   #=> [1, 2, 3, 4]
{% endhighlight %}