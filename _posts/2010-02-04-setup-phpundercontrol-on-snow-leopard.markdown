--- 
layout: post
title: Setup phpUnderControl on Snow Leopard
wordpress_id: 195
wordpress_url: http://blog.elinkmedia.net.au/?p=195
date: 2010-02-04 14:28:18 +11:00
---
Once in a while, people do pointless things. So ... here I am. I setup phpUnderControl on Snow Leopard last night ... that's right. I know you probably will not ever run your central continuous integration service on a Mac ... but you know ... just in case. Having said this, after I installed phpUnderControl on my "LOCAL" machine, I think it does give me a good view of what's happening with me project, which is quite cool.

First of all, I use XAMPP stack on OS X for my development. XAMPP provides us some important modules to complete the whole puzzle. Here's what I did. I'm not sure if they are all necessary, but at least I know it worked for me.

With XAMPP, there are 2 important bits, the PHP and PEAR executable. I have the following

[code]
jimli@Jim-Lis-MacBook-Pro~: ll /usr/bin/pear
lrwxr-xr-x  1 root  wheel  39 29 Nov 08:45 /usr/bin/pear -&gt; /Applications/XAMPP/xamppfiles/bin/pear
jimli@Jim-Lis-MacBook-Pro~: ll /usr/bin/php
lrwxr-xr-x  1 root  wheel  38 28 Nov 18:50 /usr/bin/php -&gt; /Applications/XAMPP/xamppfiles/bin/php
[/code]

First of all, let's install xdebug. Download the precompiled xdebug.so file from http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging

Open /Applications/XAMPP/xamppfiles/etc/php.ini in an editor, append the file with

[code]
[xdebug]
zend_extension=/Applications/XAMPP/xamppfiles/lib/php/php-5.3.0/extensions/no-debug-non-zts-20090626/xdebug.so
xdebug.remote_enable=on
xdebug.remote_handler=dbgp
xdebug.remote_host=localhost
xdebug.remote_port=9000
xdebug.file_link_format = &quot;txmt://open?url=file://%f&amp;line=%l&quot;
[/code]

Make sure the extension path is where you put the downloaded xdebug.so file to.

Now, let's install a few things

[code]
sudo pear channel-discover components.ez.no
sudo pear install -a ezc/Graph
[/code]

Need to set preferred_state to alpha to get phpDocumentor for some reason.

[code]
sudo pear config-set preferred_state alpha
sudo pear channel-discover pear.phpunit.de
sudo pear install --alldeps phpunit/phpUnderControl
sudo pear config-set preferred_state stable
[/code]

You can see, we did not install phpUnit, I believe phpUnit should be shipped with the latest XAMPP by default.
Now, Download cruisecontrol <a href="http://sourceforge.net/projects/cruisecontrol/files/CruiseControl/2.8.2/">here</a>. Locate your download from a shell and do

[code]
sudo unzip cruisecontrol-bin-2.8.2.zip -d /opt
sudo ln -s /opt/cruisecontrol-bin-2.8.2 /opt/cruisecontrol
sudo phpuc install /opt/cruisecontrol
sudo phpuc example /opt/cruisecontrol
[/code]

Ok. I also assume you have subversion and Java installed.
Make sure you set the JAVA_HOME path in your environment. I did this by editing ~/.bashrc

[code]
JAVA_HOME=&quot;/System/Library/Frameworks/JavaVM.framework/Versions/1.6.0/Home&quot;;export JAVA_HOME
[/code]

You may change the path accordingly base on your Java setup.

Now, edit /opt/cruisecontrol/config.xml, it should have something like

