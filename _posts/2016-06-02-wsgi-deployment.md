---
layout: post
title:  "Python, WSGI und Betrieb mit dem Apache Webserver"
date:   2016-06-2 17:44:51 +0200
categories: lehre
tags: python, apache, wsgi
excerpt_separator: <!--more-->
---

Durch die Entwicklung ihrer Webapplikation in ihrer lokalen Umgebung kommt es
häufig zu Problemen beim Deployment auf dem WSGI-fähigen Webserver. Hauptursache
sind fehlende oder fehlerhaft gesetzte Datei- und Ordnerberechtigungen.
Dieser Post beschäftigt sich mit der notwendigen Konfiguration und enthält
Implementierungshinweise für den reibungslosen Betrieb einer WSGI-Applikation
auf dem Apache Webserver.
<!--more-->

## Berechtigungen
Damit der Webserver Ihre WSGI-Applikation korrekt ausführen kann benötigt
er Lesezugriff auf alle Dateien und Ordner die dur Applikation gehören.

*Der Webserver* bezeichnet dabei die Applikation *Apache Httpd* auf dem Host
*www* (Das www in www.mi.hs-rm.de). Die Applikation wird unter einem eigenen
Unix-Benutzer ausgeführt und hat deshalb zunächst keinen Zugriff auf die Dateien
in den `public_html`-Ordnern der einzelnen Nutzer.
Die Rechte müssen zunächst gewährt werden.

Für WSGI-Applikationen müssen alle Verzeichnisse und Dateien auf dem Weg zum
Applikations-Verzeichnis mindestens lesbar sein (`chmod o+r`).

## Aufbau von URIs/URLs
Relevante Disskusionen um URI, URL, URN auf Stackoverflow:

  - <http://stackoverflow.com/q/176264>

Mit den wichtigen Antworten:

  - <http://stackoverflow.com/a/176274>
  - <http://stackoverflow.com/a/1984225>

Eine URI ist wie folgt aufgebaut:

```
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
```

- Scheme: `http`, `https`, `ftp` ...
- Host z.B.: `www.mi.hs-rm.de`
- Port z.B.: `80`
- Path z.B.: `~/username/WSGI/app.wsgi/kunden`
- Query z.B.: `?page=1&orderby=name`
- Fragment: wird vom User-Agent clientseitig ausgewertet, HTML: Anchor auf Seite

## Relative Pfade

| Pfadanfang | Bedeutung                                               |
|:-----------|:--------------------------------------------------------|
| `/`        | Relativ von Wurzel ausgehend                            |
| `./`       | Relativ zu aktuellem Verzeichnis                        |
| `../`      | Relativ zu übergeordnetem Verzeichnis                   |

### Beispiele

| URL | href | Ziel |
|:----|:-----|:------------|
| http://www.mi.hs-rm.de/~user/WSGI/app.wsgi/ | /kunde             | http://www.mi.hs-rm.de/kunde |
| http://www.mi.hs-rm.de/~user/WSGI/app.wsgi/ | ./kunde            | http://www.mi.hs-rm.de/~user/WSGI/app.wsgi/kunde |
| http://www.mi.hs-rm.de/~user/WSGI/app.wsgi/ | ../kunde           | http://www.mi.hs-rm.de/~user/WSGI/kunde |
| http://localhost:8080/                      | /kunde             | http://localhost:8080/kunde |
| http://localhost:8080/                      | ./kunde            | http://localhost:8080/kunde |
| http://localhost:8080/                      | ../kunde           | http://localhost:8080/kunde |

## URLs und Dateipfade im Backend
Häufig werden Module und Dateien nicht gefunden, weil der Webserver die
Applikation nicht in ihrem jeweiligen Verzeichnis, sondern in seinem Home-
Verzeichnis startet. Ihre Applikation wird allerdings in der Regel relative
Pfade, ausgehend vom eigenen Wurzelverzeichnis verwenden.

Damit die relativen Pfade weiterverwendet werden können, muss zunächst beim
Start der WSGI-Applikation dafür gesorgt werden, dass das Wurzelverzeichnis
im System-Pfad zu finden ist und auch als aktuelles Arbeitsverzeichnis genutzt
wird. Die Änderung des Systempfads sorgt dafür, dass der Python-Interpreter
des Webservers ihre Module finden kann. Die Änderung des aktuellen Arbeits-
verzeichnisses sorgt dafür, dass Sie selbst innerhalb Ihrer Module mit relativen
Pfadangaben auf Datein verweisen können (zum Beispiel `'./Templates/'`).

