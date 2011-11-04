--- 
layout: post
title: Manage Rails app configurations
wordpress_id: 404
wordpress_url: http://blog.elinkmedia.net.au/?p=404
date: 2011-07-23 23:06:12 +10:00
---
Most of applications require application configuration files. The Rails applications I worked on pretty much all require an initialiser (or should it be the Rails convention initialiZer) to load a configuration YAML file. I'm aware that there are gems out there providing this kind of configuration solutions, such as SettingsLogic. For a simple problem like this one, I think we could roll our own too (oh, the not invented here syndrome).

The codebase I worked on typically has an initialiser called load_config.rb. It's got something like this
{% highlight ruby %}
# config/initializers/load_config.rb

AppConfig = YAML.load_file(File.join(RAILS_ROOT, 'config', 'config.yml'))

# Override config options by correct environment
env_options = AppConfig.delete(RAILS_ENV)
AppConfig.merge!(env_options) unless env_options.nil?
{% endhighlight %}

Easy and good. There's one more thing I'd love to add to this. I'd love to have a local config file, config.local.yml, that can overwrite config values defined in the config.yml. And this config.local.yml is not source controlled, means every developer can make tweak the configs to tailor to their own development environment without needing to touch the source controlled configuration file, config.yml.

To do this, just add the following block of code to the load_config.rb
{% highlight ruby %}
local_config_filepath = File.join(RAILS_ROOT, 'config', 'config.local.yml')
if File.exist?(local_config_filepath)
  AppConfig.merge!(YAML.load_file(local_config_filepath))
end
{% endhighlight %}
