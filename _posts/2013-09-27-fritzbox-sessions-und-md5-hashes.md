---
layout: post
title: FRITZ!Box-Sessions und MD5-Hashes
date: '2013-09-27T22:38:00.000+02:00'
tags:
- Programmieren
- FritzBox
- Ruby
- CSharp
modified_time: '2013-09-27T22:38:54.652+02:00'
blogger_id: tag:blogger.com,1999:blog-695972570119037880.post-6542281146891757123
blogger_orig_url: http://hirnerweichung2null.blogspot.com/2013/09/fritzbox-sessions-und-md5-hashes.html
---

Wie gültige Session-IDs für das Webinterface einer FRITZ!Box erzeugt werden ist eigentlich ziemlich gut in [einem der vielen Docs](http://www.avm.de/de/Extern/files/session_id/AVM_Technical_Note_-_Session_ID.pdf) beschrieben. Sogar mit Codebeispielen in C#, wow! Will man die Codebeispiele jedoch auf eine andere Programmiersprache wie Python oder Ruby portieren gibt es eine Überraschung. Der Code

{% highlight csharp %}
public string GetResponse (string challenge, string kennwort) {
    return GetMD5Hash(challenge + "-" + kennwort);
}
{% endhighlight %}

sieht doch eigentlich ziemlich beherrschbar aus. Dasselbe Ergebnis sollte sich doch bestimmt mit

{% highlight ruby %}
def get_response(challenge, kennwort)
  Digest::MD5.hexdigest(challenge + "-" + kennwort)
end
{% endhighlight %}

generieren lassen? Leider nein. Es werden für dieselben Inputs unterschiedliche Hashes erzeugt. Der Teufel steckt wieder mal im Detail, bzw. in der `GetMD5Hash`-Methode:

{% highlight csharp linenos %}
public string GetMD5Hash (string input) {
    MD5 md5Hasher    = MD5.Create();
    byte[] data      = md5Hasher.ComputeHash(Encoding.Unicode.GetBytes(input));
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i &lt; data.Length; i++) {
        sb.Append(data[ i ].ToString("x2"));
    }
    return sb.ToString();
}
{% endhighlight %}

Hier sieht auf den ersten Blick alles super aus. `x2` bedeutet, dass der String als Hex-Wert angehängt wird, das Ergebnis sollte also auf jeden Fall `hexdigest` entsprechen. Wirft man jedoch den Debugger an, sieht man dass mit `GetBytes()` ein Array erzeugt wird, in dem jedes zweite Element eine `0` ist. Glücklicherweise hat Ruby eine elegante Vorgehensweise, schnell jedes zweite Element eines Arrays zu setzen. Eine neue `get_response`-Methode macht also noch ein wenig mehr:

{% highlight ruby linenos %}
def get_response(challenge, kennwort)
  before  = challenge + "-" + kennwort
  ary     = []
  before.size.times { ary << 0 }
  after   = before.bytes.zip(ary).flatten!
  unicode = after.pack('U*')
  Digest::MD5.hexdigest(unicode)
end
{% endhighlight %}

Das Verwenden von Unicode wird gleich mit der Umwandlung eines Byte-Arrays mit `pack()` erschlagen. Nun sind die erzeugten Hashes sowohl mit Ruby- als auch mit C#-Programm endlich identisch.