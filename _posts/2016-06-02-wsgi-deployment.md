---
layout: post
title:  "Python wsgi-applications and Apache!"
date:   2016-04-15 18:44:51 +0200
categories: python lehre wsgi
---
# Deployment Best-Practices
Das Deployment auf dem Webserver führt immer wieder zu Problemen. Die Ursachen
sind in der Regel auf fehlerhaften Umgang mit Pfaden und falsch gesetzte
Berechtigungen zurückzuführen.

## Berechtigungen
Damit der Webserver Ihre wsgi-Applikation korrekt ausführen kann benötigt
er Lesezugriff auf alle Dateien und Ordner die dur Applikation gehören.

*Der Webserver* bezeichnet dabei die Applikation *Apache Httpd* auf dem Host
*www*. Die Applikation wird unter einem eigenen Unix-Benutzer ausgeführt und hat
deshalb zunächst keinen Zugriff auf die Dateien in den `public_html`-Ordnern der
einzelnen Nutzer. Die Rechte müssen zunächste gewährt werden.

Für wsgi-Applikation müssen alle Verzeichnisse und Dateien auf dem Weg zum
Applikations-Verzeichnis lesbar sein (`chmod o+r`).


## Relative Pfade
| Pfadanfang | Bedeutung                                               |
|------------|---------------------------------------------------------|
| `/`        | Relativ von Wurzel ausgehend                            |
| `./`       | Relativ zu aktuellem Verzeichnis                        |
| `../`      | Relativ zu übergeordnetem Verzeichnis                   |

### Beispiele
| URL | href | Ziel |
|-----|------|-------------|
| http://www.mi.hs-rm.de/~user/wsgi/app.wsgi/ | /kunde             | http://www.mi.hs-rm.de/kunde |
| http://www.mi.hs-rm.de/~user/wsgi/app.wsgi/ | ./kunde            | http://www.mi.hs-rm.de/~user/wsgi/app.wsgi/kunde |
| http://www.mi.hs-rm.de/~user/wsgi/app.wsgi/ | ../kunde           | http://www.mi.hs-rm.de/~user/wsgi/kunde |
| http://localhost:8080/                      | /kunde             | http://localhost:8080/kunde |
| http://localhost:8080/                      | ./kunde            | http://localhost:8080/kunde |
| http://localhost:8080/                      | ../kunde           | http://localhost:8080/kunde |

## Pfade serverseitig
Häufig werden Module und Dateien nicht gefunden, weil der Webserver die
Programme nicht in ihrem jeweiligen Verzeichniss, sondern in seinem Home-
Verzeichnis startet. Ihre Software wird allerdings in der Regel relative
Pfade, ausgehend vom Wurzelverzeichnis Ihrer Applikation verwenden.

Damit die relativen Pfade weiterverwendet werden können, muss zunächst beim
start der wsgi-Applikation dafür gesorgt werden, dass das Wurzelverzeichnis
im System-Pfad zu finden ist und auch als aktuelles Arbeitsverzeichnis genutzt
wird. Die Änderung des Systempfads sorgt dafür, dass der Python-Interpreter
des Webservers ihre Module finden kann. Die Änderung des aktuellen Arbeits-
verzeichnisses sorgt dafür, dass Sie innerhalb Ihrer Module mit relativen
Pfadangaben auf Datein verweisen können (zum Beispiel Templates).

Im folgenden sind zwei Dateien gezeigt über die ihre Applikation gestartet
werden kann. Die Datei `app.wsgi` ist dabei lediglich als Einstiegspunkt für
den Webserver vorgesehen. Hier wird keinerlei weitere Applikationslogik auf-
gerufen oder implementiert.

Die Datei `app.py` enthält den eigentlichen Einstiegspunkt in Ihre Applikation
(die Implementierung der `application`-Funtkion) sowie einen Block zum starten
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
Applikation im wsgi-Kontext des Webservers notwendig ist. Das gezeigte
Beispiel initialisiert lediglich die notwendingen Pfade und import danach
die `application`-Methode aus dem `app`-Modul.

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
Zustäzlich ist ein Main-Block vorgesehen um die Applikation lokal mit einem
eigenen Webserver auszuführen.

## Aufbau von UR[ILN]s
Relevante Disskusionen um URI, URL, URN auf Stackoverflow:
  - http://stackoverflow.com/q/176264

Mit den wichtigen Antworten:
  - http://stackoverflow.com/a/176274
  - http://stackoverflow.com/a/1984225

Eine URI ist wie folgt aufgebaut:

```
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
```

- scheme: http, https, ftp ...
- z.B www.mi.hs-rm.de
- Port z.B.: 80
- Path z.B.: ~/username/wsgi/app.wsgi/kunden
- query z.B.: page=1&orderby=name&order=asc
- fragment: wird vom User-Agent clientseitig ausgewertet, HTML: Anchor auf Seite

## Pfade clientseitig
Wenn Sie ihre Software lokal ausgeführt haben, werden Sie in der Regel Pfade
nach folgendem Schema verwendet haben:

- `http://localhost:8080/<route>`

Hier können sie scheinbar Problemlos mit Pfaden, relativ zur Seitenwurzel
in ihren Anchor-Tags hantieren (z.B. `<a href="/kunden" > ...`).
Lassen Sie sich davon bitte nicht täuschen. Das führt auf dem Webserver direkt
zu folgendem Problem:

Die Basis-URL ihrer Applikation auf dem Webserver lautet
`http://www.mi.hs-rm.de/~username/wsgi/app.wsgi`. Der relative Link zu `/kunden`
unterhalb der Seitenwurzel wird im Browser dann auf folgende URL verweisen:
`http://www.mi.hs-rm.de/kunden`

Diese URL ist *NICHT* mehr Teil ihrer Applikation.

Um ihre Applikation korrekt verwenden zu können sollten Sie URLs relativ zur
Seitenwurzel vermeiden. Bei Einsatz von relativen URLs sollten diese immer
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
