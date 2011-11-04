--- 
layout: post
title: Ruby string interpolation
wordpress_id: 412
wordpress_url: http://blog.elinkmedia.net.au/?p=412
date: 2011-08-20 20:26:23 +10:00
---
We store configuration strings in YAML files. It's common that we need to substitute part of the string with runtime values. Hence we introduce some kind of placeholders in the config strings. Given an example that our application may need to hit an external URL to retrieve some remote resources at runtime. To do this, we could make a config string in our application's config YAML file like this

{% highlight ruby %}
remote_url_template: "http://remote.domain.com/give/me/RESOURCE1/and/RESOURCE2"
{% endhighlight %}

Then in our code, we substitute "RESOURCE1" and "RESOURCE2" with the real resource names we require. In my early days of programming in Ruby, I used some very ordinary string substitution method like this

{% highlight ruby %}
# remote_url_template is read from the config yaml
resource_one = "coke"
resource_two = "chips"
remote_url = remote_url_template.gsub("RESOURCE1", resource_one).gsub("RESOURCE2", resource_two)
{% endhighlight %}

It works, but I don't really like it... So I found something better, that is to use Ruby's string interpolation methods

{% highlight ruby %}
# in yaml file
remote_url_template: "http://remote.domain.com/give/me/%s/and/%s"

# in ruby code
# remote_url_template is read from the config yaml
resource_one = "coke"
resource_two = "chips"
remote_url = sprintf(remote_url_template, resource_one, resource_two)
{% endhighlight %}

A lot nicer, but still can be better. Thanks to a colleague of mine, <a href="http://blog.noizeramp.com/" target="_blank">Aleksey Gureiev</a>, another more elegant way can be used

{% highlight ruby %}
# in yaml file
remote_url_template: "http://remote.domain.com/give/me/%s/and/%s"

# in ruby code
# remote_url_template is read from the config yaml
resource_one = "coke"
resource_two = "chips"
remote_url = remote_url_template % [resource_one, resource_two]
{% endhighlight %}

That is satisfactory!
