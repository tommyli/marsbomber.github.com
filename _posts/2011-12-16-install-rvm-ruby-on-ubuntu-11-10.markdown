---
layout: post
title: Install RVM Ruby on Ubuntu 11.10
---
Because OS X crashes every 2nd day, I thought it's time to give Ubuntu another good spin. First task is to setup my Ruby working environment.

On a fresh Ubuntu 11.10 VM, I firstly installed curl and git via apt-get
{% highlight bash %}
sudo apt-get install curl git-core
{% endhighlight %}

I then installed RVM using the normal RVM installation command
{% highlight bash %}
bash < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
{% endhighlight %}

Then I tried to install Ruby 1.9.3, I ran into all sorts of issues, readline not available, yaml failed to build, etc. After some fooling around, I found that all I needed to do was to RTFM! Running the following gives me all required packages needed to install an MRI (and others) ruby...
{% highlight bash %}
rvm requirements
{% endhighlight %}

Believe or not, after doing the sudo apt-get install line from rvm requirements, RVM is happy as!

Lesson learnt: before start blaming a tool (especially a free one!), let's check what the README says first!
