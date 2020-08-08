---
layout: post
title: Fun with PaaS & dokkufy
date: '2014-10-26T15:00:00.000+01:00'
tags:
- Ruby
- Ubuntu
- PaaS
- Docker
modified_time: '2014-11-01T07:35:05.340+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-90570856887439820
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2014/10/fun-with-paas-dokkufy.html
---

Since I had to work with certain PaaS implementations for the first time a few months ago, I wanted to try out something similar for myself. One very cool way I found are the [dokku shell scripts](https://github.com/progrium/dokku/). Unfortunately, I couldn't get ssh and Git work properly with my local virtual machine, so I switched to the [dokkufy gem](https://github.com/cbetta/dokkufy) and it worked like a charm. So, here it is: The fool-proof way to get a local virtual machine to pose as a heroku/cloud foundry replacement, serve a simple rack web application and even map it to an occupied domain name!  

I did set up a fresh VM (I used VirtualBox) with Ubuntu 14.04 and set the network settings to bridged mode. This will be somewhat tedious every time the IP changes, but it seemed to be the least complicated way. I just named the user of the VM 'dokku'.  
I then installed `openssh-server` (I also installed `ssh-askpass`, but in the meantime I'm not certain the scripts actually need it) and made sure I could ssh into the virtual machine.  
On the host machine I did set up a directory with the [simplest possible rack app from the heroku docs](https://devcenter.heroku.com/articles/rack). Then I installed the dokkufy gem and ran the `dokkufy server` script. It asks for the IP address, hostname and user name (and several times for the password, that's not an error). The IP address was something like 192.168.0.12, and I decided to use ali.io as a domain name, just to realize a bit later that this name is already taken. But as we will see in a moment, that's no big deal since dokku sets the pushed applications to be subdomains of the set domain name and you can manipulate the hosts file.  
Since dokkufy uses dokku, but in a nicer, more interactive way (as described [here](http://cristianobetta.com/blog/2014/08/05/your-mini-heroku-with-dokku-and-dokkufy)), it will set up Git, docker and nginx and has to download about 400 mb. After it's finished you should be able to hit 192.168.0.12 (or whatever your VM's IP is) and see the welcome screen from nginx.  
After that I ran `git init` in the directory of the rack app and committed all the files. Then I ran the `dokkufy app` command, which asked me again about user name and IP, and did set up Git remotes (the part I couldn't get to work with dokku itself). The output from `git push dokku master` should look familiar to everyone who has used cloud foundry, and the app will be deployed into a docker container inside the VM (see the links above).  
To reach the app under the set domain name, I had to add the following to the `/etc/hosts`-file on the host machine: `192.168.0.12 hello.ali.io` Now http://ali.io gave me the personal page of some guy, but hello.ali.io gave me a 'bad gateway' (111: Connection refused while connecting to upstream)  error. As it turned out, these lines in `/etc/nginx/nginx.conf` in the VM were missing:

{% highlight bash linenos %}
http {
  ...
  server {
    server_name .ali.io;
  }
}
{% endhighlight %}

After restarting nginx hello.ali.io gave me the long awaited "Hello World!" message.