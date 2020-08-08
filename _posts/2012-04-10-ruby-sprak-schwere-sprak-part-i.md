---
layout: post
title: Ruby Sprak - Schwere Sprak Part I
date: '2012-04-10T11:53:00.001+02:00'
tags:
- Ruby
- Informatik
modified_time: '2012-04-10T11:55:51.880+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-8392102362786327023
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/04/ruby-sprak-schwere-sprak-part-i.html
---

Ruby macht es einem nicht gerade leicht. Selbst Personen wie [Peter Cooper](http://peterc.org/), die seit jahrzehnten professionell Ruby einsetzen geben in z.B. "Ruby Trick Shots" zu, immer noch über Dinge wie mögliche Syntax zu stolpern von denen sie nicht mal eine Ahnung hatten dass es sie gibt. Selbst die einfachsten Dinge wie z.B. Klassenvariablen werden durch den Umstand, dass Klassen an Sich auch Objekte sind zu einem gewaltigen Verwirrspiel aufgeblasen und erzeugen Henne-Ei-Probleme nebst Knoten im Gehirn. Beim Durcharbeiten von "The Well-Grounded Rubyist" (was ich übrigens vor allen anderen Ruby-Büchern hätte beackern sollen) bin ich auf Seite 157 auf das erste große Verständnisproblem mit Klassenvariablen gestoßen, und das obwohl ich seit gut sieben Monaten fast ausschließlich mit Ruby programmiere. Vielleicht ist ja das damit gemeint, wenn es heißt dass sich Ruby "einfach anwenden und produktiv einsetzen" lässt. Einfach schlecht und so produktiv, dass man von der häßlichkeit des eigenen Codes beschämt drei Monate später am liebsten die Festplatte formatieren möchte. Aber ich schweife ab...  

Klassen sind auch Objekte. Ich kann eine Klasse "fragen", welche Variablen es in einer Instanz von ihr geben wird - obwohl es noch kein Objekt zu der Klasse gibt. Das heißt, es gibt ja ein Objekt. Nur ist das eben die Klasse an sich. Hier spannt einen "The Well-Grounded Rubyist" noch etwas auf die Folter und verspricht, dem Problem "was war zuerst da" später auf den Grund zu gehen.  

Ich kann in irb on-the-fly eine Klasse definieren, im einfachsten Fall  

{% highlight ruby %}
class Leer
end
{% endhighlight %}

`Leer` hat jetzt schon einige Methoden:  

{% highlight ruby %}
Leer.instance_methods.size
{% endhighlight %}

ergibt ganze 56, welche direkt von `Object` geerbt werden (das stimmt eigentlich nicht  ganz, weil `Object` das `Kernel`-Modul einmixt und so den großteil seiner Methoden bekommt). Ich kann `Leer` nach seiner Klasse fragen:  

{% highlight ruby %}
Leer.class
{% endhighlight %}

ergibt `Class`. Das Objekt "Leer" gehört also zur Klasse "Class". Dass "Leer" wirklich ein Objekt ist zeigt die Nachfrage nach `Leer.object_id`. Ich kann "Leer" auch nach seinen Instanzmethoden und -variablen fragen. Variablen hat es keine, und die Methoden sind die 56 von oben. Erzeuge ich eine Instanz der Klasse "Leer", kann ich sie nicht nach Instanzmethoden fragen (klar, die sind ja in der Klasse selbst definiert) sondern nach den Methoden direkt, mit `.methods`. Wie kommen jetzt die Variablen in Spiel?  

In Ruby gibt es zwei Arten von Variablen in einer Klasse. Die einen sind die Klassenvariablen, die anderen sind die Instanzvariablen. Es ist aber auch wichtig, wo diese definiert sind. Anders als in Java, wo sie einfach oben über dem Konstruktor deklariert und später im Konstruktor initialisiert werden, "leben" die Variablen in verschiedenen "Scopes" welche von self zum Zeitpunkt der Deklaration abhängen.  

{% highlight ruby %}
class NichtLeer
  @v = 'v'
end
{% endhighlight %}

Hier ist eine Instanzvariable. Diese gehört, da sie außerhalb von jeder Methode definiert wurde zum "Klassenobjekt" `NichtLeer`. (Getter/Setter gibt es so ja nicht, und initialize ist nur eine weitere Methode, die beim Aufruf von `.new` automatisch ausgeführt wird.) Mittels `NichtLeer.instance_variable_get :@v` wird auch tatsächlich "v" ausgegeben. Erzeuge ich nun eine Instanz `l` von "NichtLeer" ist @v erstmal so nicht zu erreichen. `l.instance_variables` gibt ein leeres Array zurück. Dabei ist das doch eine Instanzvariable! Ja schon, aber nur von Objekten der Klasse "Class", aber nicht von Objekten der Klasse "NichtLeer"! Bei der Deklaration von Klassenvariablen (die mit dem '@@' vorne dran) verläuft das ganze eigentlich analog.  

{% highlight ruby %}
class NichtLeer2
  @@v = 'v'
end
{% endhighlight %}

`NichtLeer2.instance_variables` ist nun ohne Inhalt, dafür gibt es einen Eintrag in `NichtLeer2.class_variables`. An den Wert von @@v kommt man mittels `NichtLeer2.get_class_variable :@@v`. Instanzen von "NichtLeer2" haben immer noch keine Instanzvariablen im Array. Diese Klassenvariablen sind an der ganzen Geschichte noch das Java-mäßigste, obwohl man in Verbindung mit Vererbung [vorsichtig sein sollte](http://blog.codegram.com/2011/4/understanding-class-instance-variables-in-ruby). Machen wir das ganze etwas spannender: Jetzt fügen wir eine Methode ein.  

{% highlight ruby %}
class Voll
  @v = 'v'
  def zeige_var
    @v
  end
end
{% endhighlight %}

Wird jetzt `v = V.new` und `v.zeige_var` ausgeführt, kommt ein `nil` angeflogen. "Aber ich habe doch meine Variable deklariert!" wird jeder Java-Programmierer jetzt ausrufen. Das stimmt zwar, aber die dynamische Natur von Ruby bezieht sich nicht nur auf die Typisierung. Genau wie oben gehört die erste Variable @v zum Klassenobjekt und nicht zu einem Instanzobjekt. Die Methode zeige_var hat keine Kenntnis über die Instanzvariablen - Die hat nicht mal das instantiiere Objekt der Klasse "Voll" selbst! Es wird zwar ein Objekt mit `.new` erzeugt, da aber nichts initialisiert wurde, existiert für das Objekt der Klasse "Voll" die Variable noch gar nicht. Der erste gravierende Unterschied zu Java: Variablen existieren erst ab dem Zeitpunkt, zu dem der Interpreter vorbeischaut und sie zuweist.  

Ein zweites Beispiel macht dies deutlicher:  

{% highlight ruby %}
class NochVoller
  @v = 'v'
  def mache_var
    @v = 'foo'
  end

  def zeige_var
    @v
  end
end
v = NochVoller.new
v.mache_var
v.zeige_var
{% endhighlight %}

Wird "zeige_var" ausgeführt, bevor "mache_var" ausgeführt wurde gibt es wieder ein `nil`. Wo schaut der Interpreter aber eigentlich genau nach, wenn er @var zunächst nicht findet?  

Die Antwort sind die oben erwähnten Scopes von self. self ändert sich jedes mal, wenn eine Klasse/Methode/Modul definiert wird, außerdem gibt es noch das top-level (das ist das Standardlevel in z.B. einer irb-Session oder in einem Klassendefinitionslosen Script). In dem Codeschnipsel von oben ist self zunächst `NochVoller` - und zwar das berühmt-berüchtigte Klassenobjekt. Das erste @v wird also auf Class-Objekt-Ebene definiert und alle Aussagen von oben treffen zu. Danach geht es mit einer Methodendefinition weiter. Zu Methodendefinitionen ist wichtig zu wissen dass die Methoden zwar definiert werden, ausgeführt aber erst wenn es ein Objekt gibt welches die Methoden überhaupt ausführen kann. Die Methoden sind quasi Verhaltensweisen eines noch unbekanntes Objekts in der Zukunft. Und genau dieses bekommt in "mache_var" ein @v verpasst. Und zwar wenn auf einem schon instantiierten Objekt die Methode aufgerufen wird, in der ein @v einem Speicherstück z.B. `#Voller:0x0000000130c518` zugewiesen wird. Das ist der zweite gravierende Unterschied zu Java: self kann verschiedene Formen annehmen, und leider ist es nicht immer offensichtlich was gerade abläuft. Ich habe nur an der Oberfläche gekratzt, aber komplexere Beispiele sollten mit der Methode "Schau immer wo @bar deklariert wurde und verfolge die Reihenfolgen der Instantiierungen und Zuweisungen im Code" leichter zu verstehen sein.