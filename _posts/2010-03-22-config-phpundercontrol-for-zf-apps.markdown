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

[code]
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/source
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/api
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/coverage
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/logs
sudo mkdir /opt/cruisecontrol/projects/zfdoctrine12/build/php-code-browser
sudo touch /opt/cruisecontrol/projects/zfdoctrine12/build.xml
[/code]

Next, the ant build file content. You can see it's fairly minimal, but you should get the idea and know how to beef it up

[xml]
&lt;?xml version=&quot;1.0&quot; encoding=&quot;UTF-8&quot;?&gt;
&lt;project name=&quot;zfdoctrine12&quot; default=&quot;build&quot; basedir=&quot;.&quot;&gt;
  &lt;target name=&quot;build&quot; depends=&quot;php-codesniffer,phpunit&quot;/&gt;
  &lt;target name=&quot;php-codesniffer&quot;&gt;
    &lt;exec executable=&quot;phpcs&quot; dir=&quot;${basedir}/source&quot; output=&quot;${basedir}/build/logs/checkstyle.xml&quot; error=&quot;/var/tmp//checkstyle.error.log&quot;&gt;
      &lt;arg line=&quot;-n --report=checkstyle --standard=ZEND application&quot;/&gt;
    &lt;/exec&gt;
  &lt;/target&gt;
  &lt;target name=&quot;phpunit&quot;&gt;
    &lt;exec executable=&quot;phpunit&quot; dir=&quot;${basedir}/source/tests&quot; failonerror=&quot;on&quot;&gt;
      &lt;arg line=&quot; --log-junit ${basedir}/build/logs/phpunit.xml --coverage-clover ${basedir}/build/logs/phpunit.coverage.xml --coverage-html ${basedir}/build/coverage&quot;/&gt;
    &lt;/exec&gt;
  &lt;/target&gt;
&lt;/project&gt;
[/xml]

Next, I want to clone the project from my local git repository into my phpUnderControl monitored project directory. To do this, we do

[code]
cd /opt/cruisecontrol/projects/zfdoctrine12/source/
sudo git clone ~/Sites/zfdoctrine12/ .
[/code]

You can see that I'm only cloning the project from my local project repository, however this can be easily changed to utilise a remote git repository. e.g. repositories hosted on github.

I admit that after doing the git clone, I had to manually create and copy a couple of files. This was because of my .gitignore file from my local repository. So, go to the .gitignore file from your project repo, and do whatever changes that make sense to you.

Now, let's setup the phpUnderControl config.xml file, to make phpUnderControl become aware of our newly created project. Add the following XML snippet to your /opt/cruisecontrol/config.xml.

[xml]
  &lt;project name=&quot;zfdoctrine12&quot;&gt;
    &lt;listeners&gt;
      &lt;currentbuildstatuslistener file=&quot;logs/${project.name}/status.txt&quot;/&gt;
    &lt;/listeners&gt;
    &lt;modificationset quietperiod=&quot;60&quot;&gt;
      &lt;git localWorkingCopy=&quot;projects/${project.name}/source/&quot; /&gt;
    &lt;/modificationset&gt;
    &lt;bootstrappers&gt;
      &lt;gitbootstrapper localWorkingCopy=&quot;projects/${project.name}/source/&quot; /&gt;
    &lt;/bootstrappers&gt;
    &lt;schedule interval=&quot;300&quot;&gt;
      &lt;ant anthome=&quot;apache-ant-1.7.0&quot; buildfile=&quot;projects/${project.name}/build.xml&quot;/&gt;
    &lt;/schedule&gt;
    &lt;log dir=&quot;logs/${project.name}&quot;&gt;
      &lt;merge dir=&quot;projects/${project.name}/build/logs/&quot;/&gt;
    &lt;/log&gt;
    &lt;publishers&gt;
      &lt;artifactspublisher
        dir=&quot;projects/${project.name}/build/api&quot;
        dest=&quot;artifacts/${project.name}&quot;
        subdirectory=&quot;api&quot;/&gt;
      &lt;artifactspublisher
        dir=&quot;projects/${project.name}/build/coverage&quot;
        dest=&quot;artifacts/${project.name}&quot;
        subdirectory=&quot;coverage&quot;/&gt;
      &lt;execute
        command=&quot;phpcb
                --log projects/${project.name}/build/logs
                --source projects/${project.name}/source
                --output projects/${project.name}/build/php-code-browser&quot;/&gt;
      &lt;artifactspublisher
        dir=&quot;projects/${project.name}/build/php-code-browser&quot;
        dest=&quot;artifacts/${project.name}&quot;
        subdirectory=&quot;php-code-browser&quot;/&gt;
      &lt;execute command=&quot;/Applications/XAMPP/xamppfiles/bin/phpuc graph logs/${project.name} artifacts/${project.name}&quot;/&gt;
      &lt;email
        buildresultsurl=&quot;http://localhost:8080/cruisecontrol/buildresults/zfdoctrine12&quot;
        mailhost=&quot;smtp.gmail.com&quot;
        mailport=&quot;465&quot;
        usessl=&quot;true&quot;
        username=&quot;USERNAME&quot;
        password=&quot;PASSWORD&quot;
        returnaddress=&quot;SENDER_EMAIL&quot;&gt;
        &lt;always address=&quot;RECIPIENT_EMAIL&quot;/&gt;
      &lt;/email&gt;
    &lt;/publishers&gt;
  &lt;/project&gt;
[/xml]

The above project config tells CruiseControl to look for VCS (git in this case) changes, if any changes are detected, a build will be triggered every 5 min. The result is then emailed out to a nominated email address.

This is it. It should give you a start point, and then it's up to you to tune it yourself. Hope it helps for people like myself, who is a beginner in Continuous Integration.

Reference
<ul>
	<li> <a href="http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/" target="_blank">http://blog.elinkmedia.net.au/2010/02/04/setup-phpundercontrol-on-snow-leopard/</a></li>
	<li><a href="http://cruisecontrol.sourceforge.net/main/configxml.html" target="_blank">http://cruisecontrol.sourceforge.net/main/configxml.html</a></li>
</ul>
