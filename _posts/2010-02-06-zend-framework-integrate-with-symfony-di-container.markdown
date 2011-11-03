--- 
layout: post
title: Zend Framework integrate with Symfony DI Container
wordpress_id: 219
wordpress_url: http://blog.elinkmedia.net.au/?p=219
date: 2010-02-06 22:57:25 +11:00
---
I want to integrate Symfony DI Container and Zend Framework together. It's been put on the back burner for a while. After trashed a box of tissue because of my runny nose today, I finally got something up. And let me share it here.

I'm not going to explain what dependency injection means, and how the Symfony DI Contain works here ... I'm actually not going to explain anything here today(I feel really crap at the moment) ... I'll find another time to get into the details.

For now, you can head to github, download the source code and have a play. Just make sure that the "/data/" directory is writable by your web server, hence it is used to store the generated DI service class file.

Download
<ul>
	<li><a href="http://github.com/marsbomber/zf-with-doctrine" target="_blank">http://github.com/marsbomber/zf-with-doctrine</a></li>
</ul>
Reference
<ul>
	<li><a href="http://blog.starreveld.com/2009/11/using-symfony-di-container-with.html" target="_blank">http://blog.starreveld.com/2009/11/using-symfony-di-container-with.html</a></li>
</ul>
