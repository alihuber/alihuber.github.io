---
layout: post
title: 'Kurztipp: Standardloginshell bei *BSD'
date: '2010-03-11T00:41:00.006+01:00'
tags:
- Unix
modified_time: '2010-03-11T00:55:09.025+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-525914782895348913
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/03/kurztipp-standardloginshell-bei-bsd.html
---

Wenn man beginnt sich in eins der *nixe einzuarbeiten kommt es fast immer vor, dass man für eine eher kleine Änderung unheimlich viel Zeit verplempert weil man noch nicht weiß wie die richtigen Befehle lauten.  

Neulich war ich wieder in so einer Situation und habe dooferweise den Befehl vergessen den ich suchte und mich ca. eine halbe Stunde lang totgegoogelt. Wie bei solchen Dingen üblich, findet man dann die Lösung, freut sich so dermaßen dass man sie endlich gefunden hat, führt sie durch und hat sie fünf Minuten später wieder vergessen und natürlich nirgends aufgeschrieben.  
Aber jetzt habe ich ja einen Ort, wo ich solche Sachen konservieren kann, und sie auch noch anderen zur Verfügung stellen, damit die sich nicht auch noch totgoogeln müssen. Also los:  

Eines der ersten Dinge die man ändert wenn man sich eins der BSD-Unixe aufgespielt hat ist die Änderung der Standardloginshell auf die gute alte Bash, wie man sie unter einem Linux gewohnt ist:  

**FreeBSD:**  
{% highlight bash %}
# pkg_add -r bash
{% endhighlight %}

**OpenBSD:**  
{% highlight bash %}
# pkg_add -v ftp://ftp.openbsd.org/pub/OpenBSD/4.6/packages/amd64/bash-4.0.24.tgz
{% endhighlight %}


**NetBSD:**  
Man kann pkg_add wie oben benutzen, bequemer geht es aber wenn die PKG_PATH-Variable gesetzt wird:
{% highlight bash %}
# export PKG_PATH="http://ftp.netbsd.org/pub/pkgsrc/packages/NetBSD/amd64/5.0.1_2009Q4/All"
{% endhighlight %}

Danach funktioniert ein
{% highlight bash %}
# pkg_add -v bash
{% endhighlight %}

Dann ändern wir einfach die Standardhell mit dem chsh -s Befehl, welcher die Standardshell für den jeweiligen User ändert:

**FreeBSD:**  
{% highlight bash %}
# chsh -s /usr/local/bin/bash
{% endhighlight %}

**OpenBSD:**  
{% highlight bash %}
# chsh -s /usr/local/bin/bash
{% endhighlight %}

**NetBSD:**  
{% highlight bash %}
# chsh -s /usr/pkg/bin/bash
{% endhighlight %}

Für Useraccounts die nicht Root sind funktionierts genauso.