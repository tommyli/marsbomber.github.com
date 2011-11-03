--- 
layout: post
title: Enable Memcache on MAMP
wordpress_id: 337
wordpress_url: http://blog.elinkmedia.net.au/?p=337
date: 2010-11-02 19:59:40 +11:00
---
I switched to MAMP from XAMPP for a while. A few days back I needed to make my localdev PHP script use Memcached servers. After some digging around, I found this nice post <a href="http://www.lullabot.com/articles/setup-memcached-mamp-sandbox-environment" target="_blank">http://www.lullabot.com/articles/setup-memcached-mamp-sandbox-environment</a>

I simply followed the steps to setup my local memcached service. However when I try to enable my local MAMP with the memcache module, I ran into some issues. Since I use PHP 5.3.2 on my localdev, I couldn't find a suitable memcache.so to plugin to my MAMP installation. So, I did some research, and compiled the it myself from source. Here's how I did it
<ol>
	<li>Download PHP 5.3.2 source</li>
	<li>Untar the source and place everything into /Applications/MAMP/bin/php5.3/include/php</li>
	<li>Open terminal and go to the /Applications/MAMP/bin/php5.3/include/php directory</li>
	<li>Run ./configure to generate all necessary header files, which will be used to compile memcache.so</li>
	<li>Download the stable version of memcache from PECL</li>
	<li>Unpack the source and go into that directory from terminal</li>
	<li>Run the following
[code]
jimli@Jim-Lis-MBP: phpize
jimli@Jim-Lis-MBP: CFLAGS='-O3 -fno-common -arch i386 -arch x86_64 -g -Os' LDFLAGS='-O3 -arch i386 -arch x86_64' CXXFLAGS='-O3 -fno-common -arch i386 -arch x86_64 -g -Os' ./configure --disable-dependancy-tracking --disable-shared -enable-static
jimli@Jim-Lis-MBP: make
jimli@Jim-Lis-MBP: sudo cp modules/memcache.so /Applications/MAMP/bin/php5.3/lib/php/extensions/no-debug-non-zts-20090626/.
[/code]</li>
        <li>Enable memcache from php.ini, restart MAMP</li>
</ol>

That's it. Hope you find it's useful.

Reference
<ul>
	<li><a href="http://www.lullabot.com/articles/setup-memcached-mamp-sandbox-environment" target="_blank">http://www.lullabot.com/articles/setup-memcached-mamp-sandbox-environment</a></li>
</ul>
