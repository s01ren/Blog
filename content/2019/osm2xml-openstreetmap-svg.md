---
title: "OSM Daten als SVG exportieren"
date: "2019-09-09"
author: "soeren"
tags: 
  - osm
  - python
post: true
draft: false
---

# OSM2XML

Ich möchte euch heute ein cooles Projekt vorstellen. [Der Hase](https://twitter.com/mhaseneyer/status/1170462635214745600) nutzt die Zeit während seines Spezialprojektes sehr effektiv und hat am Wochenende ein [Python-Projekt auf GitHub](https://github.com/mhaseneyer/osmxml) geladen.

Mit dem Skript könnt ihr Daten aus [OpenStreetMaps](https://www.openstreetmap.org) als XML oder SVG exportieren. Das ist super-nützlich, wenn ihr eine skalierbare Grafik vom "Grundriss" eurer Heimatstadt, eures [Lieblingsbundeslandes](https://shop.baden-wuerttemberg.de/out/pictures/generated/product/1/665_665_75/bw_aufkleber_nett_hier_665x665.png), eurer Lieblingsinsel, [Bielefeld](https://www.openstreetmap.org/relation/62646) oder [Trinidad und Tobago](https://www.openstreetmap.org/relation/555717) braucht. 

Das bietet sich auch super für ein Ratespiel an. Wer weiß welche Stadt das ist?

<figure>
  <img alt="This is a black swan" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215164&authkey=%21ACwGOlR3Lenacog&width=256" />
  <figcaption>Umrisse von ???</figcaption>
</figure>

# Los geht's!

Als erstes solltet ihr die einzige Voraussetzung auf eurem System installieren: [Python 3](https://www.python.org/). Ich empfehle (vor allem unter Windows) das mittels [Anaconda](https://www.anaconda.com/) zu tun. Ladet euch die neuste Version herunter und folgt den Anweisungen des Installers: [https://www.anaconda.com/distribution/](https://www.anaconda.com/distribution/).

Ob alles geklappt hat, könnt ihr in der Windows Eingabeaufforderung testen. Wenn ihr `python` eingebt, solltet ihr einen Hinweis zur installierten Version erhalten.

```
C:\Users\Batman>python
Python 3.7.0 (default, Jun 28 2018, 08:04:48) [MSC v.1912 64 bit (AMD64)] :: Anaconda, Inc. on win32
Type "help", "copyright", "credits" or "license" for more information.
```

Als nächstes ladet ihr euch aus dem Repository das Skript herunter: [https://github.com/mhaseneyer/osmxml/blob/master/src/osm2svg.py](https://github.com/mhaseneyer/osmxml/blob/master/src/osm2svg.py)


Und als letztes sucht ihr noch den passenden OSM-Link für das Objekt eurer Wahl heraus. Um das Rätsel von oben aufzulösen, nehmen wir mal an ihr interessiert euch für die Umrisse von Stuttgart: [https://www.openstreetmap.org/relation/2793104](https://www.openstreetmap.org/relation/2793104). 

Die sehen in OSM wie folgt aus.

<figure>
  <img alt="This is a black swan" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215166&authkey=%21AHXPGK9kx9FXv6w&width=256 " />
  <figcaption>Stuttgart auf OpenStreetMaps</figcaption>
</figure>


# Die Wundermaschine anwerfen

Mit nur einem Befehl lassen wir Python jetzt das Objekt von OSM herunterladen und in eine SVG umwandeln. Dafür übergebt ihr dem Skript einfach den Link zu OSM und einen Namen für das SVG-File. 

```
C:\Users\Batman>python link/to/osm2svg.py https://www.openstreetmap.org/relation/2793104 stuttgart.svg
```

So einfach ist das. Richtig geiler Scheiß, Danke! 👍


