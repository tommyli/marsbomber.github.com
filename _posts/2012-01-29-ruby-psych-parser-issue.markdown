---
layout: post
comments: true
title: Ruby (1.9.3) Psych YAML parser issue
---
Ruby 1.9.3 uses a more restrict YAML parser, Psych, instead of the old Syck parser. This caused me issues when using [Settingslogic](https://github.com/binarylogic/settingslogic). Even doing what suggested by Settingslogic to set the YAML parser back to Syck didn't help.

A simple test script
<script src="https://gist.github.com/1697539.js?file=yaml_parse.rb"></script>

Running it under Ruby 1.9.3 outputs this

{% highlight bash %}
Using: psych
{"defaults"=>{"cool"=>{"bang"=>"wow", "fruit"=>"apple"}}, "development"=>{"cool"=>{"fruit"=>"banana"}}}
Using: syck
{"defaults"=>{"cool"=>{"bang"=>"wow", "fruit"=>"apple"}}, "development"=>{"cool"=>{"fruit"=>"banana"}}}
{% endhighlight %}

You could see "development" lost its "bang" hash key.

There are ways to get around the issue by re-building Ruby with libyaml flag. I consider this as harmful than helpful. So I reverted my application.yml back to its very dumb form, no default options, no merging, just duplicate all settings for all environments. It's definitely stupid to do so, but for now, it's considered as a temporary workaround.
