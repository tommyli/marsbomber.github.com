---
layout: post
title: Netbeans debugger with Zend Framework
wordpress_id: 231
wordpress_url: http://blog.elinkmedia.net.au/?p=231
date: 2010-02-08 20:28:06 +11:00
---
Are you sick of doing var dumps on browser screens or error logs when you need to debug some errors? Have you seen the .Net guys step through their code line by line to figure out what went wrong? With PHP, you can do the same. Here's how I do it.

The IDE I use is Netbeans (6.8 on Mac). It's shipped with a built-in debugger, which works with Xdebug.

So ... we first need to make sure our PHP is running Xdebug instead of Zend. I have explained how I have Xdebug setup on my XAMPP stack on Mac from one of my previous posts, <a href="http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/" target="_blank">Setup phpUnderControl on Snow Leopard</a>. We can verify whether Xdebug is setup properly by loading up our phpinfo page. We should see something like this (notice the last line prints Xdebug)

<img class="aligncenter size-full wp-image-232" title="xdebug-php" src="/images/posts/xdebug-php.png" alt="" width="609" height="86" />Now, let's go to Netbeans. Netbeans out of the box listens on port 9000, which is the exact port number we set our Xdebug remote_port to in our previous step. This means if we are not running our application with mod_rewrite, we now should be able to set a breakpoint in our code, hit the debug icon, and have everything going.

But ... there's always a but. When we develop Zend Framework based applications, in fact any applications that utilising mod_rewrite. We will run into trouble. We'll find the debugger always hits the index controller, index action. To overcome this, we need to do the following.

Go to the Zend Framework application's project property in Netbeans. Go to the "Run Configuration" option. Click on Advanced. For "Debug URL", choose "Ask Every Time".

<img class="aligncenter size-full wp-image-238" title="zf-project-property" src="/images/posts/zf-project-property1.png" alt="" width="721" height="428" />

Now, if we want to debug the email action from the index controller, when we hit the debug icon in Netbeans, it'll pop up a dialog box asking us to type in the debug URL.

<img class="aligncenter size-full wp-image-235" title="set-debug-url" src="/images/posts/set-debug-url1.png" alt="" width="648" height="294" />

Once we Ok the dialog, Firefox will fire up (after you install the Netbeans debugger), and off you go!

The screenshots above are grabbed from my <a href="http://github.com/marsbomber/zf-with-doctrine" target="_blank">ZFWithDoctrine project</a> on github.

That's it, and hope it helps.
