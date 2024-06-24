---
title: Corona-Daten des RKI mit Python an Telegram-Kanal senden
date: 2020-12-27T17:00:00+01:00
author: soeren
tags:
  - script
  - data
post: true
draft: false
---

Dritter und letzter Teil der Reihe in der ich beschreibe wie ich [Daten vom RKI mit Python abziehe](/2020/rki-daten-laden-python), [diese mit Plotly grafisch aufbereite](/2020/rki-daten-visualisieren-python) und schließlich [jeden Morgen in einen Telegram-Kanal sende](/2020/rki-daten-telegram-senden-python). 

In diesem Artikel schauen wir uns gemeinsam an, wie man einen Telegram-Bot erstellt, diesen einem Kanal hinzufügt und schließlich mit [Python](/tags/python) Nachrichten verschicken lässt. 

<article>
    <h3>Artikel in dieser Serie</h3>
  <ul>
    <li><a href="../rki-daten-laden-python">Teil 1: Corona-Daten des RKI mit Python herunterladen</a></li>
    <li><a href="../rki-daten-visualisieren-python">Teil 2: Corona-Daten des RKI mit Python visualisieren</a></li>
    <li><a href="../rki-daten-telegram-senden-python">Teil 3: Corona-Daten des RKI mit Python an Telegram-Kanal senden</a></li>
  </ul>
</article>

### Telegram Bot erstellen

Im Gegensatz zu anderen Messengern ist es bei [Telegram](/tags/telegram) überaus leicht, eigene Bots zu erstellen. Der *BotFather* kümmert sich um die technischen Details, ihr müsst ihm lediglich mitteilen wie eurer Bot heißen soll. Beginnt also zunächst einen Chat mit dem Account [`@BotFather`](https://t.me/botfather) - ihr findet ihn ganz einfach über die Suche. 

<figure>
  <img alt="Beginne einen Chart mit dem BotFather" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215171&authkey=%21AG5Vlsu4bjIvLx4&width=660"/>
  <figcaption>Beginne einen Chart mit dem BotFather</figcaption>
</figure>

Mit dem Befehl `/newbot` kündigt ihr dem *BotFather* an, dass ihr einen neuen Bot erstellen wollt. Er fragt euch daraufhin, wie euer Bot heißen soll - einmal sprechend, einmal technisch. Der technische Name muss auf `bot` enden. Wir könnten den Bot also beispielsweise "CoronaBotStuttgart" und "CoronaBotStuttgart_bot" nennen. 

<figure>
  <img alt="Erstelle deinen neuen Bot" src="https://onedrive.live.com/embed?resid=273EB2087BC33FC5%215173&authkey=%21AC-SO1Kg09m_Es8&height=660"/>
  <figcaption>Erstelle deinen neuen Bot</figcaption>
</figure>

Der *BotFather* nennt euch daraufhin euren **API access token**. Dieser ist quasi Benutzername und Passwort für euren Bot in einem. **Diese Information solltet ihr also unter keinen Umständen aus den Händen geben**. Ihr braucht diesen Token gleich, um den Bot von Python aus zu steuern. 

### Telegram Bot einem Kanal hinzufügen

Jetzt solltet ihr einen neuen Kanal erstellen und euren Bot als Admin hinzufügen. Dazu geht ihr wie folgt vor:

1. In Telegram klickt auf "New Channel"
1. Gebt eurem Kanal einen Namen
1. Entscheidet ob der Kanal öffentlich oder privat sein soll und unter welcher URL er erreichbar sein soll
1. Fügt euren Bot dem Kanal hinzu (sucht nach dem "sprechenden Namen")

Der Kanal ist nun erstellt. 
1. Öffnet ihn und klickt oben auf den Namen und wählt "Aministrators" aus. 
1. *Befördert* euren Bot zum Admin des Kanals. Er braucht lediglich das Recht Nachrichten senden zu können ("Post Messages"). Alle anderen Berechtigungen könnt ihr ihm (wenn ihr wollt) entziehen. 

Anschließend könnt ihr eine *"Hello World"*-Nachricht in den Kanal schreiben. 

### ChatID herausfinden

Jetzt müssen wir die ChatID eures Kanals herausfinden. Diese benötigen wir (zusammen mit dem access token), um später Nachrichten im Namen eures Bots versenden zu können. 

Die ChatID erhaltet ihr, wenn ihr folgende URL öffnet: `https://api.telegram.org/bot<access_token>/getUpdates`. Natürlich müsst ihr `<access_token>` mit eurem Token ersetzen. Wichtig ist, dass `/bot` vor dem Token in der URL erhalten bleibt. Die Ausgabe sollte ungefähr so aussehen: 

```
{"ok":true,"result":[{"update_id":773467236,
"message":{"message_id":550,"from":{"id":343487567,"is_bot":false,"first_name":"Bat","last_name":"Man","username":"wayne123","language_code":"de-DE"}
```

Die ChatID verbirgt sich in der Nachricht unter `"id"`. Im obigen Fall also `"id":343487567`. Speichert euch die ChatID ebenfalls sicher ab. 

### Nachrichten an Telegram-Kanal senden

Nun schreiben wir uns eine Funktion, die mit Hilfe des Access Tokens und der ChatID einen bestimmten Inhalt sendet. Wir übergeben der Funktion drei Parameter: 

- `chatid`: Wenn ihr mal was testen wollt schadet es nicht, wenn der Bot auch in einen anderen Chat posten kann.
- `message_type`: Hiermit legen wir fest, ob eine Textnachricht oder ein Bild/Foto versendet werden soll. Je nachdem unterscheidet sich der API-Aufruf. 
- `message_content`: Übergibt den Inhalt der Nachricht (Bild oder Text) an die Funktion.
- Den Access Token, den ihr vom *BotFather* erhalten habt, tragt ihr in der ersten Zeile ein. 

{{< highlight python >}}
def telegram_bot_send(chatid, message_type, message_content):
    # define access token (received from BotFather)
    bot_token = '<access_token>'

    # translate message_type in API URL
    if mtype == 'text':
        bot_mtype = '/sendMessage'
    elif mtype == 'photo':
        bot_mtype = '/sendPhoto'

    # concatenate API URL
    bot_url = 'https://api.telegram.org/bot' + bot_token + bot_mtype + '?chat_id=' + chatid

    # send message
    if mtype == 'text':
        response = requests.get(bot_url + '&parse_mode=Markdown&text=' + content)
    elif mtype == 'photo':
        response = requests.post(bot_url, files={'photo': open(content, 'rb')})
    
    return response.json()
{{< /highlight >}}

Damit können wir nun Inhalte über unseren Bot in den entsprechenden Telegram-Kanal senden. Den [zuvor erzeugten Plot](/rki-daten-visualisieren-python) sowie eine kleine Textnachricht können wir beispielsweise mit den folgenden Befehlen absenden. Der Text muss nicht statisch sein und kann bspw. auch dynamisch aus den Daten erzeugt werden.

{{< highlight python >}}
# send messages to Telegram channel
telegram_bot_send(
    chatid = '343487567'
    message_type = 'photo'
    message_content = 'link/to/exported/plot.png'
)
telegram_bot_send(
    chatid = '343487567'
    message_type = 'text'
    message_content = 'Die Indizenz in Stuttgart ist immer noch sehr hoch'
)
{{< /highlight >}}

Damit sind wir am Ende dieser kleinen Serie angekommen. Ich hoffe ihr könnt daraus was für eure Projekte ableiten. [Schreibt mir gern](/contact), wenn es euch weitergeholfen hat oder Probleme auftreten. 