---
title: "Etwas coolere ggplots erstellen"
date: "2019-10-08"
author: "soeren"
tags: 
  - r
  - tidytuesday
  - baden-wuerttemberg
post: true
draft: false
---

Leider muss ich zugeben, dass meine bisherige Beteiligung am [TidyTuesday](/tags/tidytuesday) weit hinter meinen Erwartungen zurück geblieben ist. Bisher habe ich lediglich einmal mitgemacht und das ist auch schon Monate her. 

Natürlich überfliege ich trotzdem jede Woche [unzählige Tweets](https://twitter.com/search?q=%23TidyTuesday) und erfreue mich an tollen Grafiken zu verschiedenen Themen. So wie beispielsweise an dieser [super-coole Visualisierung](https://twitter.com/jakekaupp/status/1179821493393469440/photo/1), die wirklich Lust darauf gemacht hat, mal wieder was mit Karten zu machen. 

Und da ich mit ein bisschen Glück demnächst wieder Vollzeit-[Baden-Württemberger](/tags/baden-wuerttemberg) werde, werden wir uns nicht Pizza-Restaurants in NYC sondern was aus dem lokaleren Datenbestand anschauen. 

In diesem Blog-Post möchte ich zeigen, wie sich Visualisierungen in meinem Workflow entwickeln, wie ich von ``ggplot() + geom_point()`` zu etwas halbwegs ansehnlichem komme. Dass manche Schritte dabei ein Fall für [@accidential aRt](https://twitter.com/accidental__art) sind, versteht sich vermutlich von selbst 😅

## Daten sammeln

Lange Rede - kurzer Sinn. Ich habe also auf die Webseite des [Statistischen Landesamtes](https://www.statistik-bw.de/) aufgerufen und mir einfach den erstbesten Datensatz runtergeladen, der Informationen auf Kreis-Ebene zur Verfügung stellt. Wir werden heute also etwas zur [Bevölkerungsdichte in Baden-Württemberg](https://www.statistik-bw.de/BevoelkGebiet/Bevoelk_I_Flaeche_j.csv) lernen. 

Damit wir die Daten auch hübsch visualisieren könne, habe ich mir darüber hinaus vom [Landesamt für Geoinformation und Landentwicklung](https://www.lgl-bw.de/) das entsprechende [Shapefile](https://www.lgl-bw.de/lgl-internet/opencms/de/07_Produkte_und_Dienstleistungen/Open_Data_Initiative/) heruntergeladen.

Da die Daten sauber vorbereitet sind, können wir sie mit wenigen Zeilen einlesen und in das gewünschte Format bringen.

{{< highlight r >}}
# --------------------------------
# notwendige Packete laden
# --------------------------------
library(tidyverse)
library(sf)

# --------------------------------
# Dateipfade definieren
# --------------------------------
file_bevoelkerung <- "link/to/Bevoelk_I_Flaeche_j.csv"
file_shapes <- "link/to/AX_Gebiet_Kreis.shp"

# --------------------------------
# Daten einlesen und aufbereiten
# --------------------------------
EINWOHNER.Kreise <- 
  read_delim(
    file = file_bevoelkerung, 
    locale = locale(encoding = readr::guess_encoding(file_bevoelkerung) %>% select(encoding) %>% unlist()),
    delim = ";", 
    skip = 20,
    col_names = c(
      "ID.Regionaleinheit",
      "AmtlicherGemeindeschluessel",
      "PLZ",
      "Regionalname",
      "Stichtag",
      "Bevoelkerung_insgesamt",
      "Gemeindegebiet_ha",
      "Bevoelkerungsdichte_EW_km2"
    )
  ) %>% 
  filter(
    Stichtag == "31.12.2018",
    ID.Regionaleinheit == "KR"
  ) %>% 
  left_join(
    y = read_sf(file_shapes),
    by = c("AmtlicherGemeindeschluessel" = "Schlüssel")
  )
{{< /highlight >}}

## Basis-Plot erstellen
Nachdem wir die Daten eingelesen und aufbereitet haben, sollten wir uns anschauen was ``ggplot()`` vorschlägt. Ein klarer Vorteil ist, dass selbst dieser erste Versuch schon relativ ansehnlich ist. Aber da geht natürlich noch mehr ... 

{{< highlight r >}}
# --------------------------------
# Basisplot erstellen
# --------------------------------
p <- ggplot(data = EINWOHNER.Kreise) + 
  geom_sf(aes(fill = Bevoelkerungsdichte_EW_km2))
p
{{< /highlight >}}

<figure>
  <img alt="This is a black swan" src="https://github.com/s01ren/ttbw/raw/master/plots/step01.png" />
</figure>

## All hail the Spätzle!

Theoretisch könnten wir hier aufhören. Wir haben eine Karte auf der die Landkreise entsprechend ihrer Bevölkerungsdichte eingefärbt sind. Es wird auf einen Blick deutlich, dass in Stuttgart sehr viele Menschen auf wenig Raum wohnen, während es im Nord- und Südosten eher ruhig zugeht. 

Nun wollten wir ja aber einen etwas cooleren Plot erstellen. Dazu ändern wir als erstes die Farben. Und weil die Landesfarbe halt *spätzle-gelb* ist, nehmen wir das als Basis. Von [coolors.co](https://coolors.co) lasse ich mir einen dazu passenden Rot-Ton erzeugen und definiere so die beiden Extremwerte meiner Farbskala. 

{{< highlight r >}}
# --------------------------------
# Farben definieren
# --------------------------------
colour_spaetzlegelb <- "#FFFEF9"
colour_rangemax <- "#AA4465"

# --------------------------------
# Plot aktualisieren
# --------------------------------
p <- p + 
  scale_fill_gradient(low = colour_spaetzlegelb, high = colour_rangemax)
p
{{< /highlight >}}

<figure>
  <img alt="This is a black swan" src="https://github.com/s01ren/ttbw/raw/master/plots/step02.png" />
</figure>

## Make the theme great again!

Das sieht schon besser (aber noch nicht gut) aus. Als nächstes passen wir das Theme an, entfernen die Legende, den grauen Hintergrund und die Achsen, deren Zahlenwerte aktuell eher verwirrend als hilfreich sind. 


{{< highlight r >}}
# --------------------------------
# Plot aktualisieren
# --------------------------------
p <- p + 
  theme(
    
    # Achsen entfernen -----------
    axis.line = element_blank(), 
    axis.title = element_blank(),
    axis.ticks = element_blank(),
    axis.text = element_blank(),

    # Hintergrund anpassen -------
    panel.background = element_rect(fill = colour_spaetzlegelb),
    plot.background = element_rect(fill = colour_spaetzlegelb),
    
    # Legende entfernen ----------
    legend.position = "none"
  )
p
{{< /highlight >}}

<figure>
  <img alt="This is a black swan" src="https://github.com/s01ren/ttbw/raw/master/plots/step04.png" />
</figure>

## Reden ist Silber, Annotierung ist Gold!

So langsam wird es doch. Es fehlt aber noch ein bisschen Kontext. Also fügen wir neben der eigentlich Grafik einen Titel und eine kurze Zusammenfassung der Ergebnisse ein. Das hilft die Hauptaussage der Visualisierung noch einmal (in prosa) hervorzuheben. 


{{< highlight r >}}
# --------------------------------
# Untertitel verfassen
# --------------------------------
plot_subtitle <- str_c(
  "Die Karte zeigt die Bevölkerungsdichte nach Kreisen in Baden-Württemberg.",
  "Am dünnsten besiedelt ist der Main-Tauber-Kreis. Hier wohnen 101 Personen pro km².",
  "Mehr als 30x mehr Personen pro Quadratkilomenter wohnen in der Landeshauptstadt.",
  sep = " "
)

# --------------------------------
# Quellen definieren
# --------------------------------
plot_caption <- str_c(
  "Daten: Statistisches Landesamt Baden-Württemberg;",
  "Karten: Landesamt für Geoinformation und Landentwicklung",
  "Grafik: https://gluecko.se",
  sep = " "
)

# --------------------------------
# Notwendige Koordinaten speichern
# --------------------------------
coord_xmin <- sf::st_bbox(EINWOHNER.Kreise$geometry)$xmin
coord_xmax <- sf::st_bbox(EINWOHNER.Kreise$geometry)$xmax
coord_ymin <- sf::st_bbox(EINWOHNER.Kreise$geometry)$ymin
coord_ymax <- sf::st_bbox(EINWOHNER.Kreise$geometry)$ymax

# --------------------------------
# Plot aktualisieren
# --------------------------------
p <- p + 
  annotate("text", label = "Bevölkerungsdichte nach Kreisen", x = .7 * coord_xmin, y = 1.01 * coord_ymax, size = 6, hjust = 0) +
  annotate("text", label = str_wrap(plot_subtitle, 40), x = .7 * coord_xmin, y = coord_ymax, size = 3, hjust = 0) +
  annotate("text", label = str_wrap(plot_caption, 40), x = .7 * coord_xmin, y = coord_ymax, size = 3, hjust = 0, vjust = 2.5)
p
{{< /highlight >}}

<figure>
  <img alt="This is a black swan" src="https://github.com/s01ren/ttbw/raw/master/plots/step05.png" />
</figure>

## Look here!

Na das sieht doch jetzt echt nach was aus! Wir sollten allerdings noch ein paar Kreise explizit hervorheben. Extremfälle bieten sich an - fügen wir also noch Hinweise auf die Kreise mit der höchsten bzw. niedrigsten Bevölkerungsdichte ein. 

{{< highlight r >}}
# --------------------------------
# Centroid berechnen
# --------------------------------
get_centroid <- function(name){
  coords <- 
    EINWOHNER.Kreise %>% 
    filter(Name %in% name) %>% 
    select(geometry) %>% 
    st_as_sf() %>% 
    st_centroid() %>% 
    unlist()
  
  if(length(coords)>2){
    warning("More than one centroid found!")
  }
  
  returnData <- 
    tribble(
      ~"Name", ~"x", ~"y",
      name, coords[1], coords[2]
    )
  
  return(returnData)
}

# --------------------------------
# Notwendige Koordinaten speichern
# --------------------------------
coord_Stuttgart_xlabel <- 1.05 * coord_xmin
coord_Stuttgart_ylabel <- 1.0045 * get_centroid("Stuttgart")$y
coord_Mannheim_xlabel <- 1.1 * coord_xmin
coord_Mannheim_ylabel <- 1.005 * get_centroid("Mannheim")$y
coord_Karlsruhe_xlabel <- 1.1 * coord_xmin
coord_Karlsruhe_ylabel <- 1.005 * get_centroid("Karlsruhe")$y
coord_MainTauberKreis_xlabel <- 0.95 * coord_xmax
coord_MainTauberKreis_ylabel <- 1.005 * get_centroid("Main-Tauber-Kreis")$y
coord_Sigmaringen_xlabel <- 0.98 * coord_xmax
coord_Sigmaringen_ylabel <- 1.005 * get_centroid("Sigmaringen")$y
coord_NeckarOdenwaldKreis_xlabel <- 0.82 * coord_xmax
coord_NeckarOdenwaldKreis_ylabel <- 1.0115 * get_centroid("Neckar-Odenwald-Kreis")$y

# --------------------------------
# Plot aktualisieren
# --------------------------------
p <- p + 
  # Top 1: Stuttgart -------------  
  annotate("segment", x = coord_Stuttgart_xlabel, y = coord_Stuttgart_ylabel, xend = get_centroid("Stuttgart")$x,  yend = get_centroid("Stuttgart")$y) + 
  annotate("label", label = "Stuttgart\n3.062 EW/km²", x = coord_Stuttgart_xlabel, y = coord_Stuttgart_ylabel) + 
  
  # Top 2: Mannheim --------------
  annotate("segment", x = coord_Mannheim_xlabel, y = coord_Mannheim_ylabel, xend = get_centroid("Mannheim")$x, yend = get_centroid("Mannheim")$y) + 
  annotate("label", label = "Mannheim\n2.134 EW/km²", x = coord_Mannheim_xlabel, y = coord_Mannheim_ylabel) + 
  
  # Top 3: Karlsruhe -------------
  annotate("segment", x = coord_Karlsruhe_xlabel, y = coord_Karlsruhe_ylabel, xend = get_centroid("Karlsruhe")$x, yend = get_centroid("Karlsruhe")$y) + 
  annotate("label", label = "Karlsruhe\n1.805 EW/km²", x = coord_Karlsruhe_xlabel, y = coord_Karlsruhe_ylabel) + 

  # Bottom 1: Main-Tauber-Kreis --
  annotate("segment", x = coord_MainTauberKreis_xlabel, y = coord_MainTauberKreis_ylabel, xend = get_centroid("Main-Tauber-Kreis")$x, yend = get_centroid("Main-Tauber-Kreis")$y) +
  annotate("label", label = "Main-Tauber-Kreis\n101 EW/km²", x = coord_MainTauberKreis_xlabel, y = coord_MainTauberKreis_ylabel) + 

  # Bottom 2: Sigmaringen --------
  annotate("segment", x = coord_Sigmaringen_xlabel, y = coord_Sigmaringen_ylabel, xend = get_centroid("Sigmaringen")$x, yend = get_centroid("Sigmaringen")$y) +
  annotate("label", label = "Sigmaringen\n109 EW/km²", x = coord_Sigmaringen_xlabel, y = coord_Sigmaringen_ylabel) +

  # bottom 3: Neckar-Odenwald-Kreis --
  annotate("segment", x = coord_NeckarOdenwaldKreis_xlabel, y = coord_NeckarOdenwaldKreis_ylabel, xend = get_centroid("Neckar-Odenwald-Kreis")$x, yend = get_centroid("Neckar-Odenwald-Kreis")$y) +
  annotate("label", label = "Neckar-Odenwald-Kreis\n127 EW/km²", x = coord_NeckarOdenwaldKreis_xlabel, y = coord_NeckarOdenwaldKreis_ylabel)
p
{{< /highlight >}}

<br>
<div class="card mx-auto" style="width: 75%;">
<div class="card-header">
    Finale Visualisierung
</div>
<img class="card-img-top" src="https://github.com/s01ren/ttbw/raw/master/plots/Bevoelkerungsdichte.png">
</div>
<br>

Eine große Version der Grafik und den vollständigen Code am Stück gibt es - wie immer - bei [GitHub](https://github.com/s01ren/ttbw). 