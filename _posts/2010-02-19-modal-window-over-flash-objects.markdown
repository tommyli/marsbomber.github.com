---
layout: post
title: Modal window over flash objects
wordpress_id: 250
wordpress_url: http://blog.elinkmedia.net.au/?p=250
date: 2010-02-19 19:44:53 +11:00
---
I've been working on a WordPress site for a client. All pretty straight forward stuff. Only special requirement from the client is to have a categorised file repository as a WordPress page, which allows users to download files. One of the categories needs to be protected. So users click on a file download link and see a modal window with a form. Fill in the details, download the file.

The issue surfaced when we replaced the site banner with a flash (this is of course IE specific ...). The popup modal window slides behind the flash object. This turned out to be a common issue and the fix is simple.

{% highlight html %}
<object classid="clsid:D27CDB6E...." width="1151" height="209" ... >
    .... you other flash params stuff ...
    <param name="wmode" value="transparent">
</object>
{% endhighlight %}

The "wmode" transparent is the key!

I'm using SimpleModal, a jQuery plugin modal library.

Reference
<ul>
	<li><a href="http://www.ericmmartin.com/projects/simplemodal/" target="_blank">http://www.ericmmartin.com/projects/simplemodal/</a></li>
</ul>
