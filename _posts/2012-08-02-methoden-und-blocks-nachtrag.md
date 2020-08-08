---
layout: post
title: Methoden und Blocks - Nachtrag
date: '2012-08-02T22:17:00.001+02:00'
tags:
- Ruby
- Informatik
modified_time: '2012-08-02T22:17:54.257+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-7632106921458544719
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/08/methoden-und-blocks-nachtrag.html
---

Ich glaube ich habe für mich den perfekten Weg gefunden, mir die Funktionsweise von Methoden mit Blocks und Procs usw. zu erschliessen. Die größte Hilfe war [dieser](http://allaboutruby.wordpress.com/2006/01/20/ruby-blocks-101/) Blogeintrag, in dem der magische Satz stand: Denke über die Methode (m3 weiter unten) wie über eine Methode die drei Argumente bekommt: x, y und beliebigen Ruby-Code. Dann wird klar, wie all die Methoden wie map, cycle usw. grundsätzlich aufgebaut sind. Wenn die Grundlagen bis m5 unten gepackt wurden, wird auch klarer, was Procs sollen. Vor allem, wenn man JavaScript kennt. Andere gute Quellen waren [diese](http://www.skorks.com/2009/09/using-ruby-blocks-and-rolling-your-own-iterators/) [alten](http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/) Blogposts. Aber genug der Vorrede: Hier ist der Weg zur Erleuchtung:  

1). Einfachster Fall: Der Block wird ignoriert.

{% highlight ruby %}
def m1
  puts "hello"
end
m1 { puts "world" } # "hello"
{% endhighlight %}

2). yield tut nichts und fügt einfach den Code ein.

{% highlight ruby %}
def m2
  yield
end
m2 { puts "hello world" } # "hello world"
{% endhighlight %}

3). yield gibt Parameter raus bzw. Blockparameter rein.

{% highlight ruby %}
def m3
  i = 1
  j = 2
  yield i, j
end
m3 { |x, y| puts x + y } # 3
{% endhighlight %}

Merksatz: m3 ist wie eine Funktion die zwei Argumente und beliebigen Ruby-Code aufnimmt. yield steht an der Stelle, an der der Code ausgeführt wird. Mit block_given? kann man die Ausführung von yield steuern.

{% highlight ruby %}
def m3choose
  i = 2
  j = 2
  if block_given?
    yield i, j
  else
    puts "zonk"
  end
end
m3choose # zonk
m3choose { |x, y| puts x*y } # 4
{% endhighlight %}

4). Methode hat Parameter und yield.

{% highlight ruby %}
def m4(n)
  yield n
end
m4(2) { |x| puts x }
{% endhighlight %}

Komplizierter:

{% highlight ruby %}
def m5(n)
  n.times do
    yield
  end
end
a = %w[a b c]
m5(2) do |x| # argument des yield von m4
  a.each do |e| # argument des yield von each
    x = e; puts x
  end
end # siehe Array#cycle
{% endhighlight %}

5). yield-Parameter wie normale Parameter.

{% highlight ruby %}
class Array
  def reverse_iterate
    current_index = self.size - 1
    while current_index >= 0
      yield self[current_index], 'Value', current_index, 'Index'
      current_index -= 1
    end
  end
end
# Der Sponge-Operator tut wie sonst auch:
[3, 6, 7, 5, 2].reverse_iterate do |value, *others|
  puts "#{others[0]} = #{value}, #{others[2]} = #{others[1]}"
end
{% endhighlight %}

6). Andere Möglichkeit, einen Block aufzurufen: Ampersand-Operator, ählich wie oben.

{% highlight ruby %}
class Array
  def iterate!(&code)
    self.each_with_index do |n, i|
      self[i] = code.call(n)
    end
  end
end
[1, 2, 3, 4].iterate! { |n| puts n ** 2 }
{% endhighlight %}

7). Der Parameter, der ein Block werden wird kann dann einfach an eine andere Methode im Body geschickt werden, und die Werte bleiben erhalten = closure

{% highlight ruby %}
def methode_mit_block(&block)
  andere_methode block
end
# Var wird ein Block
def andere_methode(var)
  x = 42
  var.call x
end
methode_mit_block { |i| puts "Ich bin Block nummer #{i}" }
{% endhighlight %}

8). Proc-Objekte benutzen und als First-Class-Members zuweisen.

{% highlight ruby %}
add = Proc.new { |a, b| puts a + b }
class Array
  def hand_2_over(code)
    self.each do |n|
      code.call(self.slice!(0, 2))
    end
  end
end
[1, 2, 3, 4].hand_2_over add # 3, 7
{% endhighlight %}

Das letzte Beispiel zeigt auch sehr gut, was mir an Ruby extrem nicht gefällt. Ohne IDE-Unterstützung und Dokumentation ist es fast ungmöglich zu sagen, wo "add" definiert ist, von wem es stammt (eingebaut oder irgendwo selbst definiert) oder geschweige denn was es tun wird.