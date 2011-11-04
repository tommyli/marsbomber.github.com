--- 
layout: post
title: Install and setup Gitosis
wordpress_id: 370
wordpress_url: http://blog.elinkmedia.net.au/?p=370
date: 2011-04-02 17:14:00 +11:00
---
Github is great for social coding. To get private repositories, it's starting price is cheap and fair. However for someone who already owns a Linux VPS, as well as an  adequate backup solution, you may want to do it the hard way: setup your own remote central Git server using Gitosis. This is how I did it on my Ubuntu VPS, which is hosted on MediaTemple. My setup steps are pretty much copied from 2 great Gitosis setup tutorials, which I will reference at the foot of this post. This reference post probably benefits myself more than anyone else.

SSH into the remote VPS machine,

<strong>Step 1: Setup Git</strong>

{% highlight bash %}
sudo apt-get install git-core
{% endhighlight %}

<strong>Step 2: Setup Gitosis</strong>

{% highlight bash %}
sudo apt-get install python-setuptools
cd ~/downloads
git clone git://eagain.net/gitosis.git
cd gitosis
sudo python setup.py install
{% endhighlight %}

<strong>Step 3: Setup git user</strong>

{% highlight bash %}
sudo adduser --system --shell /bin/sh --gecos 'git version control' --group --disabled-password --home /home/git git
{% endhighlight %}

<strong>Step 4: Set up Gitosis admin user access and init Gitosis admin repository</strong>

From your local computer, generate (if not done before) and copy the public SSH key to the VPS server. Place and name it as "/tmp/id_rsa.pub", then

{% highlight bash %}
sudo -H -u git gitosis-init &lt; /tmp/id_rsa.pub
sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update
{% endhighlight %}

Up to this point, your have done all the works required on the VPS server. You will next jump back to your local machine and admin Gitosis from there.

First thing first, let's clone the Gitosis admin repository, so that you could tweak any admin configuration settings locally and update your Gitosis server with a simple git push

{% highlight bash %}
cd ~/VpsAdmin
git clone git@YOUR_SERVER:gitosis-admin.git
{% endhighlight %}

Next you'll create a new project locally, push it to the Git server hosted on the VPS, and make the new project's remoto repository accessible by other team members.

<strong>Step 1: Add new project to Gitosis admin config</strong>

{% highlight bash %}
vim ~/VpsAdmin/gitosis-admin/gitosis.conf
{% endhighlight %}

Add a new config block to the bottom of the file, like this

{% highlight bash %}
[group new_shared_project]
members = jim bob
writable = new_shared_project
{% endhighlight %}

Note that "new_shared_project" will be your new project's remote Git repository name. "jim" in this case is yourself (Gitosis admin) and "bob" is your team member, who will also have access to this project. "jim" matches the name your used in your public key, which was used when setting up Gitosis from previous steps. "bob" matches the name, which your team member used in his public key, which will be used in the next step.

<strong>Step 2: add team member's public SSH key to Gitosis</strong>

Obtain Bob's public SSH key and put it under ~/VpsAdmin/gitosis-admin/keydir. Give it a sensible file name so that you can easily relate it back Bob in the future.

<strong>Step 3: Commit changes and push it up to Gitosis server</strong>

{% highlight bash %}
cd ~/VpsAdmin/gitosis-admin
git commit -a -m &quot;create new repository and setup access for Bob&quot;
git push
{% endhighlight %}

<strong>Step 4: Create new project and push it to remote Gitosis server</strong>

{% highlight bash %}
cd ~/Workspace
rails new new_shared_project
cd new_shared_project
git init
git remote add origin git@YOUR_SERVER:new_shared_project.git
git add .
git commit -a -m &quot;initial import&quot;
git push origin master:refs/heads/master
{% endhighlight %}

<strong>Step 5: Share the remote repository with your team member</strong>

Team member Bob can access the remote repository from any computers that have his public SSH key installed

{% highlight bash %}
git clone git@YOUR_SERVER:new_shared_project.git
{% endhighlight %}

Reference
<ul>
	<li><a href="http://www.hackido.com/2010/01/installing-git-on-server-ubuntu-or.html" target="_blank">http://www.hackido.com/2010/01/installing-git-on-server-ubuntu-or.html</a></li>
	<li><a href="http://www.howtoforge.com/setting-up-gitosis-on-ubuntu" target="_blank">http://www.howtoforge.com/setting-up-gitosis-on-ubuntu</a></li>
</ul>
