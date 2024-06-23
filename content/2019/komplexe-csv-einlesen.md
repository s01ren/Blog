---
title: Komplexe CSV-Dateien einlesen
date: 2019-08-01
author: soeren
tags:
  - script
  - data
post: true
draft: false
---

Natürlich gibt es einfachere Dinge, als Daten in R einzulesen. Und trotzdem ist es zumindest seit der Einführung des [`tidyverse`](https://www.tidyverse.org/) und den damit propagierten *tidy data principles* sehr viel einfacher geworden saubere Daten zu laden. Leider hat nicht jeder Sensor-Hersteller das [Paper von Hadley Wickem](https://www.jstatsoft.org/index.php/jss/article/view/v059i10/v59i10.pdf) gelesen.

Dieser Tage ist eine sehr spannende Aufgabe auf meinem Schreibtisch gelandet. Es klang zunächst so einfach: Wir haben da eine Maschine. Die misst etwas und schreibt ihre Ergebnisse in eine Textdatei. Diese müsste eingelesen und weiterverarbeitet werden. Ich dachte, dass sich das relativ leicht mit `read_delim()` lösen lassen können müsste. Dachte ich...

### Das Problem

Die Sache hatte natürlich einen Haken. Den Vorteil, dass die Maschine ihre Messwerte in eine CSV-Datei (und kein proprietäres Format) schrieb, machte sie damit zunichte, dass sie in einer Datei mehrere Header einfügte. *"Wie meinen?"*, denkt ihr vielleicht jetzt.

Stellen wir uns dazu vor, dass mit der Maschine die Inhaltsstoffe verschiedener Burger gemessen werden sollen. Man zielt also auf einen Burger und bekommt als Ergebnis die Anteile an Bun, Patty, Salat, etc. die in diesem Burger verwurstelt wurden. Das Problem ist nun, dass die Maschine beispielsweise bei einem vegetarischen Burger den Eintrag für Patty nicht einfach leer lässt sondern gar nicht erst notiert. Je nach Anzahl der Zutaten im Burger hat die CSV also unterschiedlich viele Spalten.

Immernoch unklar? Dann werft doch mal einen Blick auf das folgende Beispiel.

<figure>
<table>
	<colgroup>
		<col width="20%" />
		<col width="20%"/>
        <col width="20%" />
        <col width="20%" />
        <col width="20%" />
	</colgroup>
	<tbody>
        <tr>
            <td><b>ID</b></td>
            <td><b>Anteil Bun</b></td>
            <td><b>Anteil Patty</b></td>
            <td><b>Anteil Salat</b></td>
            <td><b>Anteil Tomate</b></td>
        </tr>
        <tr>
            <td>Hamburger</td>
            <td align="right">.25</td>
            <td align="right">.25</td>
            <td align="right">.25</td>
            <td align="right">.25</td>
        </tr>
        <tr>
            <td>Cheeseburger</td>
            <td align="right">.20</td>
            <td align="right">.20</td>
            <td align="right">.25</td>
            <td align="right">.35</td>
        </tr>
        <tr><td>.</td><td></td><td></td><td></td><td></td></tr>
        <tr>
            <td><b>ID</b></td>
            <td><b>Anteil Bun</b></td>
            <td><b>Anteil Salat</b></td>
            <td><b>Anteil Tomate</b></td>
            <td></td>
        </tr>
        <tr>
            <td>Veggi-Burger</td>
            <td align="right">.20</td>
            <td align="right">.20</td>
            <td align="right">.25</td>
            <td></td>
        </tr>
	</tbody>
</table>
<figcaption>Beispiel des Burger-CSVs</figcaption>
</figure>




Unschön, nicht? Und obwohl ich in den vergangen Jahren wirklich einige [R](/tags/r)-Tricks gelernt habe, hat mich das einige Arbeit gekostet. 

### Die Lösung

Meine Lösung ist sicher nicht die beste, aber funktioniert und hilft vielleicht der ein oder anderen, die ein ähnliches Problem hat. 

Zunächst speichere ich mir den String, der den Start eines neuen Headers identifiziert (erster Spaltenname im Datensatz) und lese das gesamte File mittels `readLines()` ein. Das setzt natürlich voraus, dass zumindest die erste Spalte immer gemessen wird.

```
# load packages -----------------------------------------------------------
require("tidyverse")

# define header identification --------------------------------------------
header_id <- "ID"

# read raw data -----------------------------------------------------------
RAW <- readLines(file.choose())
```

(Ich verwende `file.chose()`, um die Benutzung für den Endanwender so komfortabel wie möglich zu halten)

Dann baue ich einen Datensatz, der genausoviele Zeilen wie das originale CSV-File hat und ermittle (mit `header_id`) welche davon Header-Zeilen sind. Mittels `split()` fülle ich die restlichen Zeilen mit der dazugehörigen Header-Nummer auf.

```
# find lines to split at --------------------------------------------------
header_lines <- which(substr(RAW, 1, nchar(header_id)) == header_id)

# create a list of all datasets within the CSV file -----------------------
DATASETS <- tibble(
  Row = 1:length(RAW),
  Data = RAW,
  Split = ifelse(Row %in% header_lines, header_lines, NA)
) %>%
  fill(Split) %>% 
  split(f = .$Split)
```

Schließlich schleife ich einmal über die jeweiligen Headernummern und picke mir aus den Rohdaten alle Informationen zusammen.

```
# extract relevant data ---------------------------------------------------
BURGERS <- tibble(
  ID = character(),
  Bun = numeric(), 
  Patty = numeric(),
  Salat = numeric(),
  Tomate = numeric()
)

for(i in 1:length(DATASETS)){
  # print loop ............................................................
  print(i)
  
  # extract data ..........................................................
  tmp_str <- DATASETS[[i]] %>% select(Data) %>% unlist()
  tmp_data <- read_delim(paste(tmp_str, collapse="\n"), delim = ";", na = "<LOD") %>% 
    select(
      ID
      matches(names(BURGERS))
      ) %>%
    filter(!is.na(ID))
  
  # append to results .....................................................
  BURGERS <- BURGERS %>% bind_rows(tmp_data) 
}
```

Et voilà, wir haben einen Datensatz mit dem man arbeiten kann :)
