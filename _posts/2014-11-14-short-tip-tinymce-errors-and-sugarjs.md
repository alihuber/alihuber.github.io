---
layout: post
title: 'Short Tip: TinyMCE errors and Sugar.js'
date: '2014-11-14T12:39:00.001+01:00'
tags:
- JavaScript
- Programming
- Rails
- Ruby
modified_time: '2014-11-14T12:39:41.482+01:00'
thumbnail: http://3.bp.blogspot.com/-4nuMpARqmPA/VGXnqJ00MyI/AAAAAAAAAC4/-VE4FkAteWM/s72-c/tiny.gif
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-9160161867048913784
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2014/11/short-tip-tinymce-errors-and-sugarjs.html
---

Just in case you are using TinyMCE in your Rails project and something like this has ever happened to you:

![tiny.gif](http://3.bp.blogspot.com/-4nuMpARqmPA/VGXnqJ00MyI/AAAAAAAAAC4/-VE4FkAteWM/s1600/tiny.gif)

including both TinyMCE and Sugar.js is the culprit. Somehow Sugar.js's methods which are defined on native JavaScript objects interfere with TinyMCE (which is about 100k LOC, according to `cloc`, so don't bother debugging). Isolating the cause of the errors shown above did cost me several hours of desperate debugging, because they were not noticed for months, and I had to inspect every change that was made since it was reportedly working.