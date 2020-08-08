---
layout: post
title: 'Kurztipp: Android Apps auf der Kommandozeile bauen und im Debugmodus starten'
date: '2013-10-23T00:16:00.001+02:00'
tags:
- Android
- Ubuntu
modified_time: '2013-10-23T00:16:51.164+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-7682479545720688818
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/10/kurztipp-android-apps-auf-der.html
---

Der Emulator wird mit `emulator -avd <avd-name>` gestartet. Welche avds angelegt sind kann unter Ubuntu mit einem Blick in das Verzeichnis `/home/$USER/.android/avd/` gesehen werden. Wenn der Emulator läuft, kann mit `adb devices` gesehen werden, welche devices aufgelistet sind. Will man eine Logcat-Ausgabe wie unter Eclipse, genügt der Befehl `adb logcat`. Wenn das Eclipse-Projekt zuvor mit dem Befehl `android update project -p .` in ein Ant-kompatibles Projekt umgewandelt wurde, kann im Projektverzeichnis mit `ant clean debug` das Projekt gebaut werden. `clean` ist hier wichtig, da es sonst zu seltsamen Fehlermeldungen kommt. Das erzeugte .apk wird dann mit `adb install /pfad/zur/datei` auf dem device installiert. Die App selbst wird mit dem Befehl


{% highlight bash %}
adb shell am start -D -a android.intent.action.MAIN -n com.package.name/com.package.name.MainActivityName
{% endhighlight %}

im Debugmodus gestartet.