Im Folgenden sind zwei Dateien gezeigt, über die Ihre Applikation gestartet
werden kann. Die Datei `app.wsgi` ist dabei lediglich als Einstiegspunkt für
den Webserver vorgesehen. Hier wird keinerlei weitere Applikationslogik auf-
gerufen oder implementiert.

Die Datei `app.py` enthält den eigentlichen Einstiegspunkt in Ihre Applikation
(die Implementierung der `application`-Funktion) sowie einen Block zum starten
eines lokalen Webservers für die Entwicklung.

### app.wsgi

```python
#/usr/bin/env python
#-*- coding:utf-8 -*-
import os
import sys

root_directory = os.path.dirname(__file__)
# Interpreter des Webservers muss Module finden können
sys.path.append(root_directory)
# Eigene Module sollen relative Pfade verwenden können
# z.B.: file('./templates/base.html').read()
os.chdir(root_directory)

from app import application
```

In der app.wsgi sollte kein Code stehen, der nicht für die Initialisierung der
Applikation im WSGI-Kontext des Webservers notwendig ist. Das gezeigte
Beispiel initialisiert lediglich die notwendingen Pfade und importiert danach
die Funktion `application` aus dem Modul `app`.

### app.py
```python
#/usr/bin/env python
#-*- coding:utf-8 -*-
import ...
# ...
@Request.application
def application(request):
''' Einstiegspunkt in eigentliche Applikation'''
    return Response("Hello World")
#...
if __name__ == '__main__':
''' startet app mit lokalem Webserver zur Entwicklung '''
   from werkzeug.serving import run_simple
   run_simple('localhost', 4000, application, use_reloader=True)
```

Das `app`-Modul implementiert den Einstieg in die eigentliche Applikationslogik.
Zustäzlich ist ein Main-Block vorgesehen, um die Applikation lokal mit einem
eigenen Webserver auszuführen.

## URLs im Frontend
Wenn Sie ihre Software lokal testen, werden Sie in der Regel Pfade
verwenden, die folgendem Muster ähneln:

- `http://localhost:8080/<route>`

Hier können sie scheinbar problemlos mit Pfaden, relativ zur Wurzel
in Ihren Anchor-Tags hantieren (z.B. `<a href="/kunden" > ...`).
Lassen Sie sich davon bitte nicht täuschen. Das führt auf dem Webserver direkt
zu folgendem Problem:

Die Basis-URL ihrer Applikation auf dem Webserver lautet
`http://www.mi.hs-rm.de/~username/WSGI/app.wsgi`. Der relative Link zu `/kunden`
unterhalb der Wurzel wird im Browser dann auf folgende URL verweisen:
`http://www.mi.hs-rm.de/kunden`

Diese URL ist *NICHT* mehr Teil ihrer Applikation.

Um ihre Applikation korrekt verwenden zu können sollten Sie URLs relativ zur
Wurzel vermeiden. Bei Einsatz von relativen URLs sollten diese immer
relativ zum aktuellen Verzeichnis sein.

Falls das weiterhin problematisch ist, sollten Sie in Betracht ziehen,
aussschließlich absolute URLs zu erzeugen. Dazu können Sie das Property
`url_root`auf Werkzeugs Request-Objekt verwenden.

``` python
def render(request):
    ''' generiert eine neue seite für den request '''
    template = file('./templates/navigation.html').read()
    customer_link = request.url_root + 'kunden'
    return template % customer_link
```

# tl;dr
- Sorgen Sie mit `chmod o+r` dafür, dass Dateien für den Webserver lesbar sind.
- Nutzen Sie die `.wsgi`-Datei dazu, den Webserver seine Arbeit im richtigen
  Verzeichnis verrichten zu lassen (`os.chhdir`) und ihre Module auffindbar zu
  machen (`sys.path.append`)
- generieren Sie absolute URLs im Frontend, wenn Sie zu viele Probleme mit
  relativen URLs haben.

### Siehe auch
- [Werkzeug BaseRequest.url_root](http://werkzeug.pocoo.org/docs/0.11/wrappers/#werkzeug.wrappers.BaseRequest.url_root)
- [Apache Web Server](https://httpd.apache.org/)
- [Python WSGI](https://www.python.org/dev/peps/pep-0333/)

---
Sollten Sie Fehler finden, senden Sie mir bitte eine E-Mail.
