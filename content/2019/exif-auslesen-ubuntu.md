---
title: "EXIF Daten unter Ubuntu auslesen"
date: "2019-07-10"
author: "soeren"
tags: 
  - ubuntu
post: true
draft: false
---

Heute nur ganz kurz, weil ich es gerade gesucht, [gefunden](https://mikebeach.org/2011/12/17/how-to-retrieve-exif-data-from-the-command-line-in-ubuntu-linux/) und vermutlich morgen wieder vergessen habe: Mit Hilfe von `imagemagick` könnt ihr unter [Ubuntu](/tags/ubuntu/) die exif-Daten eurer [Fotos](/tags/fotographie) auslesen.

<!--more-->

### `imagemagick` installieren

```
batman@helium:~$ sudo apt-get install imagemagick
```

### mit `identify` die Meta-Daten auslesen

{{< highlight sh >}}
batman@helium:~$ identify -verbose link/to/image.jpg

Image: image.jpg
  Format: JPEG (Joint Photographic Experts Group JFIF format)
  Mime type: image/jpeg
  Class: DirectClass
  Geometry: 458x458+0+0
  Resolution: 72x72
  Print size: 6.36111x6.36111
  Units: PixelsPerInch
  Colorspace: sRGB
  Type: TrueColor
  Base type: Undefined
  Endianess: Undefined
  Depth: 8-bit
  Channel depth:
    red: 8-bit
    green: 8-bit
    blue: 8-bit
  ...
{{< /highlight >}}

### Output auf die exif-Informationen einschränken

Wenn ihr beispielsweise nur an den exif-Informationen interessiert seid, könnt ihr die Ausgabe mit `grep` eingrenzen. 

```
batman@helium:~$ identify -verbose link/to/image.jpg | grep "exif:"

    exif:ApertureValue: 177537/78038
    exif:BrightnessValue: 128447/18170
    exif:ColorSpace: 1
    exif:ComponentsConfiguration: 1, 2, 3, 0
    exif:ExifImageLength: 458
    exif:ExifImageWidth: 458
    exif:ExifOffset: 170
    exif:ExifVersion: 48, 50, 50, 49
    exif:ExposureBiasValue: 0/1
    exif:ExposureMode: 0
    ...
```

### Geo-Koordinaten auslesen

Und wenn euch beispielsweise nur die Geo-Koordinaten im Foto interessieren, könnt ihr natürlich auch danach suchen. 

```
batman@helium:~$ identify -verbose link/to/image.jpg | grep "exif:GPS"

    exif:GPSAltitude: 125791/8327
    exif:GPSAltitudeRef: 0
    exif:GPSDateStamp: 2018:10:26
    exif:GPSDestBearing: 268263/3437
    exif:GPSDestBearingRef: T
    exif:GPSImgDirection: 611324/2369
    exif:GPSImgDirectionRef: T
    exif:GPSInfo: 1670
    exif:GPSLatitude: 35/1, 43/1, 5997/100
    exif:GPSLatitudeRef: N
    exif:GPSLongitude: 139/1, 44/1, 4874/100
    ...
```

### With Great Power ...

Ich habe das heute gebraucht, weil mir ein Foto auf verschiedenen Betriebssystemen in unterschiedlichen Orientierungen angezeigt wurde (unter Windows richtig, unter Ubuntu auf dem Kopf) und ich herausfinden wollte was das Foto eigentlich dazu sagt (Spoiler: <span class="spoiler">natürlich hatte Ubuntu Recht :smile:</span>).

Es hat mir wieder einmal vor Augen geführt, wie schnell man persönliche Informationen preis gibt. Ein Foto mit dem Handy daheim aufgenommen und auf Facebook geladen? Dann brauchst du eigentlich auch keine Adresse mehr hinterlegen - wo du wohnst ist dann eh klar.