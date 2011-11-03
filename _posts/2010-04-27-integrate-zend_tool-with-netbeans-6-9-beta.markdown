--- 
layout: post
title: Integrate Zend_Tool with Netbeans 6.9 beta
wordpress_id: 269
wordpress_url: http://blog.elinkmedia.net.au/?p=269
date: 2010-04-27 14:27:50 +10:00
---
I've recently read a lot about Zend Framework (Zend_Tool) integration with Netbeans. Today I decided to download the Netbeans 6.9 beta and setup everything on my local development machine (OS X).

Here is how I did it on OS X with XAMPP.

First, I included Zend Framework library via a PEAR repository hosted on Google Code.

[code]

sudo pear channel-discover zend.googlecode.com/svn
sudo pear install zend/zend

[/code]

Completing the above will make Zend Framework available through PEAR, which should be in your PHP include_path.

Next, I downloaded and extracted the minimal Zend Framework package. Then I did

[code]

cd ~/Downloads/ZendFramework-1.10.3-minimal/bin
sudo cp zf.php /Applications/XAMPP/xamppfiles/bin/zf.php
sudo cp zf.sh /Applications/XAMPP/xamppfiles/bin/zf

[/code]

After this, I can run zf from command line without having any errors. So ... I did this

[code]

zf --setup storage-directory
zf --setup config-file

[/code]

I can now download the latest Netbeans 6.9 beta and do the normal install. After installation, start up Netbeans, go to "Netbeans" -&gt; "Preferences" from the menu bar. Navigate to the "PHP" -&gt; "Zend" section. Enter "/Applications/XAMPP/xamppfiles/bin/zf" into the "Zend script" input box. Then click on "Register Provider" button.

Now Netbeans should have updated the zf config file, which I created by running the "zf --setup config-file" command. And at this stage, I can create Zend Framework based projects and run "zf" commands to manipulate my projects within Netbeans.

However, if I jump back to the terminal shell, I would notice the good old "zf" command is broken and complaining it cannot find a file called "NetBeansCommandsProvider.php". Since I still want my terminal command line zf script working, I did this

[code]

sudo ln -s /Applications/NetBeans/NetBeans\ 6.9\ Beta.app/Contents/Resources/NetBeans/php/zend/NetBeansCommandsProvider.php /Applications/XAMPP/xamppfiles/lib/php/pear/NetBeansCommandsProvider.php

[/code]

Now I am happy. Hope someone find it's useful
