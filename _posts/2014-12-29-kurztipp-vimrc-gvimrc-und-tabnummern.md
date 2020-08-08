---
layout: post
title: 'Kurztipp: .vimrc, .gvimrc und Tabnummern'
date: '2014-12-29T13:53:00.000+01:00'
tags:
- Vim
modified_time: '2014-12-29T13:53:00.459+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-8147032767613854959
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2014/12/kurztipp-vimrc-gvimrc-und-tabnummern.html
---

Einer meiner Lieblingsshortcuts unter (g/m)vim ist es, mit `gt` und `gT` zwischen Tabs zu springen. Ich arbeite mit extrem vielen offenen Tabs, und mit `n gt` kann auch direkt auf einen der offenen Tabs gesprungen werden. (Ich tue das sogar mit Chrome und [Vimium](http://vimium.github.io/), das ist aber eine andere Geschichte). Das Einzige was nervt ist die fehlende Anzeige der Tabnummern, ab 10-13 offenen Tabs wird die Sache etwas unübersichtlich. Eine kurze Suche fördert zu Tage, dass mit `:set guitablabel=%N\ %f` die Tabnummern eingeblendet werden können, was während dem Betrieb auch funktioniert. Also ab in die .vimrc damit. Leider tut sich danach gar nichts, auch die Option in einem `if has("gui_running") ... endif` zu setzen brachte nichts. Nach noch viel mehr googelei fiel mir auf, dass diese Option nicht in der .vimrc, sondern in der .gvimrc gesetzt werden muss, von deren Existenz ich bisher gar nichts wusste. Alle .vimrc-Einstellungen in die .gvimrc zu packen bringt aber auch nichts, es müssen schon zwei Dateien sein. Einige Erklärungen zur .gvimrc gibt es [hier](http://vimdoc.sourceforge.net/htmldoc/gui.html). Kurz: Einige Einstellungen (wie `guitablabel`) müssen unbedingt in die .gvimrc, die auch extra nach der .vimrc geladen wird.