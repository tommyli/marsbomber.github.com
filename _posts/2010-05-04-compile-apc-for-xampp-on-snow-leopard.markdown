---
layout: post
title: Compile APC for XAMPP on Snow Leopard
wordpress_id: 275
wordpress_url: http://blog.elinkmedia.net.au/?p=275
date: 2010-05-04 15:34:14 +10:00
---
I'm using XAMPP as my local development environment on Snow Leopard. The XAMPP package does not include APC (Alternative PHP Cache). If we simply do a PECL install, you'll find that PHP complains about the APC architecture. This happens because the XAMPP for OS X is 32 bit, and Snow Leopard is 64 bit, hence PECL builds a 64 bit apc.so.

To overcome this issue, we need to manually compile APC from source and here is how I did it.

First, download APC source from <a href="http://pecl.php.net/package/APC" target="_blank">http://pecl.php.net/package/APC</a> and double click on the tar file within finder will unzip it.

Then
{% highlight bash %}
cd ~/Downloads/APC-3.1.3p1/APC-3.1.3p1/
phpize
MACOSX_DEPLOYMENT_TARGET=10.6 CFLAGS="-arch i386 -g -Os  -pipe -no-cpp-precomp" CCFLAGS="-arch i386 -g -Os  -pipe" CXXFLAGS="-arch i386 -g -Os  -pipe" LDFLAGS="-arch i386 -bind_at_load" ./configure --enable-apc --with-apxs --with-php-config=/Applications/XAMPP/xamppfiles/bin/php-config-5.3.1
make
sudo cp modules/apc.so /Applications/XAMPP/xamppfiles/lib/php/php-5.3.1/extensions/no-debug-non-zts-20090626/.
{% endhighlight %}

We should see no errors and we should have the new APC so file copied into our PHP extension directory.

Next, enable APC from php.ini by adding the following 2 lines

{% highlight apache %}
extension=apc.so
apc.shm_size = 32
{% endhighlight %}

Now if you do php -m from command line, you'll see APC listed as a loaded module.

Hope it helps and save you from pulling your hair out.

Reference
<ul>
	<li><a href="http://pecl.php.net/package/APC" target="_blank">http://pecl.php.net/package/APC</a></li>
	<li><a href="http://snipt.net/mik/compile-php-extension-on-mac-pro-64-bit-os-x-leopard/" target="_blank">http://snipt.net/mik/compile-php-extension-on-mac-pro-64-bit-os-x-leopard/</a></li>
</ul>
