---
layout: post
title: 'Rails: Definierte Url-Helper-Methoden zur Laufzeit erfragen'
date: '2013-10-27T17:03:00.000+01:00'
tags:
- Programmieren
- Ruby
- Rails
modified_time: '2013-10-27T17:03:36.058+01:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-2757049297552393035
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/10/rails-definierte-url-helper-methoden.html
---

Die den durch manuelle Einträge in die `routes.rb` erzeugten "named routes", die man in Rails 4 jetzt auch im Browser ansehen kann (unter http://localhost:3000/rails/info/routes), also z.B. der Form *_path und *_url werden ja oft als Parameter für `link_to`-Helpermethoden verwendet, z.B. `<%= link_to "About", about_path %>`. Nach einer Weile vergisst man fast, dass es sich dabei um Methoden handelt und nicht um Strings. Da Ruby bekanntlich keine Klammern an Methodenaufrufen verlangt wenn die Methode keine Parameter bekommt, lassen sich die Pfade wie Strings verwenden. Aber was ist, wenn zur Laufzeit nur der String bekannt ist? Um es mit den Worten von Michael Hartl's [Rails Tutorial](http://ruby.railstutorial.org/) zu fragen: Angenommen ich habe einen RSpec-Test, der nacheinander alle named routes aufrufen und etwas auf dieser Seite testen will:

{% highlight ruby linenos %}
describe 'Static Pages' do
  describe "titles" do
    it "should have the correct title" do
      %w[home help about].each do |title|
        visit "#{title}_path"
        expect(page).to have_title("Sample App - #{title.capitalize!}")
      end
    end
  end
end
{% endhighlight %}

Das würde nicht funktionieren. Die `visit`-Methode verlangt zwar einen String, aber der String "`home_path`" hat hier überhaupt keine Bedeutung! Einfach zu schreiben `visit home_path` funktioniert dennoch, da die Helper-Methoden je nach Endung (_path oder _url) einen String zurückgeben. Wie kommt man nun an die richtige Methode bzw. URL? Man könnte zwar im Array die richtigen Pfade angeben, sobald die Sache aber komplexer oder die Pfade unregelmäßiger werden, wird das Ganze schwer zu warten.  
Hier hilft der [Blogpost](http://ridingtheclutch.com/post/1299774075/using-rails-3-route-helpers-outside-of-a) von Rob Cameron weiter. Im Modul `Rails.application.routes.url_helpers` sind alle Pfade als Methoden definiert und unter Ruby 2 tauchen diese als Symbole mit der `methods`-Methode auf. Dem Modul selbst kann man so mit `to_sym` und (dem Smalltalk-Erbe sei Dank) `send` einen String schicken und erhält dann den richtigen Pfad zurück. Die richtige Zeile wäre also `visit Rails.application.routes.url_helpers.send(("#{title}_path").to_sym)` und die Webseiten werden brav besucht.