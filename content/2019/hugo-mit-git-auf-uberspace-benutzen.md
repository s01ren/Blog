---
title: "Hugo mit Git auf dem uberspace nutzen"
date: "2019-09-06"
author: "soeren"
tags: 
  - uberspace
  - hugo
  - blogging
  - git
post: true
draft: false
---

# Vorgeschichte

Vor gar nicht allzu langer Zeit bin ich mit diesem Blog von [Ghost](https://ghost.org/) zu [Jekyll](https://jekyllrb.com/) gewechselt. Ghost hat mir eigentlich sehr gut getaugt, auch wenn ich nicht der größte Fan davon war, dass [bloggen mit Markdown mit den neueren Versionen einen Klick weiter entfernt](https://ghost.org/blog/2-0/) war. Außerdem war es für mich immer eine kleine Herausforderung bei neuen Versionen die Abhängigkeiten auf dem Betriebssystem auf dem neusten Stand zu halten und die Datenbank zu migrieren. 

Bloggen mit einem Static-Site-Generator schien mir da der nächste logische Schritt. Ich habe also kurz Jekyll ausprobiert und bin schließlich bei [Hugo](https://gohugo.io/) gelandet. Hugo gebe ich aktuell den Vorzug, weil es sich leichter auf einem Windows-PC nutzen lässt und wesentlich schneller ist.

Der geneigte Leser wird wissen, das ich bezüglich Webhoster ein großer Fan der Jungs und Mädels von [uberspace](https://uberspace.de) bin. Im folgenden also eine kleine Anleitung, um zu meinem aktuellen Setup zu kommen. 

Dafür werden wir ...

  1. Git und Hugo lokal installieren
  1. Einen neuen Hugo-Blog anlegen
  1. Einen ersten Blog-Artikel schreiben
  1. Den uberspace vorbereiten
  1. Den Blog vom uberspace erstellen lassen
  1. Uns über einen neuen Blog freuen :)

# Git und Hugo lokal installieren

  - Eine Anleitung zur Installation von git findet ihr hier: [https://book.git-scm.com](https://book.git-scm.com)
  - Wie man Hugo installiert, steht auf der Hugo-Webseite: [https://gohugo.io/getting-started/installing/](https://gohugo.io/getting-started/installing/)

Solltet ihr, wie ich aktuell, an einem Windows-PC sitzen hilft euch vielleicht auch [dieses Video](https://www.youtube.com/embed/G7umPCU-8xc) weiter. Gleiche Videos gibt es auch für andere Betriebssysteme.

# Hugo-Blog anlegen

Ist Hugo installiert, könnt ihr mit `hugo new site <sitename>` einen neuen Blog anlegen. Wir erstellen das fiktive Blog `hugoblog.com`. 

*P.S. Ich nutze auch auf Windows meist die Git-Bash als Konsole. Die kennt auch Befehle wie ssh :)*

```
[Batman@lokal ~]$ hugo new site hugoblog.com
```

Eine Hugo-Website kommt ohne Theme. Das müssen wir also als nächstes herunterladen. Ich mag das [Base16-Theme](https://github.com/htdvisser/hugo-base16-theme). Falls euch das nicht zusagt, findet ihr [hier](https://themes.gohugo.io/) aber sicher ein oder zwei Alternativen. 

Das Theme müsst ihr in den Ordner `themes` eurer eben erstellten Seite herunterladen.

```
[Batman@lokal ~]$ cd hugoblog.com/themes
[Batman@lokal themes]$ git clone https://github.com/htdvisser/hugo-base16-theme.git base16
```

Und schließlich müssen wir Hugo noch mitteilen, dass ihr dieses Theme verwenden wollt. Dazu passen wir die Konfiguration an.

```
[Batman@lokal ~]$ cd ~/hugoblog.com
[Batman@lokal hugoblog.com]$ cat <<EOF>> config.toml
theme = "base16"
pygmentsuseclasses = true
EOF
```

# Erster Artikel

Zeit für den ersten Artikel. Mittels `touch` erstellen wir unter `content/posts/` ein neues Markdown-File und befüllen es mit ein ein bisschen Inhalt. 

```
[Batman@lokal hugoblog.com]$ touch content/posts/cooler-artikel.md
[Batman@lokal hugoblog.com]$ cat <<EOF>> content/posts/cooler-artikel.md
+++
title = "Hello World"
date = "2019-09-06"
layout = ["article"]
author = "Batman"
tags = ["uberspace", "hugo", "blog"]
+++

# Lorem Ipsum

Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet.

EOF
```

Mal drauf schauen, wie der Artikel aussieht? Das geht ganz leicht mit `hugo serve`. Hugo erstellt dann einen lokalen Server und lässt euch eure Seite unter folgendem Link betrachten: [localhost:1313](localhost:1313).

```
[Batman@lokal hugoblog.com]$ hugo serve
Building sites …
                   | EN
+------------------+----+
  Pages            | 37
  Paginator pages  |  2
  Non-page files   |  0
  Static files     |  6
  Processed images |  0
  Aliases          | 14
  Sitemaps         |  1
  Cleaned          |  0
[...]
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

Wenn alles gut aussieht, können wir auf dem uberspace weitermachen.

# uberspace vorbereiten

In diesem Schritt können wir uns am Tutorial auf [https://lab.uberspace.de/](https://lab.uberspace.de/) orientieren. Danke an Christian, luto und Julian. 

## Domain anlegen

Falls ihr eine eigene Domain verwenden wollt, lest im [uberspace-Wiki](https://manual.uberspace.de/web-domains.html) noch einmal nach wie das geht. Hier gehen wir davon aus, dass ihr eine Domain aufgeschaltet habt und diese auf euren Document-Root (`html/`) verweist.

## Hugo installieren

Um Hugo auf dem uberspace zu installieren, laden wir als erstes die neuste Version von Github als tar herunter. Prüfe im [Hugo Repository auf GitHub](https://github.com/gohugoio/hugo/releases) noch einmal welches die neuste Version ist und passe die Befehle entsprechend an. Anschließend entpacken, nach `bin` verschieben und aufräumen. Fertig. 

```
[batman@helium ~]$ wget https://github.com/gohugoio/hugo/releases/download/v0.58.1/hugo_0.58.1_Linux-64bit.tar.gz
[batman@helium ~]$ tar -xvf hugo_0.58.1_Linux-64bit.tar.gz hugo
[batman@helium ~]$ mv hugo ~/bin
[batman@helium ~]$ rm hugo_0.58.1_Linux-64bit.tar.gz
[batman@helium ~]$ hugo version
```

Sollte es mal eine neuere Version von Hugo geben, wiederholt einfach diesen Schritt, um wieder up to date zu sein :)

## Git-Repository für Hugo-Blog anlegen

Als nächstes legen wir uns im Home-Verzeichnis einen neuen Ordner an, in dem wir das Git-Repository für unseren neuen Hugo-Blog ablegen.

```
[batman@helium ~]$ mkdir hugo_websites
[batman@helium ~]$ mkdir hugo_websites/hugoblog.git
[batman@helium ~]$ cd hugo_websites/hugoblog.git
[batman@helium hugoblog.git]$ git init --bare 
```


## Git Hook anlegen

Wir möchten, das der uberspace mit jedem `push` in unser eben erstelltes Repository den Blog neu erstellt und auf der Domain verfügbar macht. Sagen wir ihm das. Dafür passen wir den `post-update`-hook wie folgt an.

```
[batman@helium hugoblog.git]$ touch hooks/post-update
[batman@helium hugoblog.git]$ cat <<EOF>> hooks/post-update
#!/bin/sh

# los geht's
echo "Start hook magic"

# hier definieren wir den Pfad zu unserem Git-Repository
GIT_REPO=$HOME/hugo_websites/hugoblog.git

# hier definieren wir ein temporäres Verzeichnis zum clonen des Repositorys
TMP_GIT_CLONE=$(mktemp -d)

# hier definieren wir den Pfad zu unserer öffentlichen Domain
PUBLIC_WWW=/var/www/virtual/$USER/html

# jetzt clonen wir das Repository in den temporären Ordner
git clone $GIT_REPO $TMP_GIT_CLONE
cd $TMP_GIT_CLONE

# Hugo anschmeißen
~/bin/hugo --cleanDestinationDir --destination $PUBLIC_WWW

# aufräumen
rm -Rf $TMP_GIT_CLONE
EOF
```

# Push auf den uberspace

Damit ist alles vorbereitet. Wir sollten jetzt nur noch unser auf dem uberspace erzeugtes Git-Repository als remote zum lokalen Ordner hinzufügen, unseren Artikel committen und das ganze hochladen.

```
[batman@lokal ~]$ cd hugoblog.com
[batman@lokal hugoblog.com]$ git init
[batman@lokal hugoblog.com]$ git remote add origin ssh://batman@helium.uberspace.de/hugo_websites/hugoblog.git
[batman@lokal hugoblog.com]$ git add .
[batman@lokal hugoblog.com]$ git commit -m "Initial commit"
[batman@lokal hugoblog.com]$ git push origin master
```

Und das war's dann auch schon. Auf den ersten Blick ist das ein bisschen länger geworden, aber diese Schritte müsst ihr ja nur einmal vornehmen. 

Da ich viel von unterwegs blogge, genieße ich es, auf dem iPad zu schreiben und nur einmal `git push` eingeben zu müssen, um die Änderungen zu veröffentlichen. Wie ich von unterwegs blogge, werde ich vermutlich demnächst mal aufschreiben. 

Und jetzt viel Spaß mit eurem neuen Blog.

<div style="width:100%;height:0;padding-bottom:44%;position:relative;"><iframe src="https://giphy.com/embed/LXiElF2dzvUmQ" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/black-panther-LXiElF2dzvUmQ">via GIPHY</a></p>