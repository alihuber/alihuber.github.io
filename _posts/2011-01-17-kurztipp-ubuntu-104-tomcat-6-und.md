---
layout: post
title: "Kurztipp: Ubuntu 10.4, Tomcat 6 und Netbeans 6.9.1"
date: "2011-01-17T23:45:00.000+01:00"
tags:
- Linux
- Java
- Studium
- Ubuntu
modified_time: "2011-01-17T23:45:33.132+01:00"
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-5876080586681076528
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2011/01/kurztipp-ubuntu-104-tomcat-6-und.html
---

Das Problem, daß NetBeans beim Verwenden des Tomcat nicht gleich so will wie der Benutzer trifft wohl noch mehrere.  
Nachdem Tomcat installiert wurde findet man durch `whereis` und Konsorten folgende Verzeichnisse heraus: `/usr/share/tomcat6` und `/etc/tomcat6`. NetBeans will bei der Serverregistrierung aber mit beiden nicht funktionieren und sagt entweder daß das Verzeichnis ungültig ist oder die Datei `$CATALINA_HOME/server.xml` nicht gelesen werden kann. Seltsam ist, daß die Dateien welche wohl in `$CATALINA_HOME` liegen sollten sich in `/usr/share/tomcat6/bin` befinden, die Datei `server.xml` und andere Konfigurationsdateien aber in `/etc/tomcat6`. Um die Verwirrung komplett zu machen gibt es noch das Verzeichnis `/var/lib/tomcat6`, welches komischerweise durch `whereis` nicht angezeigt wird und in dem sich schon mal korrekte Symlinks zu obigem `/conf/`-Verzeichnis befinden. NetBeans mag diesen Pfad aber auch nicht.  
Die Lösung: Im Verzeichnis `/usr/share/tomcat6` legt man durch  

{% highlight bash %}
sudo ln -s /etc/tomcat6/ conf
{% endhighlight %}

einen  Link, so daß NetBeans diesen Pfad akzeptiert und die Sache läuft.