---
layout: post
title: 'Arduino plattformunabhängig ansprechen mit pyserial'
date: '2012-11-24T13:08:00.000+01:00'
tags:
- Arduino
- Python
- Ubuntu
- Windows
modified_time: '2012-11-24T13:11:42.235+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-5838399666812653631
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/11/arduino-plattformunabhangig-ansprechen.html
---

Um Plattformunabhängig dem Arduino (Uno) über die serielle Schnittstelle Nachrichten zu schicken bietet sich ja zunächst der Weg über Java oder Processing an. In der aktuellen Dokumentation des Arduino wird der Weg mittels der RXTX-Bibliothek beschrieben. Da ich dies in absehbarer Zeit aber nicht hinbekam (unter anderem weil unter Windows sämtliche beschriebenen Wege die richtigen .dlls als Java-Library einzubinden nicht funktioniert haben) habe ich mich nach einem anderen Weg umgesehen. Der nächste Kandidat war Python, da es auch hier eine gut dokumentierte Bibliothek für serielle Kommunikation gibt: [pyserial](http://pyserial.sourceforge.net/) Und so klappt die Verbindung mit (Ubuntu)Linux und Windows 7 (beides 64-Bit):  
Für Windows wird zunächst die Python-Version 2.x (3.x geht laut der pyserial-Dokumentation auch, habe ich aber nicht getestet) in der 32-Bit-Version heruntergeladen und installiert. Bei der 64-Bit-Version gibt es Probleme mit den ctags. Falls der python-Befehl nicht im DOS-Prompt gefunden wird muss noch der Python-Installationsordner dem PATH hinzugefügt werden. Ich wähle dann den Weg der Installation von pyserial über setuptools und pip:  
Von [http://pypi.python.org/pypi/setuptools](http://pypi.python.org/pypi/setuptools) die ez_setup.py herunterladen und ausführen. Wenn jetzt der Befehl `easy_install` nicht gefunden wird, muss noch der Unterordner Scripts im Python-Installationsordner zum PATH hinzugefügt werden. Jetzt kann `easy_install pip` ausgeführt werden, und zum Schluss wird noch mittels `pip install pyserial` pyserial selbst installiert. Wenn in der python-REPL dann `import serial` funktioniert, hat die Installation geklappt. Unter Ubuntu muss lediglich das Paket python-serial installiert werden.

Das Script um auf den gewählten seriellen Port einen String zu verschicken hat dann nur wenige Zeilen:

{% highlight python %}
ser = serial.Serial('/dev/ttyUSB0', 9600, timeout=1)
{% endhighlight %}

öffnet z.B. eine serielle Verbindung mit dem Gerät an ttyUSB0 mit 9600 Baud und

{% highlight python %}
ser.write('Hallo')
{% endhighlight %}

schreibt dann einen String. Ein ganzes Programm zum Einstellen aller Parameter mit GUI hat dann maximal 100 Zeilen.  

Zu beachten ist dann noch, dass unter Windows die Ports mit einem Integer angesprochen werden, der auch noch bei 0 anfängt zu zählen, der Wert für COM3 wäre dann 2. Außerdem können die Ports unter Windows nicht geshared werden, d.h. es ist nicht möglich gleichzeitig das Python-Script und den Serial Monitor der Arduino-Entwicklungsumgebung zu benutzen, da es sonst einen Verbindungsfehler gibt.