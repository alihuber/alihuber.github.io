---
layout: post
title: Methoden und Blocks - Nachtrag 2
date: '2012-08-14T16:48:00.000+02:00'
tags:
- Ruby
- Informatik
modified_time: '2012-08-14T16:48:25.981+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-4091431677667779799
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/08/methoden-und-blocks-nachtrag-2.html
---

 Im Post vom 09. Juli über Ruby Blocks und Procs steht schon richtig, dass all diese Verkürzungen mit dem Ampersand ("&") gehen, weil er in Verbindung mit Methodenargumenten ein Wrapper für `to_proc` ist. Leider kommt es noch sehr auf den Kontext an, was dann tatsächlich konvertiert wird: Ein Block in ein Proc oder umgekehrt. Zum Glück hat [hier](http://www.elonflegenheimer.com/2012/07/08/understanding-ruby-closures.html) jemand alle Kombinationen schön zusammengeschrieben.