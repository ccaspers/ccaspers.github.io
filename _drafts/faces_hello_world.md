---
layout: post
title:  "Hello World mit JSF"
date:   2016-06-2 17:44:51 +0200
categories: lehre
tags: java, jsf
excerpt_separator: <!--more-->
---
Dies das, was ist Faces kurze Einleitung.
<!-- more -->
## Vorraussetzungen
- Java 7+
- (Eclipse 4.5.x)[https://www.eclipse.org/downloads/download.php?file=/oomph/epp/mars/R2/eclipse-inst-mac64.tar.gz]
- (Tomcat 8)[https://tomcat.apache.org/download-80.cgi]

# Apache Tomcat
Apache Tomcat ist Java-basierte Webserver und Servletcontainer. Er wird ihre Webapplikaton beherbergen und ausliefern.

## In Eclipse konfigurieren
Damit Sie ihre Applikation über die IDE zum Testen in einen lokalen Container kopieren können muss dieser zunächst konfiguert werden. Dazu wird das heruntergeladen Archiv (Tomcat 8) entpackt und an Ort *außerhalb* ihres Eclipse-Workspaces verschoben. Der Container hat nichts in der Nähe ihrer Entwicklungsprojekte verloren. Er ist einfach ein Stück Software zur auslieferung ihre Webapplikaton.

# Java Server Faces
Java Server Faces ist ein Framework zur Entwicklung von Webapplikationen und deckt alle Grundbedürfnisse ab.
Zur Entwicklung des Frontends wird eine Tag-Bibliothek angeboten. Es gibt *Scopes* zur Verwaltung und Strukturierung des Zustands der Applikation und des Nutzers (*Sessions*). Die Entwcklung des Backends erfolgt über einfache Java-Objekte (Beans).

## Tier2 Architektur
## Faces Projekt anlegen
- Eclipse, new Dynamics Web Project
- Faces Jar in Client libs
- TODO
## WEB-INF
## web.xml
## faces-config.xml
## FacesServlet

# Links
- Tomcat download
- Eclipse download
- Hello World Projekt auf Github
