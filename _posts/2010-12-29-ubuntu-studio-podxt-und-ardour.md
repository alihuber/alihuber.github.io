---
layout: post
title: Ubuntu Studio, PodXT und Ardour
date: '2010-12-29T14:55:00.001+01:00'
tags:
- JACK
- Ubuntu
- Linux
- Musik
- PodXT
- Ardour
modified_time: '2011-04-11T23:46:43.790+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-3959531557215603028
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/12/ubuntu-stuido-podxt-und-ardour.html
---

Als ich mich dazu entschloß, Gitarrenaufnahmen mit dem Line6 PodXT als Interface und dem freien Recordingprogramm Ardour zu realisieren, hatte ich mich schon auf stundenlanges konfigurieren und endlose Probleme eingestellt. Aber es kam alles ganz anders.  
Zuerst installierte ich das aktuelle Ubuntu Studio, dann das Paket `line6-usb-source` aus den Repositories. Als ich dann anfing, mich mit dem JACK-Soundserver zu beschäftigen kam erstmal die Ernüchterung: Der Pod wird nicht angezeigt. Zu meiner Schande muß ich gestehen, daß ich mich nicht and das RTFM-Prinzip gehalten hatte und nun etwas Zeit mit googeln verschwendete. In der [Anleitung](http://www.tanzband-scream.at/line6/driverdocs.pdf)  steht nämlich wie es gemacht wird:  
Mit `aplay -l` wird die hw-Nummer des neuen Audiogeräts herausgefunden, und mit `jackd -d alsa -d hw:1` wird der JACK-Server gestartet und als alsa-Gerät jenes an Adresse 1 benutzt. Voilà: Der Pod fungiert nun als Soundkarte und selbst Systemsounds werden über ihn ausgegeben. Und jetzt kam so was, wie es nur alle Jubeljahre einmal vorkommt:  
Ardour gestartet, neue Spur angelegt, auf "record" gedrückt und: funktioniert. Ganz von allein. Die Kanäle wurden vom JACK-Server automatisch richtig gemappt! Für jemanden, der von Sachen wie VST und Konsorten keine Ahnung hat und Cubase bisher nur als "Auf-Record-Button-Drück-Äffchen" kennt, eine Offenbarung!  
Wird jetzt in anderen Audioprogrammen als Sounddevice ebenfalls der Jack-Server angegeben, lassen sich lustige Dinge anstellen: Es läßt sich super-einfach ein Song abspielen (über den Kopfhörerausgang des Pod), dazu wird mitgeschrammelt, aber nur das gespielte wird von Ardour aufgezeichnet. Genial zum Üben, schnell eingerichtet und alles völlig umsonst!