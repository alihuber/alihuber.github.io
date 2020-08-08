---
layout: post
title: Testing file downloads with Sinatra, Capybara and Travis-CI
date: '2013-02-23T18:10:00.000+01:00'
tags:
- Programming
- Capybara
- Ruby
- Sinatra
modified_time: '2013-02-23T18:10:41.686+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-5526524417408376548
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/02/testing-file-downloads-with-sinatra.html
---

I recently had the problem to test the content of downloaded files in an non-Rails environment. It's a simple Sinatra-app that returns a file via a GET-Request and the `send_file`-helper. My first approach was to check for the response-body after Capybara clicked the 'Download'-button, but I got the same error as [this guy](https://groups.google.com/forum/?fromgroups=#!topic/cukes/YTe8_0O_Y0o) or I got the `Capybara::NotSupportedByDriverError`. It seems you can't test the response body with Capybara nor is the `last_response`-method of `Rack::Test` an option. Luckily, I found [this blog post](http://collectiveidea.com/blog/archives/2012/01/27/testing-file-downloads-with-capybara-and-chromedriver/). The gist of it is to use the Chromedriver and a little `DownloadHelper`-module. But I wasn't using Rails, Cucumber or other fancy stuff, so I had to fine-tune some things first. In a recent update of Capybara, the Chromedriver is somehow removed and I had to install the binary manually: I just downloaded it [from here](http://code.google.com/p/chromedriver/downloads/list) and moved it to any directory on my PATH. The reason Chromedriver works best is that once the default download path of Chrome is set like in above blog post, the file gets downloaded automatically without any further user interaction, whilst I was not able to control the Selenium-built-in Firefox to behave that way. The next thing you need is a temporary download location for your files you wish to test be tested. Since I'm not using rails and I don't have a Rails.root, I made a 'tmp'-directory in my Sinatra base directory and set this directory as default download location for the Chromedriver. All you really need of above's blog post `DownloadHelper`-module is a small method that reads in the downloaded file from your temporary directory and returs the content to be compared to your desired string in your test case (and deletes the file afterwards). You really don't need a full-fledged module for that. You could just have this method:

{% highlight ruby linenos %}
def get_content
  sleep 2
  file = File.open(Dir.glob(Dir.pwd + "/tmp/*").first, "r")
  content = file.read
  file.close
  Dir.glob(Dir.pwd + "/tmp/*").each do |file|
    File.delete(file)
  end
  content
end
{% endhighlight %}

`Dir.pwd` returns the base directory of the Sinatra app, and it deletes all files in the 'tmp' directory because Chrome generates also a temporary file for each download. It all worked fine locally, but what if I deployed it on, say Heroku, and had it tested via a Travis-CI build? Luckily, the root account on the virtual machines on which the builds run have no password. You can just download the Chromedriver binary and move it to `/usr/bin/` during your build. Since you can use arbitrary shell commands in your `.travis.yml` I do this via `sudo wget` and  `sudo mv` before the main script and all the tests pass as they do locally.