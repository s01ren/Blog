---
title: Corona-Daten des RKI mit Python visualisieren
date: 2020-12-27T15:00:00+01:00
author: soeren
tags:
  - script
  - data
post: true
draft: false
---

Im letzten Post hatte ich bereits angekündigt, dass ich aktuell daran arbeite [Daten vom RKI mit Python abzuziehen](/2020/rki-daten-laden-python), [diese mit Plotly grafisch aufzubereiten](/2020/rki-daten-visualisieren-python) und schließlich [jeden Morgen in einen Telegram-Kanal zu senden](/2020/rki-daten-telegram-senden-python). 

Heute werde ich euch zeigen, wie ich die heruntergeladenen Daten mit `plotly` in [Python](/tags/python) visualisiere.

<article>
    <h3>Artikel in dieser Serie</h3>
  <ul>
    <li><a href="../rki-daten-laden-python">Teil 1: Corona-Daten des RKI mit Python herunterladen</a></li>
    <li><a href="../rki-daten-visualisieren-python">Teil 2: Corona-Daten des RKI mit Python visualisieren</a></li>
    <li><a href="../rki-daten-telegram-senden-python">Teil 3: Corona-Daten des RKI mit Python an Telegram-Kanal senden</a></li>
  </ul>
</article>

Auch dafür bereiten wir zunächst das Skript vor, indem wir die notwendigen Abhängigkeiten laden. Falls euch etwas fehlt, könnt ihr es in der Regel mittels `pip install <name>` jederzeit installieren.

- `os` und `pandas` benutzen wir erneut, um die zuvor exportierten Daten einzulesen und entsprechend aufzubereiten
- mit `plotly.graph_objects` werden wir die Visualisierung erzeugen
- und `kaleido.scopes.plotly` hilft uns die eigentlich interaktive Grafik von Plotly als statische Datei zu exportieren

{{< highlight python >}}
#imports
import os
import pandas as pd
import plotly.graph_objects as go
import requests

from kaleido.scopes.plotly import PlotlyScope
{{< /highlight >}}

Mit der Hilfe von `os.path.abspath()` defineren wir uns die Arbeitspfade. Gehen wir davon aus, dass sich in dem Verzeichnis, in dem unser Python-Skript liegt, auch die Unterordner `data/` und `img/` befinden. Aus ersterem wollen wir später die Datei `rki.csv` laden. In letzteres den fertigen Plot speichern. 

Außerdem, sollten wir ein paar Farben festlegen.

{{< highlight python >}}
# define working paths
app_dir = os.path.abspath(os.path.dirname(__file__))
data_dir = app_dir + '/data/'
img_dir = app_dir + '/img/'

# define colors
spaetzle_gelb_light = 'rgb(255,254,249)'
spaetzle_gelb_dark = 'rgb(145,113,5)'
light_grey = 'rgb(204, 204, 204)'
{{< /highlight >}}

Laden wir die CSV-Datei und schränken sie auf den/die für uns relevanten Landkreis/e ein. 

{{< highlight python >}}
# import as Pandas data frame and restrict to county "Stuttgart"
rki = pd.read_csv(data_dir + "rki.csv", sep = ";")
df_str = rki.loc[rki.GEN == "Stuttgart"]
df_str.head()
{{< /highlight >}}

Jeder `plotly`-Plot beginnt mit `fig = go.Figure()`. An `fig` werden dann weitere Elemente angefügt. 

- Beginnen wir damit ein Liniendiagramm hinzuzufügen (`add_trace(go.Scatter())`)
- Dieses soll auf der x-Achse die Zeit zeigen (`x = df_str.last_update`)
- Und auf der y-Achse die 7-Tage-Inzidenz pro 100.000 Einwohner (`y = df_str.cases7_per_100k`)
- Die einzelnen Punkte sollen mit der (gerundeten) Inzidenz beschriftet werden (`text = df_str.cases7_per_100k.round()`)
- Angezeigt werden soll eine Linie, ein Marker und die Beschriftung (`mode="lines+markers+text"`)
- Außerdem wollen schwäbische Farben, also Spätzegelb verwenden


{{< highlight python >}}
# create plot
fig = go.Figure()
fig.add_trace(go.Scatter(
    x = df_str.last_update,
    y = df_str.cases7_per_100k,
    text = df_str.cases7_per_100k.round(),
    mode="lines+markers+text",
    textposition="top center",
    textfont_color=spaetzle_gelb_dark,
    line=dict(color = spaetzle_gelb_dark,
))
{{< /highlight >}}


<figure>
  <img alt="Corona Inzidenz" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215169&authkey=%21AOgfByIcS5UKL88&width=660" />
  <figcaption>Corona Inzidenz</figcaption>
</figure>

Schon ganz nett, aber layout-technisch geht da noch was. 

- Geben wir dem Plot einen Titel (`title = '7-Tage Inzidenz pro 100k Einwohner: Stuttgart'`) in der richtigen Farbe (`title_font_color=spaetzle_gelb_dark`)
- Die x-Achse wollen wir dezent in grau zeigen (`xaxis = dict()`)
- Die y-Achse brauchen wir gar nicht (`yaxis = dict()`)
- Als Hintergrundfarbe wollen wir spätzle-gelb (`plot_bgcolor`, `paper_bgcolor`)
- Und eine Legende brauchen wir auch nicht (`showlegend = False`)
- Um Missverständnissen vorzubeugen wollen wir außerdem, dass die y-Achse nicht abgeschnitten wird (vor allem, da wir sie ausgeblendet haben). Mit `update_yaxes(rangemode = 'tozero')` sorgen wir dafür, dass die y-Achse immer bei null beginnt. 

{{< highlight python >}}
# make layout great again!
fig.update_layout(
    title = '7-Tage Inzidenz (100k EW): Stuttgart',
    title_font_color=spaetzle_gelb_dark,
    xaxis=dict(
        showline=True,
        showgrid=False,
        showticklabels=True,
        linecolor=light_grey,
        color = light_grey,
        linewidth=2,
        ticks='outside', 
    ),
    yaxis=dict(
        showgrid=False,
        zeroline=False,
        showline=False,
        showticklabels=False,
    ),
    plot_bgcolor=spaetzle_gelb_light,
    paper_bgcolor=spaetzle_gelb_light,
    showlegend=False,
)
fig.update_yaxes(rangemode = 'tozero')
{{< /highlight >}}

<figure>
  <img alt="Corona Inzidenz" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215168&authkey=%21AN5KQk8ZaIU-Oqk&width=660" />
  <figcaption>Corona Inzidenz</figcaption>
</figure>

<figure>
  <img alt="Corona Inzidenz (y-Achse beginnt bei null)" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215172&authkey=%21ACVATUmS0vy8gBk&width=660" />
  <figcaption>Corona Inzidenz (y-Achse beginnt bei null)</figcaption>
</figure>


Diese Grafik sollten wir jetzt noch abspeichern, damit wir sie im nächsten Schritt in einen [Telegram-Kanal senden](/2020/rki-daten-telegram-senden-python) können. 

{{< highlight python >}}
# export plotly as png
scope = PlotlyScope()
with open(img_dir + "figure.png", "wb") as f:
    f.write(scope.transform(fig, format="png"))
{{< /highlight >}}

