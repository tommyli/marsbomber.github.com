---
layout: post
title: Ruote workflow engine with RabbitMQ (Part 1)
---
At MYOB, I am assigned the task to build a component based workflow application. There are several requirements this application has to address. Here are a few

* Decoupled components
* Resilient workflow
* Configurable workflow
* Every single piece of the application needs to be scalable

A messaging based system becomes somewhat a natural choice to address the decoupling, distributable and scalable issue. While in the hunt of a good workflow system, I was pointed to a Ruby gem called [Ruote](http://ruote.rubyforge.org/) by [@jmettraux](https://twitter.com/jmettraux). Ruote has also got several companion gems. One of them is [ruote-amqp](https://github.com/kennethkalmer/ruote-amqp).

[Rails Sample Ruote AMQP](https://github.com/hallelujah/rails-sample-ruote-amqp) is the most complete example I could find on Github. This excellent sample project almost covers all aspect. However its Rails default README file doesn't do its justice. I had to dig hard to figure out how all the pieces are put together.

Enough mumbling from me. The following are the steps I implemented to get an end-to-end Ruote + RabbitMQ going on my MBP. As prerequisites, I have Homebrew and RVM installed.

## Install RabbitMQ

This is rather simple with the help from Homebrew.

{% highlight bash %}
brew install rabbitmq
rabbitmq-plugins enable rabbitmq_management
rabbitmq-server
{% endhighlight %}

Now you can monitor your running RabbitMQ instance from the browser at [http://127.0.0.1:55672/mgmt/](http://127.0.0.1:55672/mgmt/) (login using guest - guest)

## Setup RuoteKit enabled Rails project

Create a new rails app called lcp
{% highlight bash %}
rails new lcp
{% endhighlight %}

Step in the project root, create .rvmrc
{% highlight bash %}
rvm 1.9.3@lcp --create
{% endhighlight %}

Run bundle install after amending Gemfile with
{% highlight ruby %}
gem "ruote", "~> 2.2.0"
gem "ruote-kit", "~> 2.2.0.3"
gem "ruote-amqp", "~> 2.2.0"
{% endhighlight %}

Add ruote kit initializer. As you could see, the file shown is a work in progress.
{% highlight ruby %}
# config/initializers/ruote-kit.rb

# TODO: move setting values into a config file
AMQP.settings[:host] = '127.0.0.1'
#AMQP.settings[:vhost] = '/'
#AMQP.settings[:user] = 'guest'
#AMQP.settings[:pass] = 'guest'

# run ruote engine without worker
# run ruote rake task for workers, rake ruote:run_worker
RuoteKit.engine = Ruote::Engine.new(RUOTE_STORAGE)

if ENV["RAKE_TASK"]
  RuoteAMQP::Receiver.new(RuoteKit.engine)
else # don't register participants in rake tasks
  RuoteKit.engine.register do
    participant :ldap, RuoteAMQP::ParticipantProxy, :queue => "ldap_job"
    participant :email, RuoteAMQP::ParticipantProxy, :queue => "email_job"
    participant :notifier, RuoteAMQP::ParticipantProxy, :queue => "notify_job", :forget => true
    #participant :editor

    # register the catchall storage participant named '.+'
    catchall
  end
end

# when true, the engine will be very noisy (stdout)
RuoteKit.engine.context.logger.noisy = false
{% endhighlight %}

Add ruote worker rake task
{% highlight ruby %}
# lib/tasks/ruote.rake

namespace :ruote do
  desc "Run a worker thread for ruote"
  task :run_worker => :environment do
    RuoteKit.run_worker(RUOTE_STORAGE)
  end
end
{% endhighlight %}

Last not least, mount RuoteKit to our Rails app by adding the following to the routes file
{% highlight ruby %}
# ruote-kit
match '/_ruote' => RuoteKit::Application
match '/_ruote/*path' => RuoteKit::Application
{% endhighlight %}

This completes first half of the story. We now have a running RabbitMQ instance with admin monitoring, and a RuoteKit enabled Rails app. The Rails app has several RuoteAMQP participants registered, a ruote rake task ready to run Ruote workers, and of course the RuoteKit rack app that allows us to manually launch Ruote jobs.

In the next post, I'll complete the full picture by implementing a DaemonKit generated Ruote daemon script, which processes the AMQP jobs published by the ruote worker running on the Rails app side.

But for now, PEACE!