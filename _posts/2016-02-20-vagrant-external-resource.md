---
layout: post
title: "Requesting external resources from Vagrant boxes"
tags:
- Ruby
- Rails
- Vagrant
- Selenium
- Capybara
---

At my current work setup we use Vagrant to make the setup of our development environments as seamless as possible. The goal was to just enter 'vagrant up' and automate the rest of the development setup for Ruby on Rails applications whilst letting us use our familiar text edtiors on the host machine. We documented how to set up such an environment in this [coding guide](https://github.com/tilod/coding_guide/wiki/vagrant). This allows us to run the servers, tests and all other background services on the virtual machine and syncing the edited application files via unison, keeping our normal workflow.  
However, over time things became more complex. For example, an application running inside the Vagrant VM has to be tested with real browsers (via Selenium) instead of headless ones (due to it's heavy use of JavaScript), and the application is accessing another application's API running on the host machine. So, we had to tweak things a little:  
The first thing to do is to setup the Vagrant box so that it uses static IPs for itself and the host machine, this is the same if VirtualBox is running in bridged mode. The single most important thing you need to know is that there exists a `:private_network` - option and the host machine will _always_ be accessible via `192.168.50.1`. So for the guest machine you have to do this:

{% highlight ruby %}
Vagrant.configure(2) do |config|
  config.vm.box = 'ubuntu/trusty64'

  config.vm.network :private_network, ip: "192.168.50.4"
  # …
end
{% endhighlight %}

Now the VM will be reachable under the given IP. Of course you have to enter all hostnames in the `etc/hosts/` - file inside the guest machine. Following the conventions of the coding guide, this is done in the Bash script that bootstraps the Vagrant box, for example in `bootstrap_server.bash`:  

{% highlight bash %}
[…]
echo '192.168.50.4 localhost' >> /etc/hosts
echo '192.168.50.1 api_responder' >> /etc/hosts
[…]
{% endhighlight %}

Now the Vagrant box knows where to find the respective applications.  
The nice part about this is that you can now have any service or API required by the tested app running on the host machine, and it will be found. For example, for using an Selenium server on the host machine out of the Vagrant box you can set up your Capybara tests like this (in your `test/spec_helper.rb`):  

{% highlight ruby %}
[…]
url = "http://192.168.50.1:4444/wd/hub"
Capybara.register_driver :selenium_chrome do |app|
  capabilities = Selenium::WebDriver::Remote::Capabilities.chrome
  Capybara::Selenium::Driver.new(app, :browser => :remote, :url => url,
                                :desired_capabilities => capabilities)
end
[…]
Capybara.app_host = "http://192.168.50.4:4567"
[…]
{% endhighlight %}

This means that the app inside the VM is reachable on port 4567, but the test driver will use the Selenium server which has to run on the host machine. The app under test has to run on its own dev server inside the Vagrant VM, and you will have to install all needed binaries for other web driver browsers on the host machine. When you now start the Capybara tests inside the VM, the tests will magically use the browsers on the host machine but run inside the VM.
