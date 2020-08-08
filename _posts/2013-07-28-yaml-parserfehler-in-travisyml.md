---
layout: post
title: YAML-Parserfehler in travis.yml
date: '2013-07-28T15:53:00.000+02:00'
tags:
- Programmieren
- Travis-CI
modified_time: '2013-07-28T15:54:21.864+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-2888286012232760354
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/07/yaml-parserfehler-in-travisyml.html
---

Nach mehrstündigem Kampf mit der .travis.yml lag folgener Fehler `ERROR: An error occured while trying to parse your .travis.yml file.`
`Please make sure that the file is valid YAML.` an etwas ziemlich blödem. Und es waren nicht einmal die Tabs, die Travis [nicht so gut](http://hexcoder.us/2013/05/26/travis-ci-and-tabs/) findet. Es war ein anderer Fehler, den weder die On- und Offline-[Travis-Lints](http://lint.travis-ci.org/) noch diverse YAML-Validatoren fanden. Was ist wohl der Unterschied zwischen

`"echo ’Xauth $USERNAME USERNAME’ &gt;&gt; fhhn.conf"`

und

`"echo 'Xauth $USERNAME PASSWORD' &gt;&gt; fhhn.conf"`

Der [WYSIWYG-YAML-nach-JSON-Editor](http://yaml-online-parser.appspot.com/) hat es gezeigt: Im ersten nicht funktionierendem String steckt ein typographisch korrekter Apostroph, der leider in so etwas wie \u2019 umgewandelt wird was Travis gar nicht schmeckt. Der gerade Apostroph dagegen wird zum korrekten String.