---
layout: post
title: How to get a basic (Travis | Jenkins)-CI clone in 70 LOC or less
date: '2013-04-01T20:58:00.001+02:00'
tags:
- Programming
- Travis-CI
- Ruby
- Sinatra
modified_time: '2013-07-28T15:53:51.752+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-3141321207804845962
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/04/how-to-get-basic-travis-jenkins-ci.html
---

On my frequent trips to Continuous Integration Land I struggled to get a local instance of Travis CI running. It seems the Travis folks are overwhelmed by their own success and support for a use case like that just has no prioriy for them, which is perfectly understandable. I then switched to Jenkins, and while setting up the machine for running the builds on (for rails projects by the way) I asked myself: How difficult can it be to get a somehow similar functionality which much less effort?  

It turned out to be pretty simple.  

I made up some fundamental features which both CI systems provide:  
They clone a repository from GitHub, either manually or with GitHub's URL-hook-functionality, or both. Travis installs all gems anew every time, whilst Jenkins just uses the normal Unix user environment to do all the tasks, it just creates a temporary bash environment. To mimic the behaviour of Travis, you could create a new gemset before each build and remove it afterwards.  
After the repo is cloned, you can run some bash commands to setup the gems ('bundle install') and create the databases ('rake db:migrate'). The big advantage of Travis for this is that the virtual machines on which the builds run have everything for (nearly) every possible configuration pre-installed. At build-time you just specify: "I want a MySQL-Database called X" and it gets set up autmatically. With Jenkins, you have to set up your environment and all needed packages first, but then you are able to run the same build over and over again.  
The build itself is started with just another shell command. By default, the command for rails projects for all tests to run is 'rake'.  
To recap: Clone a rails project (manually or through hook), and run three commands in a (more or less) safe environment. Then you would wanna have some info whether the build has passed, when it was started and you want to look at the output of the commands later.  
[This one-page Sinatra application does all this](https://github.com/alihuber/minimal-ci/blob/master/main.rb), and it is 68 lines of Ruby code plus configs and one HAML file for displaying the builds.
It uses a (basic, just Ruby installed) running Vagrant box in its root directory and the 'vagrant ssh -c' command for the build steps. It displays the running build by using Sinatra's stream method. Triggering a build is just one POST-method which handles both manually entered URLs and the GitHub web hook. For minimal effort it uses a simple DataMapper-powered sqlite database in the root directory for storing run builds. Vagrant is cool for this because you can get a new VM with just two shell commands, and with a proper shell provisioning script you have no setup pain at all. The GitHub web hook works just by pointing to that method, e.g. http://my-server.com/start_build.  
The outcome of this experiment calls for a Travis-Jenkins hybrid. I want the ease and goodness of Ruby with the level of control and features of Jenkins. I even got a cool (yet secret) project name - so be prepared!