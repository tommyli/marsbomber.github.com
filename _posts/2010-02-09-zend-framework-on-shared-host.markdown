--- 
layout: post
title: Zend Framework on shared host
wordpress_id: 242
wordpress_url: http://blog.elinkmedia.net.au/?p=242
date: 2010-02-09 22:09:14 +11:00
---
I came across <a href="http://akrabat.com/zend-framework/zend-framework-on-a-shared-host/" target="_blank">an article</a> made by Rob Allen (author of Zend Framework in Action) today. It gives us a very nice solution on how to host a Zend Framework project on a shared host.

The issue with running Zend Framework based application on a shared host is that sometimes you simply do not have the luxury to have "public" as your document root.  When I say "public", I refer to the web root created in Zend Framework (the directory that contains index.php). Sometimes, your FTP root is your document root. This means you are forced to dump all other Zend Framework folders inside the document root.

2 issues with that,
<ol>
	<li>Security, everything becomes exposed to the web.</li>
	<li>Ugly URL, you'll end up with something like http://mydomain.com/public/</li>
</ol>
Rob's post addressed this problem really well.

Reference
<ul>
	<li><a href="http://akrabat.com/zend-framework/zend-framework-on-a-shared-host/" target="_blank">http://akrabat.com/zend-framework/zend-framework-on-a-shared-host/</a></li>
</ul>
