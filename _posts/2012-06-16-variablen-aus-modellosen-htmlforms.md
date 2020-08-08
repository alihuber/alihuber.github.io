---
layout: post
title: Variablen aus modellosen HTMLForms an Rails-Controller übermitteln
date: '2012-06-16T20:19:00.000+02:00'
tags:
- Programmieren
- Ruby
- Rails
modified_time: '2012-06-16T20:19:59.485+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-6931817015850368351
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2012/06/variablen-aus-modellosen-htmlforms.html
---

Eine Funktionalität über die man als Rails-Anfänger eventuell stolpern könnte ist die Übergabe von Variablen aus HTML-Elementen an Funktionen eines Controllers, obwohl das Model dazu diese Felder gar nicht hat. Die eigentlich sehr einfache Aufgabe - "Ich hab hier ne Checkbox und nen Button und wenn ich draufklicke soll der Controller den Zustand der Checkbox aus den Params ziehen" - hat mir einige Kopfschmerzen bereitet. Nicht zuletzt weil Rails ja so MVC-zentriert ist und die "normalen" HTML-Forms normalerweise an die Felder eines Models gekoppelt sind. `form_for` oder gar `semantic_form_for` unterstützen auf den ersten Blick eben nur genau die Felder eines Models, die Action dazu ist dann der submit-Button welcher ein POST auf die create-Action ausführen wird. Die meisten Tutorials gehen vielleicht noch am Rand auf andere Submitmöglichkeiten wie `:method => "GET"` ein und das war's. Die wohl einzig richtige Vorgehensweise ist eine komplett durchgemodelte Applikation und alles andere ist nicht RESTful. Manchmal will man aber einfach zusätzliche Funktionen haben... Und so habe ich es hinbekommen:  
Die Ressource wird wie gehabt in die routes.rb eingetragen, z.B. `resources :events` Die beispielhaften Events haben bei mir nur zwei eigene Felder - `starts_at` und `ends_at`. Angenommen ich wollte jetzt noch einige beliebige Filterregeln haben, die per Klick auf einen Button die derzeitige Ansicht eines Events refreshen. In der show-View steht für das setzen des Datums eines Events wie gehabt folgender HAML-Code (unter Verwendung von formtastic):  

{% highlight haml linenos %}
= semantic_form_for @event do |f|
  = f.text_field :starts_at
  = f.text_field :ends_at
  = f.actions :submit
{% endhighlight %}

Dies erzeugt standardmäßig einen PUT-Request und wird den Code in der Update-Methode des Event-Controllers ausführen um die aktuellen Daten des derzeitigen Events zu ändern. Was viele Tutorials/Bücher leider verschweigen ist dass es zu form_for-Elementen/Methoden auch form_tag-Elemente/Methoden gibt, die nicht an ein Model gebunden sein müssen. In der show-View habe ich dann folgenden Code hinzugefügt:  

{% highlight haml linenos %}
= form_tag('/events/1', :method => "get") do
  = check_box_tag 'tos', 'yes', false
  = submit_tag "Submit"
{% endhighlight %}

Dies ist ein beispielhafter Schalter der dem Controller übermitteln soll ob die "Terms Of Service" angeklickt wurden. Die form_tag()-Methode kann also auf einer bestimmten URL (hier "/events/1", es gehen aber beliebige) mit einem bestimmten Request definiert werden und führt dann auch die Methode im Controller aus die auf diese URL und diesen HTTP-Request passt, also in diesem Fall "show": Die Seite wird jetzt einfach neu gerendert. Im Parameter-Hash der aber nun so aussieht: `{"utf8"=> "✓", "tos"=> "yes", "commit"=> "Submit", "id"=> "1"}` sind nun die Daten der Checkbox drin, die mit `params[:tos]` in der show-Methode im Events-Controller abgefragt werden können, ganz ohne JavaScript-Trickserei.