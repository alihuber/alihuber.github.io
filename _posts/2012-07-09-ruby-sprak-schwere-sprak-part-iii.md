---
layout: post
title: Ruby Sprak - Schwere Sprak Part III
date: '2012-07-09T20:47:00.001+02:00'
tags:
- Ruby
- Informatik
modified_time: '2012-07-09T20:47:59.439+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-2286239278597232980
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/07/ruby-sprak-schwere-sprak-part-iii.html
---

Die meiste Verwirrung um Blocks und Procs rührt daher, wie beide zusammenarbeiten. Die Aussage, ALLES in Ruby sei ein Objekt ist einfach nicht wahr: Blocks, Argumentlisten und Funktionen sind keine Objekte, daher ist die gern zitierte Aussage, Ruby sei "objektorientierter" als Python ziemlicher Quatsch, [so hatte Matz das gar nicht gemeint](http://linuxdevcenter.com/pub/a/linux/2001/11/29/ruby.html). Blocks sind aber dazu fähig, ein Proc zu erzeugen. Und die sind wiederum diesmal wirklich Objekte. Der Unterschied zwischen Blocks und Procs ist folgender:

{% highlight ruby %}
[1, 2, 3].each { |x| puts x * 10 }
{% endhighlight %}

Der Codeblock oben ist wie gesagt, kein Objekt. Er gehört zur Syntax des Methodenaufrufs. Eine Analogie wäre eine Argumentliste:

{% highlight ruby %}
puts say_hello('hi')
{% endhighlight %}

Das Argument ist ein Objekt, die Argumentliste selbst aber nicht. Genauso wie der Block oben: Der Empfänger von each ist ein Objekt, der Block selbst ist aber kein Objekt. Eine Instanz eines Proc ist aber ein Objekt und kann in Methodenargumenten auftauchen. Viel Konfusion entsteht, weil die Syntax der Erzeugung von Procs so ähnlich wie die eines Blocks hinter einem Methodenaufruf ist:

{% highlight ruby %}
pr = Proc.new { puts 'Im Block eines Proc!' }
# Procs werden aufgerufen, indem sie gecallt werden:
pr.call
{% endhighlight %}

Das ist so wie die Zuweisung einer Methode an eine Variable in JavaScript, und alles weitere sind lediglich Erweiterungen dieser Funktionalität: Methodenargumente, Zuweisung an Variablen, die Verwendung in Arrays... Das Funktioniert alles, weil Procs Objekte sind und Blocks nicht.  

Der Witz ist nun, dass Blocks in Procs umgewandelt werden können und umgekehrt.

{% highlight ruby %}
def capture_block(&block)
  puts "Ich hab den Block erwischt, er ist jetzt ein Proc!"
  block.call
end
capture_block { "Das wird ein Proc!" }
#=> "Ich hab den Block erwischt, er ist jetzt ein Proc!"
#=> "Das wird ein Proc!"
{% endhighlight %}

Anders herum:

{% highlight ruby %}
p = Proc.new { puts "Das wird ein Block" }
capture_block(&p)
{% endhighlight %}

Die Aussage in meinem letzten Blogeintrag, dass es auf yield ankommt ob eine Methode einen Block bekommen kann, ist so dann nicht ganz richtig weil das Argument oben ja auch ein Block wird. Dies funktioniert alles, weil das "&" ein Wrapper für `to_proc` ist. Die `to_proc`-Methode erlaubt einige lustige Dinge: Wenn in einer Klasse `to_proc` definiert wird, wird bei einem Aufruf "&Klassenname" der Code des Procs ausgeführt. Hier mal ein sinnloses Beispiel:

{% highlight ruby %}
class A
  def self.to_proc
    Proc.new { puts "AAH!" }
  end
end
[nil, nil].map(&A)
#=> AAH!
#=> AAH!
{% endhighlight %}

Die (schön?) kurze Schreibweise, in der man die normale Blocksyntax durch die mit Symbol und Ampersand ersetzt funktioniert, weil Symbols auch eine `to_proc`-Methode haben:

{% highlight ruby %}
%w{otto odol}.each(&:reverse!) #=> ["otto", "lodo"]
{% endhighlight %}

`:reverse` wird als Symbol an jedes Element gesendet, wo es als Message interpretiert wird. Das obige ist gleichbedeutend mit:

{% highlight ruby %}
%w{otto odol}.each { |i| i.reverse! }
{% endhighlight %}