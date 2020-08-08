---
layout: post
title: Ruby-Konstrukte in Rails
date: '2013-02-12T17:06:00.000+01:00'
tags:
- Ruby
- Rails
modified_time: '2013-02-12T17:06:35.544+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-3313947492408132713
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/02/ruby-konstrukte-in-rails.html
---

Beim lesen von Ruby-Sourcecode stolpert man laufend über Sprachkonstrukte von Ruby die trotz Beschreibung in der Rails API nicht sofort einleuchtend sind. Hier habe ich mal zwei zusammengefasst:

### ActiveSupport::Concern
Es gibt offensichtlich Situationen, in denen Erben von `ActiveRecord::Base` Instanz- und Klassenmethoden gleichzeitig hinzugefügt werden sollen, etwa wenn man eine Bibliothek schreibt, die allen Models bestimmte Eigenschaften geben soll die sie vorher nicht hatten. Dafür gibt es das `ActiveSupport::Concern`-Modul. Angenommen, ich habe mir eine Webanwendung zur Verwaltung all meiner Literatur geschrieben. Und jetzt will ich auch Magazine verwalten und die Funktionalität nachziehen, die Magazine nach Themen sortieren zu können. Mit `ActiveSupport::Concern` kann ich folgendes machen:

 In meinem neuen Modul wird `ActiveSupport::Concern` extended, und die neuen Klassenmethoden kommen in ein eigenes Modul. Es ist inzwischen deprecated, ein eigenes Untermodul für Instanzmethoden zu schreiben. Stattdessen werden Methoden die einfach im Body deklariert sind automatisch Instanzmethoden.

{% highlight ruby linenos %}
module ThemeLibrary
  extend ActiveSupport::Concern
  module ClassMethods
    def find_by_theme(theme_name)
      puts theme_name
    end
  end
  def themes
    puts "Android, Windows8"
  end
end
{% endhighlight %}

Dann wird mein neues Modul in `ActiveRecord::Base` included, so dass mit etwas Ruby-Magie die Instanz- und Klassenmethoden zu meinen Models hinzugefügt werden:

{% highlight ruby linenos %}
class ActiveRecord::Base
  include ThemeLibrary
end

class Magazine < ActiveRecord::Base
end

Magazine.find_by_theme("Android")
m = Magazine.new
m.themes # => "Android, Windows8"
{% endhighlight %}

Durch `ActiveSupport::Concern` sind Dinge möglich wie:

{% highlight ruby linenos %}
module A
  extend ActiveSupport::Concern
  # definiere Klassen- und Instanzmethoden
end

module B
  extend ActiveSupport::Concern
  include A
  included do
    # Klassenmethoden aus Modul A
  end
  # definiere Klassen- und Instanzmethoden
end

class C
  include B
end
{% endhighlight %}

In Klasse `C` ist jetzt auch Modul `A` included.

### Module die sich selbst extenden
Der Unterschied zwischen `include` und `extend` ist der, dass `include` die Modulmethden im Body eines Moduls zu Instanzmethoden macht. `extend` macht die Modulmethoden dagegen zu Klassenmethoden. Außerdem kann `extend` dazu benutzt werden, Singletonmethoden auf einem Objekt zu definieren. Dabei ist zu beachten, dass jedes Objekt extenden kann, auch self:

{% highlight ruby linenos %}
module Greeter
  extend self
  def hello
    "hi"
  end
end
puts Greeter.hello
{% endhighlight %}

Dies wird als ein Ersatz für das Singleton-Pattern benutzt, mit dem zusätzlichen Vorteil, dass `hello` immer noch von einer Klasse eingemixt werden kann.