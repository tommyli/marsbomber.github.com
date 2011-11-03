--- 
layout: post
title: Beautify Zend Framework project error pages
wordpress_id: 202
wordpress_url: http://blog.elinkmedia.net.au/?p=202
date: 2010-02-04 23:20:57 +11:00
---
Thanks to Udo Schochtert (http://www.psiop.ch/), I become aware of this really nice Agavi style error handler. I basically followed http://www.iezzi.ch/archives/397 to get most of it going. However there are 2 little glitches I want to point out.
<ol>
	<li>If you follow http://www.iezzi.ch/archives/397, most likely you'll forget, or not even aware of this (Thanks to Udo again, he pointed this out in the original blog's comments section).
In the application.ini file, we need to add
[php]resources.frontController.errorview = shiny[/php]</li>
	<li>Since I'm beautifying the ErrorController and its pages in my zf-with-doctrine project  (http://github.com/marsbomber/zf-with-doctrine). I ran into issues with displaying the ZFDebug menu on the error pages. What I had to do was simple. Go to the ErrorController.php, errorAction method, and add 2 lines to disable the ZFDebug plugin
[php]// Disable ZFDebug plugin
$frontController = Zend_Controller_Front::getInstance();
$frontController-&gt;unregisterPlugin('ZFDebug_Controller_Plugin_Debug');[/php]</li>
</ol>
If interested, you can check my zf-with-doctrine project out from github, http://github.com/marsbomber/zf-with-doctrine

Hope you like it and find it's useful.

Download
<ul>
	<li><a href="http://github.com/marsbomber/zf-with-doctrine" target="_blank">http://github.com/marsbomber/zf-with-doctrine</a></li>
</ul>
Reference
<ul>
	<li><a href="http://www.iezzi.ch/archives/397" target="_blank">http://www.iezzi.ch/archives/397</a></li>
</ul>
