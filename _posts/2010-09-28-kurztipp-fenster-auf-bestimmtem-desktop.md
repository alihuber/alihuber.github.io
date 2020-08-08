---
layout: post
title: "Kurztipp: Fenster auf bestimmtem Desktop starten"
date: '2010-09-28T15:10:00.001+02:00'
tags:
- Linux
- Ubuntu
modified_time: '2010-09-28T15:10:36.637+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-8120511825699543834
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/09/kurztipp-fenster-auf-bestimmtem-desktop.html
---

Das Erste was ich mache wenn der Rechner hochgefahren ist: Ich starte ein paar Programme welche ich auf verschiedene Desktops verteile. Schneller kann man dies mit dem CompizConfig-Einstellungs-Manager erledigen:  
Den Manager starten, auf "Fenster platzieren" klicken. Unter dem Reiter "Fixed Widow Placement" gibt es die Einstellung "Windows with fixed viewport". Wenn man auf "Neu" klickt, kann man mit dem Pluszeichen ein Fenster definieren, welches man platzieren will. Die Einstellungen für Typ und Wert erfährt man mit `xprop`, einem der unzähligen Hilfsprogramme des X-Window-Systems: `xprop` in einer Konsole starten: der Mauszeiger ändert sich nun zu einem Kreuz. Klickt man nun in ein beliebiges Fenster, erscheinen auf der Konsole alle möglichen Daten, unter anderem auch `WM_CLASS(STRING)` (welches der Fensterklassenname ist usw.) die man in die Eingabemaske für das zu platzierende Fenster füllt. Mit den X-Y Schiebereglern wählt man dann den Desktop aus auf welchem das Fenster erscheinen soll: fertig. Falls es nicht gleich funktioniert, einfach mit verschiedenen Einstellungen herumprobieren.