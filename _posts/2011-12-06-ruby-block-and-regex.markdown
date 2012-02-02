---
layout: post
comments: true
title: Ruby block and Regex
---
Open source is fantastic! I learnt something new from reading Rails source code today again.

Today's surprise was brough to me by the power of regular expression and Ruby blocks.

Rails 3 Net::HTTPHeader module has the following method
{% highlight ruby %}
def urlencode(str)
  str.dup.force_encoding('ASCII-8BIT').gsub(/[^a-zA-Z0-9_\.\-]/){'%%%02x' % $&.ord}
end
{% endhighlight %}

1st, I learnt the gsub method could take a block as its second argument.

2nd, inside the block, all regular expression back references could be used, e.g. $& for referencing all matching elements.