---
layout: post
title: 'Short Tip: Firefox stopped reloading Rails assets'
date: '2014-11-01T05:33:00.002+01:00'
tags:
- Ruby
- Rails
- Firefox
modified_time: '2014-11-01T07:34:53.015+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-9007170122241142727
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2014/11/short-tip-firefox-stopped-reloading.html
---

I just updated Firefox from version 33.0 to 33.0.2, and all of a sudden it stopped serving assets correctly. I changed some CSS file, and despite a hard refresh (cmd + shift + r) the changes were not loaded correctly during development. I found the solution [here](http://forums.mozillazine.org/viewtopic.php?f=9&amp;t=381118&amp;start=0), somehow the "back-forward-cache" seems to be doing something different now. I changed the setting and now it's working again as intended.