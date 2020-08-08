---
layout: post
title: Java Media Framework und Eclipse
date: '2010-03-16T05:31:00.002+01:00'
tags:
- Java
- Eclipse
- Windows
modified_time: '2010-03-16T05:37:34.533+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-6909688412545895239
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/03/java-media-framework-und-eclipse.html
---

"Und wie Sie sehen, sehen Sie nichts." Da es so offensichtlich nicht nur mir nach der Installation des JMF + des MP3-Plugins erging beschreibe ich mal wie ich es bei mir zum laufen gebracht habe.  
Direkt nach der Installation konnte Eclipse mit den import-Anweisungen wie z.B.

{% highlight java %}
import javax.Manager.*;
import javax.Player.*;
{% endhighlight %}

nichts anfangen. Auch das Einfügen der JARs in den Buildpath des Projekts welche unter

{% highlight batch %}
C:\Programme\JMF2.1.1e\lib
{% endhighlight %}

liegen brachte nichts.  
Ich habe auf meinem System komischerweise das JDK und noch zusätzlich das JRE installiert (obwohl das JDK allein genügt) und Eclipse erkennt nun unter "Installed JREs" zwei verschiedene JREs obwohl beide im selben Ordner liegen: hat es wohl einiges verspult (typisch!). Ich habe dann die oben gennanten JARs in die Verzeichnisse

{% highlight batch %}
C:\Programme\Java\jre6\lib\ext\
{% endhighlight %}

und

{% highlight batch %}
C:\Programme\Java\jdk1.6.0_18\jre\lib\ext
{% endhighlight %}

kopiert, und noch zusätzlich die JARs aus ersterem Verzeichnis als externe JARs in den Buildpath eingefügt. Auch die JRE System Library habe ich auf die andere geändert, die bei mir "JRE6u18" heißt. Und siehe da:  
Es funktioniert plötzlich. Aber warum weiß ich jetzt noch immer nicht genau...