--- 
layout: post
title: Ruby string interpolation
wordpress_id: 412
wordpress_url: http://blog.elinkmedia.net.au/?p=412
date: 2011-08-20 20:26:23 +10:00
---
We store configuration strings in YAML files. It's common that we need to substitute part of the string with runtime values. Hence we introduce some kind of placeholders in the config strings. Given an example that our application may need to hit an external URL to retrieve some remote resources at runtime. To do this, we could make a config string in our application's config YAML file like this

[code]
remote_url_template: &quot;http://remote.domain.com/give/me/RESOURCE1/and/RESOURCE2&quot;
[/code]

Then in our code, we substitute "RESOURCE1" and "RESOURCE2" with the real resource names we require. In my early days of programming in Ruby, I used some very ordinary string substitution method like this

[code]
# remote_url_template is read from the config yaml
resource_one = &quot;coke&quot;
resource_two = &quot;chips&quot;
remote_url = remote_url_template.gsub(&quot;RESOURCE1&quot;, resource_one).gsub(&quot;RESOURCE2&quot;, resource_two)
[/code]

It works, but I don't really like it... So I found something better, that is to use Ruby's string interpolation methods

[code]
# in yaml file
remote_url_template: &quot;http://remote.domain.com/give/me/%s/and/%s&quot;

# in ruby code
# remote_url_template is read from the config yaml
resource_one = &quot;coke&quot;
resource_two = &quot;chips&quot;
remote_url = sprintf(remote_url_template, resource_one, resource_two)
[/code]

A lot nicer, but still can be better. Thanks to a colleague of mine, <a href="http://blog.noizeramp.com/" target="_blank">Aleksey Gureiev</a>, another more elegant way can be used

[code]
# in yaml file
remote_url_template: &quot;http://remote.domain.com/give/me/%s/and/%s&quot;

# in ruby code
# remote_url_template is read from the config yaml
resource_one = &quot;coke&quot;
resource_two = &quot;chips&quot;
remote_url = remote_url_template % [resource_one, resource_two]
[/code]

That is satisfactory!
