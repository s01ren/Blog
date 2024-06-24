---
title: Bloggen vom iPad
date: 2020-12-28T12:00:00+01:00
author: soeren
tags:
  - hugo
  - blog
  - tech
post: true
draft: false
---

Leider versanden Ideen für neue Blog-Beiträge bei mir häufiger, weil mir der *Aufwand* zu groß vorkommt abends den Laptop nochmal hochzufahren und die Gedanken zu ordnen. Ich bin daher immer auf der Suche nach Möglichkeiten die Hemmschwelle, so weit wie möglich abzusenken.

Als guten Ansatz könte ich mir vorstellen, den Laptop aus der Gleichung zu streichen und von einem Gerät zu bloggen, das ich eh andauernd in der Hand habe: Smartphone oder Tablet zum Beispiel. Weil das Handy-Display aber vielleicht doch ein bisschen klein ist, schaue ich mir heute an, ob ich *gut* vom iPad aus bloggen kann. Und **ja**, dieser Artikel ist komplett auf dem iPad entstanden. 

Als Unterbau für mein Blog nutze ich [Hugo](/tags/hugo), das ich ([wie her beschrieben](/2019/hugo-mit-git-auf-uberspace-benutzen)) auf einem [uberspace](/tags/uberspace) betreibe. Das hat den Vorteil, das ich einen Teil der Magie auf den Server auslagern kann. Konkret habe ich im [Git](/tags/git)-Repository auf dem Server einen `post-update`-Hook eingerichtet. Dieser erstellt die statischen Files automatisch nach jedem Commit. 

Auf dem i-Device muss ich demnach Hugo nicht installiert haben sondern benötige lediglich einen Git-Client und einen Texteditor. Beides habe ich in [Working Copy](https://workingcopyapp.com/) gefunden. 

Die App kostet ein bisschen was, ist aus meiner Sicht aber jeden Cent wert. Vor allem, weil es hervorragend in [iOS](/tags/ios) eingebunden ist. So ist das Repository bspw. über die Dateien-App aufrufbar - ich kann den Inhalt also mit vielen verschiedenen Apps bearbeiten.

Mein Workflow gestaltet sich nun wie folgt:

1. Innerhalb von Working Copy dupliziere ich ein existierendes Markdown-File als Grundlage für den neuen Artikel und passe den YAML-Header an.
1. Im integrierten Texteditor öffne ich die neue Datei und schreibe den Artikel. Für ein bisschen mehr Syntax-Highlighting, kann man die Datei auch in einem externen Editor (bspw. Textastic) öffnen.
1. Wenn ich Screenshots oder andere Dateien einbinden möchte, speichere ich diese über die iOS-Dateien-App direkt im `static`-Ordner des Repositories. 
1. Wenn ich fertig bin, nutze ich Working Copy um die Änderungen zu committen und anschließend ins Repository zu pushen. Der uberspace aktualisiert dann automatisch den Blog. 

Einfacher gehts kaum. 