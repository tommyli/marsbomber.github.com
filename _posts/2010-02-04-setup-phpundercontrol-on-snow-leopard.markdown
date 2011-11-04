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
{% highlight bash %}
jimli@Jim-Lis-MacBook-Pro~: ll /usr/bin/pear
lrwxr-xr-x  1 root  wheel  39 29 Nov 08:45 /usr/bin/pear -> /Applications/XAMPP/xamppfiles/bin/pear
jimli@Jim-Lis-MacBook-Pro~: ll /usr/bin/php
lrwxr-xr-x  1 root  wheel  38 28 Nov 18:50 /usr/bin/php -> /Applications/XAMPP/xamppfiles/bin/php
{% endhighlight %}

First of all, let's install xdebug. Download the precompiled xdebug.so file from http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging

Open /Applications/XAMPP/xamppfiles/etc/php.ini in an editor, append the file with
{% highlight apache %}
[xdebug]
zend_extension=/Applications/XAMPP/xamppfiles/lib/php/php-5.3.0/extensions/no-debug-non-zts-20090626/xdebug.so
xdebug.remote_enable=on
xdebug.remote_handler=dbgp
xdebug.remote_host=localhost
xdebug.remote_port=9000
xdebug.file_link_format = "txmt://open?url=file://%f&line=%l"
{% endhighlight %}

Make sure the extension path is where you put the downloaded xdebug.so file to.

Now, let's install a few things
{% highlight bash %}
sudo pear channel-discover components.ez.no
sudo pear install -a ezc/Graph
{% endhighlight %}

Need to set preferred_state to alpha to get phpDocumentor for some reason.
{% highlight bash %}
sudo pear config-set preferred_state alpha
sudo pear channel-discover pear.phpunit.de
sudo pear install --alldeps phpunit/phpUnderControl
sudo pear config-set preferred_state stable
{% endhighlight %}

You can see, we did not install phpUnit, I believe phpUnit should be shipped with the latest XAMPP by default.
Now, Download cruisecontrol <a href="http://sourceforge.net/projects/cruisecontrol/files/CruiseControl/2.8.2/">here</a>. Locate your download from a shell and do
{% highlight bash %}
sudo unzip cruisecontrol-bin-2.8.2.zip -d /opt
sudo ln -s /opt/cruisecontrol-bin-2.8.2 /opt/cruisecontrol
sudo phpuc install /opt/cruisecontrol
sudo phpuc example /opt/cruisecontrol
{% endhighlight %}

Ok. I also assume you have subversion and Java installed.
Make sure you set the JAVA_HOME path in your environment. I did this by editing ~/.bashrc
{% highlight bash %}
JAVA_HOME="/System/Library/Frameworks/JavaVM.framework/Versions/1.6.0/Home";export JAVA_HOME
{% endhighlight %}

You may change the path accordingly base on your Java setup.

Now, edit /opt/cruisecontrol/config.xml, it should have something like
{% highlight xml %}
<?xml version="1.0"?>
<cruisecontrol>
  <project name="connectfour">
    <listeners>
      <currentbuildstatuslistener file="logs/${project.name}/status.txt"/>
    </listeners>
    <bootstrappers>
      <antbootstrapper anthome="apache-ant-1.7.0" buildfile="projects/${project.name}/build.xml" target="clean"/>
    </bootstrappers>
    <modificationset quietperiod="30">
      <!-- touch any file in connectfour project to trigger a build -->
      <filesystem folder="projects/${project.name}"/>
    </modificationset>
    <schedule interval="300">
      <ant anthome="apache-ant-1.7.0" buildfile="projects/${project.name}/build.xml"/>
    </schedule>
    <log>
      <merge dir="projects/${project.name}/target/test-results"/>
    </log>
    <publishers>
      <onsuccess>
        <artifactspublisher dest="artifacts/${project.name}" file="projects/${project.name}/target/${project.name}.jar"/>
      </onsuccess>
    </publishers>
  </project>
  <project name="php-under-control">
    <listeners>
      <currentbuildstatuslistener file="logs/${project.name}/status.txt"/>
    </listeners>
    <modificationset>
      <alwaysbuild/>
    </modificationset>
    <bootstrappers/>
    <schedule interval="300">
      <ant anthome="apache-ant-1.7.0" buildfile="projects/${project.name}/build.xml"/>
    </schedule>
    <log dir="logs/${project.name}">
      <merge dir="projects/${project.name}/build/logs/"/>
    </log>
    <publishers>
      <artifactspublisher dir="projects/${project.name}/build/api" dest="artifacts/${project.name}" subdirectory="api"/>
      <artifactspublisher dir="projects/${project.name}/build/coverage" dest="artifacts/${project.name}" subdirectory="coverage"/>      <execute command="phpcb --log projects/${project.name}/build/logs --source projects/${project.name}/source/src --output projects/${pro
ject.name}/build/php-code-browser"/>      <artifactspublisher dir="projects/${project.name}/build/php-code-browser" dest="artifacts/${project.name}" subdirectory="php-code-brow
ser"/>
      <execute command="/Applications/XAMPP/xamppfiles/bin/phpuc graph logs/${project.name} artifacts/${project.name}"/>
    </publishers>
  </project>
</cruisecontrol>
{% endhighlight %}

Now you can start and stop cruisecontrol by doing
{% highlight bash %}
cd /opt/cruisecontrol
sudo -E ./cruisecontrol.sh

kill `cat /opt/cruisecontrol/cc.pid`
{% endhighlight %}

After cruisecontrol is started, you can go to http://localhost:8080/cruisecontrol/ in your browser. Now everything should be good, except the metrics page. After some googling, I found this http://criticallog.thornet.net/?p=39
Basically you need to do
{% highlight bash %}
cd /Applications/XAMPP/xamppfiles/lib/php/pear/phpUnderControl/Graph/Input/
sudo mv ClassComplexityInput.php ClassComplexityInput.php.bak
{% endhighlight %}

Everything should now work!

Reference:
<ul>
	<li><a href="http://techportal.ibuildings.com/2009/03/03/getting-started-with-phpundercontrol/" target="_blank">http://techportal.ibuildings.com/2009/03/03/getting-started-with-phpundercontrol/</a></li>
	<li><a href="http://criticallog.thornet.net/?p=39" target="_blank">http://criticallog.thornet.net/?p=39</a></li>
	<li><a href="http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging" target="_blank">http://aspn.activestate.com/ASPN/Downloads/Komodo/RemoteDebugging</a></li>
</ul>
