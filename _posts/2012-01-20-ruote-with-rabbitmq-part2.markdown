---
layout: post
title: Ruote workflow engine with RabbitMQ (Part 2)
---
I explained how I setup Ruote and RabbitMQ in [last post](/2012/01/20/ruote-with-rabbitmq-part1/). From there on, you basically already have a working system. All there's left is to have external services subscribe to the RabbitMQ job queues, parse the workitem JSON, do whatever and post the modified/amended JSON back to RabbitMQ, default queue is named "ruote_workitems", which is what the RuoteAMQP receiver listens on.

In this post, I'll demonstrate how a DaemonKit generated daemon script consumes the RabbitMQ job messages and how we construct a DaemonKit compatible Ruote process definition.

Generate DaemonKit project
{% highlight bash %}
rvm 1.9.3@daemonkit_ruote
gem install daemon-kit
daemon-kit daemon -i ruote
cd ruote
bundle
{% endhighlight %}

Make daemon listen to correct AMQP queues by editing ruote.yml config
{% highlight ruby %}
defaults: &defaults
  amqp:
    queues:
      - ldap_job
      - email_job
{% endhighlight %}

Edit participant class by adding the following 2 methods
{% highlight ruby %}
def ldap
  workitem["ldap"] = "done"
  # just to demonstrate the AMQP message carries the ID fields and it can be consumed
  workitem["original_cust_id"] = workitem["cust_id"]
end

def email
  workitem["email"] = "done"
end
{% endhighlight %}

Let's start everything up
{% highlight bash %}
rabbitmq-server
cd /my-ruotekit-enabled-rails-project && rails s
RAKE_TASK=true rake ruote:run_worker
cd /my-daemonkit-ruote-project && bin/daemon
{% endhighlight %}

Now let's work out how we wire everything up by carefully crafting a process definition.

Go to [http://localhost:3000/_ruote/processes/new](http://localhost:3000/_ruote/processes/new) in your browser and enter the following as process definition
{% highlight ruby %}
Ruote.process_definition :name => 'suspend_account', :revision => '0.1' do
  sequence do
    ldap :command => "/sample/ldap"
    # slot in editor to check ldap process result status
    email :command => "/sample/email"
    # slot in editor to check email process result status
    notifier :forget => true # this could be email or final results back in notification queue
  end
end
{% endhighlight %}

Enter the following into the workitem fields box
{% highlight ruby %}
{"cust_id": 111}
{% endhighlight %}

Then launch it! If everything goes well(it should!), you'll see no processes listed under the ruotekit app interface. And you'll have 4 queues listed under RabbitMQ's management interface.

- email_job, 0 job
- ldap_job, 0 job
- notify_job, 1 job
- ruote_workitems, 0 job

By popping the message out of the notify_job queue, you'll see the serialised JSON string contains ldap done and email done key-value pairs. It shows me the job is done, and all participants are exercised correctly.

Now, we go back a step and explain a little on the process definition we crafted.

If a remote participant will be processed by a DaemonKit daemon script, we need to give each of those participants a hash, which contains a :command key. The value for :command key needs to follow a certain convention too. The convention is "/participant_class_name/method_name". DaemonKit will classify and constantize "participant_class_name" in to ParticipantClassName object, and "method_name" will be sent to it. So in our example, when the daemon script sees command "/sample/ldap", it'll invoke a call to "Sample#ldap".

The value we entered into the workitem fields textbox is simply be merged into the initial workitem hash, and it'll be sent along to all participants.

That is it. Hope it all makes sense, and please tweet me if you found I've got any bits laid out wrongly or explained wrongly.