--- 
layout: post
title: ZF, Doctrine and Unit Tests
wordpress_id: 116
wordpress_url: http://blog.elinkmedia.net.au/?p=116
date: 2009-12-03 21:44:13 +11:00
---
Recently there are a lot of very interesting blog posts about using Zend Framework with the Doctrine ORM. I am really glad to see more and more people start talking about application design patterns, test driven development, etc. This trend means the PHP community is preparing to take PHP into the next level (enterprise? no?) . As a PHP developer, I see a lot of things to learn.

Back to the topic. I've been testing to use both Zend Framework and Doctrine for a while now. As a matter of fact, I never really have them hook up perfectly. The issue I faced was about the Doctrine command line tool. I just cannot manage to bootstrap the Doctrine command line script correctly. I can get Doctrine command line script to drop database, create database, generate models in PEAR style (which is used by the Zend Framework) from the yaml schema file. But I cannot get it to create tables, generate SQL, etc.

This did not seem to be a real issue until I started wondering about unit test my Doctrine model classes. Ideally, when I do my unit tests, I can re-create my testing database, reload my data fixture file. So ... I started digging.

Eric Leclerc made a really helpful <a href="http://www.danceric.net/2009/10/29/doctrine-1-2-is-zend-framework-friendly/" target="_blank">blog post</a> recently about setting up Zend Framework and Doctrine. Even better, Eric kindly created a <a href="http://github.com/danceric/zfdebugdoctrine" target="_blank">sample application</a> on github. I downloaded it, had a play, worked really well!

Next is unit testing. Lucky we still have Jon Lebensold, the creator of ZendCasts. Jon just made another great podcast on "<a href="http://www.zendcasts.com/writing-doctrine-unit-tests-with-zend_test/2009/12/" target="_blank">Writing Doctrine Unit Tests with Zend_Test</a>" yesterday. I downloaded the source code from Jon's episode. I started merging it with Eric's sample application.

After some tweaks and changes. I believe I finally have a working copy. That is Zend Framework + Doctrine + ZFDebug + Zend_Test. All in one project (no need to create models in a model generator project with Doctrine, copy them across to my ZF application).

I have pushed my sample application to github to share with others who are interested in using Doctrine with Zend Framework. You can find the application at <a href="http://github.com/marsbomber/zf-with-doctrine">http://github.com/marsbomber/zf-with-doctrine</a>.

Hope it helps. And also, my sincere thanks to Eric Leclerc and Jon Lebensold.

Reference
<ul>
	<li><a href="http://www.danceric.net/2009/10/29/doctrine-1-2-is-zend-framework-friendly/" target="_blank">http://www.danceric.net/2009/10/29/doctrine-1-2-is-zend-framework-friendly/</a></li>
	<li><a href="http://github.com/danceric/zfdebugdoctrine" target="_blank">http://github.com/danceric/zfdebugdoctrine</a></li>
	<li><a href="http://www.zendcasts.com/writing-doctrine-unit-tests-with-zend_test/2009/12/" target="_blank">http://www.zendcasts.com/writing-doctrine-unit-tests-with-zend_test/2009/12/</a></li>
	<li><a href="http://github.com/marsbomber/zf-with-doctrine" target="_blank">http://github.com/marsbomber/zf-with-doctrine</a></li>
</ul>
