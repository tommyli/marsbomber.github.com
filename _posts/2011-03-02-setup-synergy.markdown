--- 
layout: post
title: Setup Synergy
wordpress_id: 367
wordpress_url: http://blog.elinkmedia.net.au/?p=367
date: 2011-03-02 21:26:50 +11:00
---
I spent some time this morning setting up Synergy. Synergy allows me to use one set of mouse and keyboard to control multiple computers. The setup is is fairly straight forward. I just downloaded the latest stable release and followed the <a href="http://synergy2.sourceforge.net/running.html" target="_blank">instruction</a> from the Synergy site. My Synergy server is my MBP, and my Synergy client is an OS X 10.6 server. After the initial setup, my mouse is successfully shared. However my keyboard sharing is not working. A <a href="http://www.linkedin.com/in/tommyyli" target="_blank">friend</a> of mine suggested me to try an old release from the Synergy2 project, so I did. I downloaded Synergy2 1.3.1 from <a href="http://sourceforge.net/projects/synergy2/files/Binaries/" target="_blank">here</a>, repeat the same setup procedure. Both keyboard and mouse sharing are working nicely.

I then created a couple of automator applications, one on my MBP, the other on the OS X server. This allows me to start the Synergy server and client automatically on system startups.

On the Synergy server (my MBP). I created an automator application. Drag "Run Shell Script" into the workflow. The script I'm using is

{% highlight bash %}
#!/bin/sh
prog=(/PATH-TO-SYNERGY/synergys --no-daemon --config /PATH-TO-SYNERGY-CONF/synergy.conf)

### Stop any currently running Synergy client
killall ${prog[0]##*/}

### Start the new client
exec "${prog[@]}" -f > /tmp/synergy.log 2>&1 &
{% endhighlight %}

Save the automator application into /Applications. Add the new application to account login items from System Preferences.

Similarly, on the Synergy client (OS X Server) machine, repeat the above steps. Substitute the script content with

{% highlight bash %}
/PATH-TO-SYNERGY/synergyc -f SERVER-MACHINE-HOSTNAME > /tmp/synergy.log 2>&1 &
{% endhighlight %}

This is it. See if this helps you in any ways and let me know if the automation bit can be improved (I'm sure there are better ways to register startup processes on os x).

Reference
<ul>
	<li><a href="http://synergy2.sourceforge.net/running.html" target="_blank">http://synergy2.sourceforge.net/running.html</a></li>
	<li><a href="http://sourceforge.net/projects/synergy2/files/Binaries/" target="_blank">http://sourceforge.net/projects/synergy2/files/Binaries/</a></li>
</ul>
