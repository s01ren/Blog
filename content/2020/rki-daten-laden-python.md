---
title: "Corona-Daten des RKI mit Python herunterladen"
date: 2020-12-26T17:57:33+01:00
author: "soeren"
tags: ["telegram", "python", "open-source", "bot", "corona"]
post: true
draft: false
---

Auch Ende 2020 hält uns [Corona](/tags/corona) fest im Griff. Vor allem vor den Feiertagen habe ich mir die Frage gestellt, wie sinnvoll es ist, wenn Personen aus unterschiedlichen Landesteilen zusammenkommen. Ich wollte also auf einen Blick für meine Familie sehen können, wie sich die Lage in den jeweiligen Landkreisen entwickelt. Idealerweise automatisiert jeden Morgen auf meinem Handy - ohne die völlig benutzerunfreundlichen Veröffentlichungen des RKI durchstöbern zu müssen. 

Die Idee war geboren. Ein [Python](/tags/python)-Skript sollte mir jede Nacht die Daten vom RKI abziehen, in einer Grafik aufbereiten und via [Telegram](/tags/telegram) an meine Familie verteilen. Wenn die Muse anhält, werde ich in den nächsten Tagen also kurz beschreiben, wie ich [Daten vom RKI mit Python abziehe](/2020/rki-daten-laden-python), [diese mit Plotly grafisch aufbereite](/2020/rki-daten-visualisieren-python) und schließlich [in einen Telegram-Kanal jeden Morgen poste](/2020/rki-daten-telegram-senden-python).


<article>
    <h3>Artikel in dieser Serie</h3>
  <ul>
    <li><a href="../rki-daten-laden-python">Teil 1: Corona-Daten des RKI mit Python herunterladen</a></li>
    <li><a href="../rki-daten-visualisieren-python">Teil 2: Corona-Daten des RKI mit Python visualisieren</a></li>
    <li><a href="../rki-daten-telegram-senden-python">Teil 3: Corona-Daten des RKI mit Python an Telegram-Kanal senden</a></li>
  </ul>
</article>

