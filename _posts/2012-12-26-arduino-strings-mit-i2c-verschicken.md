---
layout: post
title: 'Arduino: Strings mit I2C verschicken'
date: '2012-12-26T18:18:00.001+01:00'
tags:
- Arduino
- Cplusplus
modified_time: '2012-12-26T18:22:43.965+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-1487714898545595129
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/12/arduino-strings-mit-i2c-verschicken.html
---

Dieses Jahr war ich wohl fleißig und habe (rein rechnerisch) mindestens 1x im Monat gebloggt. Und hier kommt der letzte Beitrag 2012:  

Für ein Projekt im Rahmen der Veranstaltung "Embedded Systems" sollte ein String mit Informationen für eine RGB-LED-Matrix (Uhrzeit und Farbinformationen als Hex-Wert) über den I2C-Bus der Arduino-Mikrocontroller verschickt werden. Da auf der Empfängerseite eventgesteuert gearbeitet wird ist das verschicken mehrerer kleiner Nachrichten nicht praktikabel, da es auch etwas dauert bis die gesendeten Informationen weiterverarbeitet sind. Experimente mit allzu langen Strings waren auch nicht erfolgreich, so dass ein String der Länge 12 verschickt wird. Um den "cplusplussigen" Umgang mit einzelnen Bytes zu vermeiden wird der zu verschickende String zuerst in ein Char-Array gewandelt:

{% highlight cpp %}
String outstring = "12150x68FC63";
char outAry[13] = { };
outstring.toCharArray(outAry, 13);
{% endhighlight %}

Das Array und die Größenangabe des Buffers sind eins größer, da noch ein Terminierungszeichen angehängt wird. Dann wird das Array mit Hilfe der Wire-Bibliothek verschickt:

{% highlight cpp %}
Wire.beginTransmission(4);
Wire.write(outAry);
Wire.endTransmission();
{% endhighlight %}

In der Methode

{% highlight cpp %}
receiveEvent(int lengthBytes)
{% endhighlight %}

des Empfängers, die mit

{% highlight cpp %}
Wire.onReceive(receiveEvent);
{% endhighlight %}

deklariert wurde und die ankommenden Bytes verarbeitet wird der String dann wieder ausgepackt indem die einzelnen Bytes gelesen, gecastet und an ein String-Objekt konkateniert werden:

{% highlight cpp %}
String inString = "";
while (Wire.available()) {
  inString += char(Wire.read());
}
{% endhighlight %}

Die `available()`-Methode macht bei langen Strings Probleme, und da im Projekt im Sekundentakt gearbeitet wird wurde wie oben beschrieben deshalb ein kürzerer String gewählt. Soll jetzt z.B. die Farbinformation, die jetzt ja als ausgeschriebener Hexwert vorliegt wieder in die richtige Zahl gewandelt werden, wird der String wieder in ein Array gepackt:

{% highlight cpp %}
String colorStr = inString.substring(4);
char colorAry[9];
colorStr.toCharArray(colorAry, 9);
{% endhighlight %}

Auch hier ist wieder darauf zu achten, dass das Array eins größer ist. Die Zahl kann dann mit der String-to-Long-Methode gecastet werden, die auch die Basis als Parameter bekommen kann:

{% highlight cpp %}
uint32_t color;
color = strtol(colorAry, NULL, 16);
{% endhighlight %}