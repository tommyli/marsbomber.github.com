---
layout: post
title: RVM update
---
RVM is super easy to use, and it manages Ruby Gems well. Most of the Ruby/Rails projects I work on are using its Gemset feature.

I was running RVM version 1.6 and decided to perfom and upgrade.
{% highlight bash %}
rvm get latest
rvm upgrade 1.9.2-p180 1.9.2-p290
{% endhighlight %}

Second command from above is extremely useful. As per example above, it updates my local Ruby 1.9.2-p180 to 1.9.2-p290. All gemsets associated with the 1.9.2-p180 are also relinked to the new 1.9.2-p290. Handy!
