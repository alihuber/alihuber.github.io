---
layout: post
title: Factor und GVim
date: '2010-04-30T20:08:00.003+02:00'
tags:
- Linux
- Windows
- Factor
- Vim
- Studium
modified_time: '2010-04-30T20:28:58.006+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-2128204392049397971
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/04/factor-und-gvim.html
---

Da ich im Rahmen der Vorlesung "Software Engineering komplexer Systeme" in den Genuß des Erlernens einer konkatenativen, stackbasierten Programmiersprache namens [Factor](http://www.factorcode.org) komme, stellte sich für mich die Frage wie ich mir das Programmieren in Factor erleichtern kann. Ich hatte die Sprache in den letzten Semesterferien schon einmal kurz ausprobiert, ohne jedoch den genauen Kontext zu kennen. Abgesehen davon, das alles, aber auch alles erstmal ungewohnt war fand ich am schlimmsten, dass es so schwer war die Quelldateien zu editieren.  
Da es jetzt richtig mit Factor losgeht und ich schon seit fast einem Jahr auf der ach-so-steilen Lernkurve des Vim-Editors herumschliddere und nicht mal einen Bruchteil der coolen Funktionen richtig intelligent benutze dachte ich mir dass es wohl am besten wäre, den GVim für das editieren der Factor-Sourcefiles zu verwenden, um auch mit dem GVim besser umgehen zu können.  
Leider sind die Anleitungen zum Einrichten eines Editors in den Factor-Docs widersprüchlich, was mich wieder mal fast zwei Stunden des Herumprobierens gekostet hat.  
Hier nun die Anleitung, wie man Factor beibringt, den GVim als Standardeditor gleich beim Start zu kennen und vor allem das Syntax-Highlighting auch unter Windows zu aktivieren:  

**Unixähnliche:**
Die Datei `.factor-rc` im `/home/username/`-Verzeichnis erstellen und folgendes einfügen:

{% highlight factor %}
USING: namespaces editors.gvim ;
"/usr/bin/gvim" \ gvim-path set-global
{% endhighlight %}

Nun sollte, wenn man im Listener

{% highlight factor %}
wordname edit
{% endhighlight %}

eingibt automatisch der GVim erscheinen und die entsprechende Datei in der sich das Word befindet schon geladen haben.  
Syntax-Highlighting bekommt man, indem man die Ordner `ftdetect`, `ftplugin`, `plugin` und `syntax`, welche sich im Factor-Ordner unter `/misc/vim` befinden in das Verzeichnis `/home/username/.vim/` kopiert (das Verzeichnis war bei mir bis auf den Ordner "tags" leer).  

**WindowsXP:**  
Die Datei `factor-rc` im Verzeichnis `C:\Dokumente und Einstellungen\username` erstellen und folgendes einfügen:

{% highlight factor %}
USING: namespaces editors.gvim ;
"C:\\Programme\\Vim\\vim72\\gvim.exe" \ gvim-path set-global
{% endhighlight %}

Die Ordner für die Vim-Dateien sind im Factor-Ordner an derselben Stelle.  
Den Inhalt der jeweiligen Ordner für das Syntax-Highlighting kopierte ich mir in die jeweiligen Ordner unter `C:\Programme\Vim\vimfiles` Nun sollte der GVim wie oben integriert sein.  

**Windows7**
Im Grunde alles wie bei WindowsXP nur dass der Pfad für die factor-rc-Datei im Verzeichnis `C:\Users\username` liegt.