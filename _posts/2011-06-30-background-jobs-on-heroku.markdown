--- 
layout: post
title: Background jobs on Heroku
wordpress_id: 381
wordpress_url: http://blog.elinkmedia.net.au/?p=381
date: 2011-06-30 19:43:19 +10:00
---
I'm recently asked what my recent technology excitement is, of course in the context of programming. The name of <a href="http://www.heroku.com/" target="_blank">Heroku</a> came into my mind instantly. I've been using Heroku for all my Rails based applications recently. I'm amazed how easy it is to deploy and scale (to a degree) applications on to Heroku.

Heroku's default way of running background jobs is using the <a href="https://github.com/tobi/delayed_job" target="_blank">delayed_job</a> Ruby gem, which essentially records background jobs into a database table. And on Heroku, you can start a worker to monitor and process the job queue. This involves a lot of database polling as you could imagine.

So, in one of the applications I'm working on, I tried something more elegant (in my opinion). Github uses a Ruby gem called <a href="https://github.com/defunkt/resque" target="_blank">Resque</a> for their background jobs.
<ul>
	<li>Resque uses a <a href="http://redis.io/" target="_blank">Redis</a> based backend to store queued jobs, so it's fast in job logging and job retrieving.</li>
	<li>Resque allows you to separate jobs into different queues, so everything becomes better categorised.</li>
	<li>Resque also provides a Sinatra based web interface to monitor all jobs  including failures, so better monitoring and better debugging.</li>
</ul>
All good. I also opted to use a Resque plugin called <a href="https://github.com/ajmurmann/resque-heroku-autoscaler" target="_blank">resque-heroku-autoscaler</a>. This allows me to dynamically scale the number of Heroku workers up and down base on the jobs recorded in the queue. Since Heroku charges on worker usage, so with this auto scaler, I only pay for what's used.

As for the implementation, it's scattered across many files. Here's my implementation, which handles the delivery of welcome emails upon membership creation.

Gemfile

{% highlight ruby %}
gem 'resque', :require => "resque/server"
gem 'resque-heroku-autoscaler'
{% endhighlight %}

config/resque.yml

{% highlight ruby %}
development: 127.0.0.1:6379
test: 127.0.0.1:6379
{% endhighlight %}

config/initializers/resque.rb

{% highlight ruby %}
require 'resque'

heroku_environments = ["staging", "production"]
rails_root = ENV['RAILS_ROOT'] || File.dirname(__FILE__) + '/../..'
rails_env = ENV['RAILS_ENV'] || 'development'

unless heroku_environments.include?(rails_env)
  resque_config = YAML.load_file(rails_root + '/config/resque.yml')
  Resque.redis = resque_config[rails_env]
else
  uri = URI.parse(ENV["REDISTOGO_URL"])
  Resque.redis = Redis.new(:host => uri.host, :port => uri.port, :password => uri.password)
end
{% endhighlight %}

config/initializers/resque_heroku_autoscaler_setup.rb

{% highlight ruby %}
require 'resque/plugins/resque_heroku_autoscaler'

Resque::Plugins::HerokuAutoscaler.config do |c|
  c.heroku_user = 'heroku_user_name'
  c.heroku_pass = ENV['HEROKU_PASSWORD']
  c.heroku_app  = ENV['HEROKU_APP']

  if Rails.env.production?
    c.new_worker_count do |pending|
      (pending/5).ceil.to_i
    end
  end

  if Rails.env.development?
    c.scaling_disabled = true
  end
end
{% endhighlight %}

app/models/membership_observer.rb

{% highlight ruby %}
class MembershipObserver < ActiveRecord::Observer
  def after_create(membership)
    Resque.enqueue(MailerCallback, "MyMailer", :welcome_email, membership.id)
  end
end
{% endhighlight %}

app/models/mailer_callback.rb

{% highlight ruby %}
require 'resque/plugins/resque_heroku_autoscaler'

class MailerCallback
  extend Resque::Plugins::HerokuAutoscaler

  # resque queue name
  def self.queue
    :email_queue
  end

  # resque callback method
  def self.perform(mailer, email_type, *args)
    mailer.constantize.send(email_type, args).deliver
  end
end
{% endhighlight %}

Reference
<ul>
	<li><a href="http://www.heroku.com/" target="_blank">http://www.heroku.com/</a></li>
	<li><a href="https://github.com/tobi/delayed_job" target="_blank">https://github.com/tobi/delayed_job</a></li>
	<li><a href="https://github.com/defunkt/resque" target="_blank">https://github.com/defunkt/resque</a></li>
	<li><a href="http://redis.io/" target="_blank">http://redis.io/</a></li>
	<li><a href="https://github.com/ajmurmann/resque-heroku-autoscaler" target="_blank">https://github.com/ajmurmann/resque-heroku-autoscaler</a></li>
</ul>
