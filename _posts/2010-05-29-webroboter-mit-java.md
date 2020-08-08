---
layout: post
title: Webroboter mit Java
date: '2010-05-29T21:54:00.003+02:00'
tags:
- Java
modified_time: '2010-05-29T22:25:31.344+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-5035709486506827950
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2010/05/webroboter-mit-java.html
---

In der Zeitschrift C't Nr. 4 2010 wurde auf persönliche Webroboter eingegangen. Im Artikel wurden dann Möglichkeiten genannt, wie man mit JavaScript Kontoauszüge herunterlädt oder mit Perl oder Ruby(-Mechanize und Nokogiri) seine Onlinebankinggeschäfte scripten kann. Mal abgesehen davon, daß ich Nie und Nimmer selbstgeschriebene Scripte auf mein Bankkonto loslassen würde, hat mich am Artikel befremdet daß auf weitere offensichtliche, weniger exotische Möglichkeiten nicht eingegangen wurde. Ich behaupte einfach mal, daß sich das Scripten von sehr viel mehr stumpfsinnigeren Abläufen viel mehr lohnt, und viel einfacher ist wenn man mit einer weiter verbreiteten (Web-)Programmiersprache *HUST*Java*HUST* arbeitet. Ich werde mal auf so ein kleines Beispiel eingehen, unter der Benutzung von [HtmlUnit](http://htmlunit.sourceforge.net) und [FireBug](http://getfirebug.com).  
Jeder kennt das Problem: Man hat ein paar Foren oder sonstige Websites, in die man sich meistens nur für ein paar Sekunden einloggt, nur um zu sehen ob es eine Änderung gab, z.B. ob ein neuer Beitrag gepostet wurde, ob man eine Message erhalten hat usw. Mein Ziel ist ein kleines Programm für die Kommandozeile, welches mir einfach nur mitteilt: Ja, es gibt (in meinem Fall) neue Forenbeiträge. Nach dem Herunterladen des HTMLUnit-Zipfiles wird der Ordner entpackt und (hier unter NetBeans) die .JAR-Files in die lokalen Bibliotheken eingefügt (Rechtsklick auf den Projektnamen -> Eigenschaften -> Bibliotheken -> Register "Übersetzen" -> JAR/Verzeichnis hinzufügen). Jetzt gehts los: Wir erzeugen einen WebClient:

{% highlight java %}
WebClient wb = new WebClient();
{% endhighlight %}

und eine neue HtmlPage:

{% highlight java %}
HtmlPage page = wb.getPage("http://url.zum.forum");
{% endhighlight %}

Über diese Page können wir uns nun die Elemente selektieren, welche wir für das Einloggen brauchen. Die Namen der Elemente bekommt man über FireBug (Rechtsklick -> Element untersuchen). In meinem Fall heißen sie: "username", "password" (die Eingabefelder) und "login" (der Submit-Button).

{% highlight java %}
HtmlTextInput login = (HtmlTextInput) page.getElementByName("login");
HtmlPasswordInput pass = (HtmlPasswordInput) page.getElementByName("password");
HtmlSubmitInput submit = (HtmlSumitInput) page.getElementByName("login");
{% endhighlight %}

Jetzt können wir die Daten eingeben:

{% highlight java %}
login.setValueAttribute("mein_username");
pass.setValueAttribute("mein_passwort");
{% endhighlight %}

Wenn wir auf den "submit"-Button drücken, ist das Ergebnis wieder eine HtmlPage.

{% highlight java %}
HtmlPage loggedInPage = (HtmlPage) submit.click();
{% endhighlight %}

Jetzt, da ich eingeloggt bin, kann ich mich auf die Suche nach Elementen machen, die neue Beiträge anzeigen. Ich will aber keinen HTMLParser oder XPath verwenden, und die Funktion sich neue Beiträge anzeigen zu lassen gibt es bereits. (By the way: es handelt sich um eines dieser unsäglichen phpBB-Foren). Also tue ich das Einfachste: Ich vergleiche Strings. Das ist zwar ein wenig dilettantisch, für meine Zwecke aber ausreichend. Das Menü des Forums hat eine Schaltfläche "Neue Beiträge". Das Forum ist in Tabellen eingeteilt, und die meisten Menüpunkte sind einfach Links auf die Suchfunktion. Also lasse ich mir die neue HtmlPage geben, welche man durch drücken des Links "Neue Beiträge" bekommt, und erzeuge mir einen String mit der Ergebnisseite:

{% highlight java %}
HtmlAnchor a = loggedInPage.getAnchorByText("Neue Beiträge");
HtmlPage newPosts = (HtmlPage) a.click();
{% endhighlight %}

Der HtmlAnchor ist hier ein Wrapper für das HTML-Element "a". Jetzt habe ich die Website, welche mir, falls es keine neuen Posts gibt sowas wie "Es wurden keine passenden Ergebnisse gefunden." ausgibt (weil es ja eine interne Suche war). Die `asText()`-Methode eines HtmlPage-Objektes gibt Webseiten auch schön formatiert aus, so wie sie in minimalistischen Textbrowsern wie "links" aussehen würden. Da ich aber keine Vorschau auf neue Beiträge will, sondern nur wissen will ob es neue gibt, durchsuche ich einfach den Ergebnisstring:

{% highlight java linenos %}
String theText = newPosts.asText();
if(theText.contains("Es wurden keine passenden Ergebnisse gefunden"))
  System.out.println("Nix neues");
else
  System.out.println("Es gibt neue Beiträge!");
{% endhighlight %}

Und schon habe ich einen netten kleinen Webroboter, ganz ohne Low-Level-Sachen wie HTTP-GET Anfragen oder zusätzliche Parser. Die Methoden zum Ausloggen funktionieren genauso, obwohl sie in meinem Fall unnötig wären, da man automatisch ausgeloggt wird.