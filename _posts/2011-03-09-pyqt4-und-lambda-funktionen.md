---
layout: post
title: PyQt4 und lambda-Funktionen
date: '2011-03-09T20:27:00.000+01:00'
tags:
- Programmieren
- Python
modified_time: '2011-03-09T20:27:00.421+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-3027757762609262639
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2011/03/pyqt4-und-lambda-funktionen.html
---

Beim Programmieren von GUIs mit PyQt4 stößt man manchmal auf das Problem, daß man beim connecten von Signalen mit Slots bei einem bestimmten Signal eine Funktion aufrufen möchte, welche Parameter übergeben bekommt. Bei mir sah das z.B. so aus:  

{% highlight python %}
self.connect(eintrag, QtCore.SIGNAL('triggered()'), self.oeffne_datei_aus_menu(pfad))
{% endhighlight %}

Dumm ist nur, daß man es so nicht hinschreiben kann. Der letzte Parameter in der `connect()`-Methode kann zwar jedes callbare Objekt sein, durch die C++-Herkunft des Qt-Frameworks muß der Aufruf am Ende aber ohne Klammern geschehen, es steht immer nur der Methodenname ohne Klammern da, z.B.:  

{% highlight python %}
self.connect(self.nextButton, QtCore.SIGNAL('clicked()'), self.zeigeAntwort)
{% endhighlight %}

Mit lambda-Funktionen ist es aber trotzdem möglich. Die Struktur eines lambda-Ausdruckes sieht bei Python so aus:  

{% highlight python %}
g = lambda x: x**
{% endhighlight %}

Diese Deklaration entspricht der Funktion  

{% highlight python %}
def g(x): return x**2
{% endhighlight %}

Der Witz ist nun, daß man dem "x", welches der lambda-Funktion übergeben wird wie bei einer normalen Funktion gleich einen Parameter mitgeben kann. In obigem Beispiel, in welchem noch der ungültige Methodenaufruf in der `connect()`-Methode steht habe ich stattdessen eingefügt:  

{% highlight python %}
self.connect(eintrag, QtCore.SIGNAL('triggered()'), lambda der_pfad=pfad: self.oeffne_Datei_aus_menu(pfad))
{% endhighlight %}

(Den aktuellen Dateipfad hole ich mir im realen Programm etwas weiter oben, das Ganze war dazu gedacht ein Dateimenü dynamisch mit Einträgen zu versorgen). Daß man dem "x" bei lamda-Funktionen mit dem Zuweisungsoperator zu Leibe rücken kann habe ich leider nirgendwo in der Doku gefunden, was mich mal wieder einiges an Sucherei gekostet hat.