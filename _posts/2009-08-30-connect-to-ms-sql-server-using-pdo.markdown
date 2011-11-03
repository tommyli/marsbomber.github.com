--- 
layout: post
title: Connect to MS SQL Server using PDO
wordpress_id: 63
wordpress_url: http://blog.elinkmedia.net.au/?p=63
date: 2009-08-30 16:06:55 +10:00
---
I am not sure if having PHP talking to SQL Server is a common practice, but I have to do it recently with a new Zend Framework based project. Using the Zend Framework means I will not  use my good old pear MDB2 database abstraction mechanism to make my PHP application talk to SQL Server. PDO is used to handle this task. And of course, as soon as you move out of your comfort zone, you have problems.

My local server, hosted on XAMPP, cannot establish connection to my local 2005 SQL Server. After spending hours on this problem, I found it is caused by a DLL file shipped with the pre-compiled PHP was out dated. The updated ntwdblib.dll file can be downloaded from <a href="http://www.dlldll.com/ntwdblib.dll_download.html" target="_blank">http://www.dlldll.com/ntwdblib.dll_download.html</a> or <a href="http://www.userscape.com/ntwdblib.dll" target="_blank">http://www.userscape.com/ntwdblib.dll</a>

For a XAMPP installation, you will need to paste the DLL file in <em>"C:\xampp\php"</em> and <em>"C:\xampp\apache\bin"</em>. After this, everything should be good to go!
