---
layout: post
title: Ruby Sprak - Schwere Sprak Part II
date: '2012-07-07T13:06:00.000+02:00'
tags:
- Ruby
- Informatik
modified_time: '2012-07-07T13:10:09.771+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-2472782556048166132
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/07/ruby-sprak-schwere-sprak-part-ii.html
---

 "The well-grounded Rubyist" hat auch eine sehr gute Einführung in Blocks und Procs. Ich bin zwar selbst noch gefühlt meilenweit davon entfernt, Situationen zu erkennen in denen selbst definierte Methoden die Blocks aufnehmen nützlich sein könnten; vielleicht bin ich zu sehr in der Java-Denke drinnen und werde es wohl nie erkennen.  
Ob eine Methode einen Block bekommen kann, hängt davon ab ob sie "yield" enthält.

{% highlight ruby %}
def y_meth
  yield
end
y_meth { puts 'gabba' }
#=> gabba
{% endhighlight %}

"yield" kann auch Parameter haben, diese werden in den aufnehmenden Block zwischen Pipe-Characters geschrieben. Alle Regeln für Methodenargumente (Vorbelegung, Sponge-Stern usw.) gelten auch für Blockparameter.  

{% highlight ruby %}
def add_all
  yield 1, 2, 3
end
add_all do |a, b, c|
  a + b + c
end
#=> 6
{% endhighlight %}

Der Interpreter springt regelrecht hin und her: Zuerst wird a an add_all übergeben, da dort nur yield steht und a das erste Argument ist, bekommt add_all eine "1" zurück. Was damit gemacht werden soll steht im Block von add_all. Das ist sehr kontraintuitiv, da man es von vielen anderen Programmiersprachen gewohnt ist nur einen Ausführungspfad innerhalb einer Methode zu haben ("man steckt was rein und es kommt was raus"). add_all mit den Parametern für yield kann ja sonstwo definiert sein oder selbst Methodenaufrufe beinhalten, die selbst wieder einen Block benutzen. Oder einen Block in dem yield vorkommt, welcher das geyieldete Element wieder an die ursprüngliche Methode zurückgibt.

{% highlight ruby %}
class Array
  def give_back
    acc = []
    # Das erste e kommt von each bzw. give_back,
    # das zweite wird an den Block von give_back gegeben.
    each { |e| acc << yield(e) }
    acc
  end
end
[1, 2, 3].give_back { |e| e**2 }
#=> [1, 4, 9]
{% endhighlight %}

Dann gehen auch noch mehrere yield-Statements in einer Methode und vieles mehr. Aus genau solchen Gründen sind Blocks für mich größtenteils weiße Flecken in fremdem Sourcecode, weil die Ausdrucksstärke von Ruby wegen solchen Ratespielchen flöten geht.  
Was Blocks jetzt genau mit Procs zu tun haben darauf werde ich ein andermal eingehen.