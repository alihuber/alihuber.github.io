---
layout: post
title: 'Kurztipp: Rails 4 mit Ruby 2.0.0 auf Windows 7 64-bit'
date: '2013-09-07T12:16:00.000+02:00'
tags:
- Ruby
- Rails
- Windows
modified_time: '2013-09-07T12:16:59.500+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-5128465115135040831
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/09/kurztipp-rails-4-mit-ruby-200-auf.html
---

Da ich in letzter Zeit fast nur unter Windows entwickle, dachte ich mir ich erstelle eine aktuelle Railsumgebung unter Windows 7. Wie schwer kann es schon sein? Leider stellte sich heraus, dass es dabei einige Fallstricke gibt. Die grundlegenden Schritte sind erst mal einfach: Den [RubyInstaller](http://rubyinstaller.org/downloads/) herunterladen und installieren. Dann das dazugehörige Development-Kit herunterladen und der [Anleitung](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit) folgen, also im entpackten Verzeichnis `ruby dk.rb init` und `ruby dk.rb install` ausführen. Danach muss vermutlich erst SQlite3 installiert werden. Um SQlite3 mit Windows zu verwenden, muss man die SQLite3-dlls von [http://www.sqlite.org/download.html](http://www.sqlite.org/download.html) herunterladen, die .def und .dll-Dateien nach C:\Windows\system32 kopieren und eventuell neu starten. Wenn man möchte, kann man sich vor der Installation von Rails noch die Optionen install: --no-document und update: --no-document in die gemrc schreiben, die unter Windows 7 in C:\ProgramData einfach angelegt wird. Nach einem `gem install rails --platform=ruby` sollte `rails s` im Verzeichnis einer neuen App funktionieren. Falls die Fehlermeldung "Add 'gem sqlite3' to your Gemfile" erscheint (obwohl das Gem natürlich von Anfang an eingetragen ist) gab es einen Fehler mit der Version des Development-Kit und der installierten Version von Ruby. In diesem Fall bringt auch `bundle install` nichts, SQlite3 wird dann in dieser Auflistung nicht einmal aufgeführt. Die Lösung hierfür ist es, in der Datei Gemfile.lock den Eintrag sqlite3 (1.3.8 x64-mingw32) in sqlite3 (1.3.8) zu ändern. Nach erneuter Ausführung von `bundle install` sollte sqlite3 aufgelistet werden und `rails s` die ersehnte "Welcome aboard"-Seite anzeigen.

Allerdings ist die Verwendung von Ruby 2.0.0 unter 64-bit-Windows generell eher schlecht, weil Nokogiri [derzeit](https://github.com/sparklemotion/nokogiri/issues/864) [wohl](http://stackoverflow.com/questions/17280884/cant-install-nokogiri-for-ruby-in-windows) [nicht](http://stackoverflow.com/questions/17014854/libxml2-missing-for-nokogiri-gem-on-windows-8-x64-with-ruby-1-9-3) funktioniert.