Beginnen wir mit dem Abziehen der Daten aus dem [NPGEO Corona Hub](https://npgeo-corona-npgeo-de.hub.arcgis.com/). Das RKI veröffentlicht die Daten - aus meiner Sicht sehr dürftig dokumentiert - über arcgis.com, das mittels API von Python aus angesprochen werden kann. Wir benötigen zunächst also die URL.

Da ich mich für Daten auf Landkreisebene interessiere, öffnen wir [https://npgeo-corona-npgeo-de.hub.arcgis.com/search?collection=Dataset](https://npgeo-corona-npgeo-de.hub.arcgis.com/search?collection=Dataset) und wählen den [Landkreis-Datensatz](https://npgeo-corona-npgeo-de.hub.arcgis.com/datasets/917fc37a709542548cc3be077a786c17_0) aus. Über den [API-Explorer-Tab](https://npgeo-corona-npgeo-de.hub.arcgis.com/datasets/917fc37a709542548cc3be077a786c17_0/geoservice) können wir Filter setzen, die relevanten Felder auswählen und die sich ergebende URL kopieren. 

<figure>
  <img alt="API Explorer" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215170&authkey=%21AH8VWilZsTogEiQ&width=660" />
  <figcaption>API Explorer</figcaption>
</figure>


Dann bereiten wir unser Python-Skript vor, indem wir die notwendigen Bibliotheken laden. 

  - mit `json` und `requests` laden wir die Daten
  - `pandas` vereinfacht die Aufräum-Arbeit in den heruntergeladenen Daten
  - `os` und `datetime` helfen uns die Daten in Unterordnern und mit Datumsstempeln zu speichern

{{< highlight python >}}
# imports
import json
import os
import pandas as pd
import requests

from datetime import datetime
from pandas.io.json import json_normalize
{{< /highlight >}}

Im nächsten Schritt definieren wir ein paar Variablen.

  - `api_url` ist die API-Query-URL, die wir von arcgis.com kopiert haben.
  - `app_dir` und `data_dir` enthalten den Dateipfad zum Verzeichnis, in dem die Python-Datei liegt, sowie einem Unterordner `data`. In letzterem werden wir die heruntergeladenen Daten speichern.

{{< highlight python >}}
# define variables
api_url = r'https://services7.arcgis.com/mOBPykOjAyBO2ZKk/arcgis/rest/services/RKI_Landkreisdaten/FeatureServer/0/query?where=1%3D1&outFields=*&outSR=4326&f=json'
app_dir = os.path.abspath(os.path.dirname(__file__))
data_dir = app_dir + '/data/'
{{< /highlight >}}

Der eigentliche Download verfolgt über eine Kombination von `json.loads()` mit `requests.get()`. Mittels `json_normalize()` wandeln wir das heruntergeladen JSON-File in einen Pandas-Datensatz um. 

{{< highlight python >}}
# download and normalize
d = json.loads(requests.get(api_url).text)
df = json_normalize(d, record_path=['features'])
{{< /highlight >}}

Perfekt. Mittels `df.head()` können wir einen Blick auf die Daten werfen. Mir persönlich gefällt das `attributes.`-Präfix bei den Spaltennamen nicht. Lass es uns also entfernen. Dafür erzeugen wir ein leeres Array, schleifen über die Spaltennamen, ersetzen dabei jeweils "attributes." mit "" und überschreiben schließlich die Spaltennamen mit dem Array. Es gibt dafür sicher einen besseren Weg.

{{< highlight python >}}
# remove "attributes." from field names
colnames = []
for i in range(len(df.columns)):
    colnames.append(df.columns[i].replace('attributes.', ''))

df.columns = colnames
{{< /highlight >}}

Das RKI veröffentlicht über die API lediglich die neusten Daten. Sofern wir einen Zeitverlauf analysieren wollen, müssen wir die Daten also abspeichern. Der Einfachheit halber sichern wir den heruntergeladenen Datensatz als CSV mittels Pandas `to_csv()`-Funktion. Damit wir die Dateien den einzelnen Tagen zuordnen können, extrahieren wir aus der Spalte `last_update` das Datum und bauen das in den Namen der exportierten CSV-Datei ein. 

{{< highlight python >}}
# extract last update date (for filename)
file_date = datetime.strptime(df['last_update'][0], '%d.%m.%Y, %H:%M Uhr').strftime("%Y%m%d_%H%M")

# epxort CSV to data directory, using last update date for filename
df.to_csv(
    path_or_buf = data_dir + "rki_" + file_date + ".csv",
    sep = ";",
    header = True,
    index = False,
    encoding = 'utf-8'
)
{{< /highlight >}}

Und damit sind wir eigentlich auch schon fertig. Damit ich nicht für jede Anwendung die Daten neu einlesen und zusammenfügen muss, habe ich meinem Python-Skript noch einen letzten Absatz mitgegeben. Dieser erzeugt zunächst eine Liste aller unter `data/` abgespeicherten Dateien (deren Dateiname mit `rki_` beginnt), liest diese anschließend der Reihe nach ein, fügt sie zu einem Datensatz zusammen und speichert diesen als zusätzliches CSV-File ab. 

{{< highlight python >}}
# create list of all exported CSV files
filepaths = [f for f in os.listdir((data_dir)) if f.startswith('rki_')]
filepaths

# append exported CSV files
rkitmp = []
for f in filepaths:
    dftmp = pd.read_csv(data_dir + f, sep = ";", decimal=",")
    rkitmp.append(dftmp)

# concat to Pandas data frame
rki = pd.concat(rkitmp, axis=0, ignore_index=True, sort = True)
rki = rki.drop_duplicates()

# export combined data
rki.to_csv(
    path_or_buf = data_dir + "rki.csv",
    sep = ";",
    header = True,
    index = False,
    encoding = 'utf-8'
)
{{< /highlight >}}


