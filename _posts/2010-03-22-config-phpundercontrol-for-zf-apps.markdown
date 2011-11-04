---
layout: post
title: Config phpUnderControl for ZF based applications
wordpress_id: 263
wordpress_url: http://blog.elinkmedia.net.au/?p=263
date: 2010-03-22 11:35:49 +11:00
---
I recently reinstalled my MacBook Pro, and it proves that my earlier post on <a href="http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/">how to setup phpUnderCotnrol on Snow Leopard</a> work well. I just followed the steps and phpUnderControl's up and running again.

Now, I want to go into the details a bit to show the configurations I have done to use phpUnderControl to do continuos integration for my Zend Framework based applications. The testing application I used here is the ZFDoctrine12 project I've created. You could pull down the source code of it from <a href="http://github.com/marsbomber/zf-with-doctrine/" target="_blank">my github project</a>.

First, I create the necessary folder structure for a new phpUnderControl project, as well as the ant build file.
{% highlight bash %}
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/source
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/api
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/coverage
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/logs
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/php-code-browser
sudo touch /opt/cruisecontrol/projects/zfdoctrine12/build.xml
{% endhighlight %}

Next, the ant build file content. You can see it's fairly minimal, but you should get the idea and know how to beef it up
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project name="zfdoctrine12" default="build" basedir=".">
  <target name="build" depends="php-codesniffer,phpunit"/>
  <target name="php-codesniffer">
    <exec executable="phpcs" dir="${basedir}/source" output="${basedir}/build/logs/checkstyle.xml" error="/var/tmp//checkstyle.error.log">
      <arg line="-n --report=checkstyle --standard=ZEND application"/>
    </exec>
  </target>
  <target name="phpunit">
    <exec executable="phpunit" dir="${basedir}/source/tests" failonerror="on">
      <arg line=" --log-junit ${basedir}/build/logs/phpunit.xml --coverage-clover ${basedir}/build/logs/phpunit.coverage.xml --coverage-html ${basedir}/build/coverage"/>
    </exec>
  </target>
</project>
{% endhighlight %}

Next, I want to clone the project from my local git repository into my phpUnderControl monitored project directory. To do this, we do
{% highlight bash %}
cd /opt/cruisecontrol/projects/zfdoctrine12/source/
sudo git clone ~/Sites/zfdoctrine12/ .
{% endhighlight %}

You can see that I'm only cloning the project from my local project repository, however this can be easily changed to utilise a remote git repository. e.g. repositories hosted on github.

I admit that after doing the git clone, I had to manually create and copy a couple of files. This was because of my .gitignore file from my local repository. So, go to the .gitignore file from your project repo, and do whatever changes that make sense to you.

Now, let's setup the phpUnderControl config.xml file, to make phpUnderControl become aware of our newly created project. Add the following XML snippet to your /opt/cruisecontrol/config.xml.
{% highlight xml %}
<project name="zfdoctrine12">
  <listeners>
    <currentbuildstatuslistener file="logs/${project.name}/status.txt"/>
  </listeners>
  <modificationset quietperiod="60">
    <git localWorkingCopy="projects/${project.name}/source/" />
  </modificationset>
  <bootstrappers>
    <gitbootstrapper localWorkingCopy="projects/${project.name}/source/" />
  </bootstrappers>
  <schedule interval="300">
    <ant anthome="apache-ant-1.7.0" buildfile="projects/${project.name}/build.xml"/>
  </schedule>
  <log dir="logs/${project.name}">
    <merge dir="projects/${project.name}/build/logs/"/>
  </log>
  <publishers>
    <artifactspublisher
      dir="projects/${project.name}/build/api"
      dest="artifacts/${project.name}"
      subdirectory="api"/>
    <artifactspublisher
      dir="projects/${project.name}/build/coverage"
      dest="artifacts/${project.name}"
      subdirectory="coverage"/>
    <execute
      command="phpcb
              --log projects/${project.name}/build/logs
              --source projects/${project.name}/source
              --output projects/${project.name}/build/php-code-browser"/>
    <artifactspublisher
      dir="projects/${project.name}/build/php-code-browser"
      dest="artifacts/${project.name}"
      subdirectory="php-code-browser"/>
    <execute command="/Applications/XAMPP/xamppfiles/bin/phpuc graph logs/${project.name} artifacts/${project.name}"/>
    <email
      buildresultsurl="http://localhost:8080/cruisecontrol/buildresults/zfdoctrine12"
      mailhost="smtp.gmail.com"
      mailport="465"
      usessl="true"
      username="USERNAME"
      password="PASSWORD"
      returnaddress="SENDER_EMAIL">
      <always address="RECIPIENT_EMAIL"/>
    </email>
  </publishers>
</project>
{% endhighlight %}

The above project config tells CruiseControl to look for VCS (git in this case) changes, if any changes are detected, a build will be triggered every 5 min. The result is then emailed out to a nominated email address.

This is it. It should give you a start point, and then it's up to you to tune it yourself. Hope it helps for people like myself, who is a beginner in Continuous Integration.

Reference
<ul>
	<li> <a href="http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/" target="_blank">http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/</a></li>
	<li><a href="http://cruisecontrol.sourceforge.net/main/configxml.html" target="_blank">http://cruisecontrol.sourceforge.net/main/configxml.html</a></li>
</ul>
