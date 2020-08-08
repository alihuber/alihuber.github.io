---
layout: post
title: 'Kurztipp: MySQL, kein Zugriff mit angelegtem User'
date: '2013-02-26T22:17:00.001+01:00'
tags:
- MySQL
- Ubuntu
modified_time: '2013-02-26T22:17:41.356+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-816430814301291986
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/02/kurztipp-mysql-kein-zugriff-mit.html
---

Für einen Test wollte ich heute eine MySQL-Datenbank anlegen und einen eigens dafür angelegten User von entfernt darauf zugreifen lassen. Also Portweiterleitung eingerichtet und Datenbank nebst User (zum Beispiel mit [dieser](http://ewus.de/en/tipp/en/create-mysql-database-and-user) oder [dieser](http://www.internist-schmidt-lohfelden.de/ReadMe/Anlegen%20neuer%20Benutzer%20unter%20MySQL.htm) Anleitung) mit der Konsole angelegt. Aber im MySQL-Workbench ist das angelegte Schema mit dem frischen User einfach nicht zu sehen, egal wie man noch an den Rechten schraubt. Und nicht mal über die Konsole auf der Datenbank einloggen kann man sich! Die Lösung steht dann natürlich [hier unten](http://dev.mysql.com/doc/refman/5.1/de/access-denied.html). Im Host-Wert für den neuen User darf kein "Jokerzeichen" wie % stehen, weil es sonst Probleme mit Verbindungen gibt, die von `localhost` kommen. Und nachdem ich wieder mal viel zu lange rumprobierte und dann einfach gleich die entfernte Verbindung testete lief alles wie geschmiert.