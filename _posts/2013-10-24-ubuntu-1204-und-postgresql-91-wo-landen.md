---
layout: post
title: 'Ubuntu 12.04 und PostgreSQL 9.1: Wo landen die Logdateien?'
date: '2013-10-24T14:39:00.000+02:00'
tags:
- PostgreSQL
- Ubuntu
modified_time: '2013-10-24T14:39:17.224+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-7632798655156910035
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/10/ubuntu-1204-und-postgresql-91-wo-landen.html
---

Auf den ersten Blick verteilt sich eine Installation von PostgreSQL auf viele verschiedene Ordner. Ein `locate postgres` brachte bei mir 762 Zeilen zum Vorschein. Nachdem ich alle nicht relevanten Einträge beseitigt hatte (und das waren viele, weil auch Konfigurationen und Bibliotheken aus RubyGems und Python-Paketen in der Liste landen) blieben noch diese Installationsorte übrig:

(1) `/etc/postgresql`
(2) `/usr/share/postgresql`
(3) `/var/lib/postgresql`
(4) `/usr/lib/postgresql`

In (1) landen Konfigurationsdateien wie `pg_hba.conf` und die `postgresql.conf`. In (2) landen die Konfigurationsdateien wie sie sowieso schon in (1) angelegt wurden als Samples, außerdem liegen hier die .sql-Dateien der Extensions, die man-pages der Onlinehilfe des Interpreters und Zeitzonendaten. In (3) ist das Verzeichnis, in der `postgresql.conf` als `data_directory` angegeben ist und in dem die eigentlichen Datenbanken gespeichert werden. In (4) liegen dann noch die Binaries und Libraries.  

Das Verwirrende ist wie mit Logs umgegangen wird: wenn in der `postgresql.conf` in (1) die Logs mit

{% highlight bash %}
log_destination   = 'stderr'
logging_collector = on
log_directory     = 'pg_log'
{% endhighlight %}

aktiviert werden, wird das Verzeichnis pg_log in (4) erzeugt, und es landen nur die Nachrichten mit dem in der `postgresql.conf` festgelegten Loglevel in der Datei. Die Logs des Daemons an sich befinden sich wie gehabt in `/var/log/postgresql`.