[xml]
&lt;?xml version=&quot;1.0&quot;?&gt;
&lt;cruisecontrol&gt;
  &lt;project name=&quot;connectfour&quot;&gt;
    &lt;listeners&gt;
      &lt;currentbuildstatuslistener file=&quot;logs/${project.name}/status.txt&quot;/&gt;
    &lt;/listeners&gt;
    &lt;bootstrappers&gt;
      &lt;antbootstrapper anthome=&quot;apache-ant-1.7.0&quot; buildfile=&quot;projects/${project.name}/build.xml&quot; target=&quot;clean&quot;/&gt;
    &lt;/bootstrappers&gt;
    &lt;modificationset quietperiod=&quot;30&quot;&gt;
      &lt;!-- touch any file in connectfour project to trigger a build --&gt;
      &lt;filesystem folder=&quot;projects/${project.name}&quot;/&gt;
    &lt;/modificationset&gt;
    &lt;schedule interval=&quot;300&quot;&gt;
      &lt;ant anthome=&quot;apache-ant-1.7.0&quot; buildfile=&quot;projects/${project.name}/build.xml&quot;/&gt;
    &lt;/schedule&gt;
    &lt;log&gt;
      &lt;merge dir=&quot;projects/${project.name}/target/test-results&quot;/&gt;
    &lt;/log&gt;
    &lt;publishers&gt;
      &lt;onsuccess&gt;
        &lt;artifactspublisher dest=&quot;artifacts/${project.name}&quot; file=&quot;projects/${project.name}/target/${project.name}.jar&quot;/&gt;
      &lt;/onsuccess&gt;
    &lt;/publishers&gt;
  &lt;/project&gt;
  &lt;project name=&quot;php-under-control&quot;&gt;
    &lt;listeners&gt;
      &lt;currentbuildstatuslistener file=&quot;logs/${project.name}/status.txt&quot;/&gt;
    &lt;/listeners&gt;
    &lt;modificationset&gt;
      &lt;alwaysbuild/&gt;
    &lt;/modificationset&gt;
    &lt;bootstrappers/&gt;
    &lt;schedule interval=&quot;300&quot;&gt;
      &lt;ant anthome=&quot;apache-ant-1.7.0&quot; buildfile=&quot;projects/${project.name}/build.xml&quot;/&gt;
    &lt;/schedule&gt;
    &lt;log dir=&quot;logs/${project.name}&quot;&gt;
      &lt;merge dir=&quot;projects/${project.name}/build/logs/&quot;/&gt;
    &lt;/log&gt;
    &lt;publishers&gt;
      &lt;artifactspublisher dir=&quot;projects/${project.name}/build/api&quot; dest=&quot;artifacts/${project.name}&quot; subdirectory=&quot;api&quot;/&gt;
      &lt;artifactspublisher dir=&quot;projects/${project.name}/build/coverage&quot; dest=&quot;artifacts/${project.name}&quot; subdirectory=&quot;coverage&quot;/&gt;      &lt;execute command=&quot;phpcb --log projects/${project.name}/build/logs --source projects/${project.name}/source/src --output projects/${pro
ject.name}/build/php-code-browser&quot;/&gt;      &lt;artifactspublisher dir=&quot;projects/${project.name}/build/php-code-browser&quot; dest=&quot;artifacts/${project.name}&quot; subdirectory=&quot;php-code-brow
ser&quot;/&gt;
      &lt;execute command=&quot;/Applications/XAMPP/xamppfiles/bin/phpuc graph logs/${project.name} artifacts/${project.name}&quot;/&gt;
    &lt;/publishers&gt;
  &lt;/project&gt;
&lt;/cruisecontrol&gt;
[/xml]

Now you can start and stop cruisecontrol by doing

[code]
cd /opt/cruisecontrol
sudo -E ./cruisecontrol.sh

kill `cat /opt/cruisecontrol/cc.pid`
[/code]

After cruisecontrol is started, you can go to http://localhost:8080/cruisecontrol/ in your browser. Now everything should be good, except the metrics page. After some googling, I found this http://criticallog.thornet.net/?p=39
Basically you need to do

[code]
cd /Applications/XAMPP/xamppfiles/lib/php/pear/phpUnderControl/Graph/Input/
sudo mv ClassComplexityInput.php ClassComplexityInput.php.bak
[/code]

Everything should now work!

Reference:
<ul>
	<li><a href="http://techportal.ibuildings.com/2009/03/03/getting-started-with-phpundercontrol/" target="_blank">http://techportal.ibuildings.com/2009/03/03/getting-started-with-phpundercontrol/</a></li>
	<li><a href="http://criticallog.thornet.net/?p=39" target="_blank">http://criticallog.thornet.net/?p=39</a></li>
	<li><a href="http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging" target="_blank">http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging</a></li>
</ul>
