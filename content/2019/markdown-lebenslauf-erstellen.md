---
title: Lebenslauf mit Markdown erstellen
date: 2019-07-08
author: soeren
tags:
  - script
  - tech
post: true
draft: false
---
s
Zum Glück müssen die meisten von uns nicht jeden Tag den eigenen Lebenslauf zu Papier bringen. Wann genau war ich nochmal bei Firma X? Wie lang dauerte das Praktikum in München? Und wie war nochmal der genaue Titel der Bachelorarbeit? ... 

Das eh schon aufwendige Prozedere wird noch ein bisschen aufwendiger, wenn man zusätzlich die Formatierungseigenheiten von Word verstehen muss. Richtig schön wird es, wenn sich Dateiformate geändert und einst schön formatierte Dokumente mittlerweile gruselig aussehen. 

Ich hatte daher seit längerem den Wunsch, den nächsten Lebenslauf so nerdfreundlich wie möglich anzugehen. Idealerweise als leicht zu schreibendes Markdown-File, das trotzdem mit einer modernen Formatierung aufwarten kann. 

Gesucht - [Gefunden](https://blog.chmd.fr/editing-a-cv-in-markdown-with-pandoc.html). Auf dem Blog von Christophe-Marie Duquesne bin ich fündig geworden. Das Design des dort [vorgestellten Lebenslaufs](https://resume.chmd.fr/) hat mich zwar nur semi-überzeugt, aber das zugrunde liegende CSS lässt sich ja leicht anpassen. 

## Lass es uns ausprobieren!

  * Als erstes installieren wir die notwendigen Programme:
Wir benötigen einen Editor. Ich mag aktuell [VS Code](https://code.visualstudio.com/) - du kannst aber jeden beliebigen verwenden (wenn es dich glücklich macht sogar [vim](https://www.vim.org/)).
  * Zur Umwandlung des Markdown-Files nach HTML benötigen wir pandoc. Eine Anleitung zur Installation findest du hier: [https://pandoc.org/installing.html](https://pandoc.org/installing.html)
  * Und schließlich solltest du noch wkhtmltopdf installieren, das aus den erzeugten HTML-Dateien schöne PDFs macht: [https://wkhtmltopdf.org/downloads.html](https://wkhtmltopdf.org/downloads.html)



### Lebenslauf in Markdown übersetzen

Dann übersetzen wir den Lebenslauf nach Markdown. Die Datei könnte wie folgt aussehen (cv.md). Falls euch die Markdown-Syntax entfallen ist, könnt ihr [hier](https://markdown.de/) nochmal spicken. Für mich neu war die [":"-Notation](https://kramdown.gettalong.org/syntax.html#definition-lists), um Definitionen zu erstellen. Das sorgt für ein schönes Zweispalten-Layout - ideal für den Lebenslauf.

#### `cv.md`
```
# Bruce Wayne

> +1 555 123456 • ceo@wayne-enterprises.com\
> Wayne Manor, Gotham, USA

## Berufserfahrung
1939 - heute
:	**Wayne Enterprises**, Vorstandsvorsitzender

1939 - heute
:   **Fledermaus-Fanclub**, Verbrechensbekämpfer
...
```

### Design des Lebenslaufs definieren

Als nächstes definieren wir, wie die einzelnen Bestandteile formatiert werden sollen (style.css). Als Ausgangspunkt empfehle ich euch, die [Vorlage aus Christophe-Maries GitHub-Repo](https://raw.githubusercontent.com/chmduquesne/resume/master/style.css) zu verwenden. Ich habe sie für meinen Lebenslauf aber deutlich angepasst. 

#### `style.css`
```
body {
    font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
    color: #000000;
    width: 800px;
    margin: auto;
    background: #FFFFFF;
    padding: 10px 10px 10px 10px;
}
```

### Befehle nur einmal schreiben, dank kleinem Skript

Um die Consolen-Befehle nur einmal tippen zu müssen, erstellen wir als letztes noch ein kleines Skript (`create_cv.sh`). **Bitte denkt daran**, die Dateinamen anzupassen falls ihr andere verwendet habt. Ich gehe davon aus, dass der Lebenslauf ``cv.md`` und das Stylesheet ``style.css`` heißt. 

#### `create_cv.sh`
```
#!/bin/bash

# Translate MD --> HTML
pandoc --standalone -c style.css --from markdown --to html -o cv.html cv.md --metadata pagetitle="CV"

# Translate HTML --> PDF
wkhtmltopdf cv.html cv.pdf
```

### Lebenslauf erstellen

Lassen wir es laufen! Skript ausführbar machen und los geht's!

```
batman@helium:~$ chmod + x create_cv.sh
batman@helium:~$ ./create_sv.sh
```

Danach solltet ihr zwei neue Dateien ``cv.html`` und ``cv.pdf`` im Ordner finden. 

Fertig :-)

Unser Lebenslauf von Batman sieht übrigens im ersten Entwurf wie folgt aus. Mit ein bisschen mehr Inhalt und Farbe lässt sich das schnell aufhübschen.


<figure>
  <img alt="This is a black swan" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215175&authkey=%21AGo0i8_B3kkni1s&width=660" />
  <figcaption>Batman Lebenslauf</figcaption>
</figure>

### Code auf Github

Wie immer findet ihr den gesamten Code auf GitHub: [https://github.com/s01ren/markdown_cv](https://github.com/s01ren/markdown_cv)
