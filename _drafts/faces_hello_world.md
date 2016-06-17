---
layout: post
title:  "Hello World mit JSF"
date:   2016-06-2 17:44:51 +0200
categories: lehre
tags: java, jsf
excerpt_separator: <!--more-->
---
JavaServer Faces ist ein Framework zur Entwicklung Java-basierter Webanwendungen, die innerhalb eines Java Applikations Servers ausgeführt werden. Im folgenden wird ein einfaches JSF-Projekt in Eclipse angelegt und über Apache Tomcat deployt um einen leichten Einstieg in die Entwicklung von JSF-Applikationen zu ermöglichen.

<!-- more -->

## Vorraussetzungen
- Java 7+
- Eclipse 4.5.x
- [Tomcat 8](https://tomcat.apache.org/download-80.cgi)

# Apache Tomcat
Apache Tomcat ist Java-basierte Webserver und Servletcontainer. Er wird ihre Webapplikaton beherbergen und ausliefern.

## In Eclipse konfigurieren
Damit Sie ihre Applikation über die IDE zum Testen in einen lokalen Container kopieren können muss dieser zunächst konfiguert werden. Dazu wird das heruntergeladen Archiv (Tomcat 8) entpackt und an Ort *außerhalb* ihres Eclipse-Workspaces verschoben (z.B. `C:\tools`, `/opt` oder `/usr/local`). Der Container hat nichts in der Nähe ihrer Entwicklungsprojekte verloren.

Im nächsten Schritt wird der Container für das Deployment aus Eclipse eingerichtet.
1. Neuen Server erzeugen:

  ![Neuen Server Erzeugen](/assets/jsftutorial/01_tomcat_new_server.png)

2. Tomcat 8.0 wählen

  ![Apache > Tomcat v8.0 Server](/assets/jsftutorial/02_tomcat_80.png)

3. Pfad einstellen. Hier wird den Pfad gewält, an den den das Tomcat-Archiv    entpackt wurde.

  ![Neuen Server Erzeugen](/assets/jsftutorial/03_tomcat_config.png)  

# Java Server Faces
Wie bereits angesprochen, handelt es sich bei JavaServer Faces um ein Framework. Es enthält eine Reihe sinnvoller Abstraktionen zur Erfüllung üblicher Anforderungen an Webapplikatonen und definiert zudem einen Lebenszyklus für die die Kommunikation mit dem Nutzer über Request-Response-getriebene Protokolle. Bei der Entwicklung von JSF standen die folgenden Aspekte im Vordergrund:

>JSF’s core architecture is designed to be **independent of specific protocols and markup**. However it is also aimed directly
at **solving many of the common problems** encountered when writing applications **for HTML clients that communicate via
HTTP** to a Java application server that supports servlets and JavaServer Pages (JSP) based applications. These
applications are typically **form-based**, and are comprised of **one or more HTML pages** with which the **user interacts to
complete a task or set of tasks**.
>
> Auszug aus *JSR-334 - Chapter 1.1 Solving Practical Problems of the Web*

JSR-334 ist die Spezifikation für JSF, [frei verfügbar](https://jcp.org/aboutJava/communityprocess/final/jsr344/index.html)  und wird von mehreren Anbietern implementiert. Die Referenz-Implementierung ist Mojarra und wird von Oracle gepflegt.

Zu den Kernbestandteilen des Frameworks gehören
- der Request-Verarbeitungs-Lebenszyklus
- definierte Standard-UI-Komponenten
- Integriertes Session-Management
- XML-Dialekt zur Interface-Erzeugung
- DSL zur Datenbindung zwischen XML und Java-Beans (Expression Language)

## Faces Projekt anlegen
1. File > New Project

  ![Neues Projekt](/assets/jsftutorial/01_new_project.png)  

2. Tomcat als Webserver wählen

  ![Neues Projekt](/assets/jsftutorial/02_faces_project.png)  

3. Source Folder auswählen (keine Änderung notwendig)

  ![Neues Projekt](/assets/jsftutorial/03_faces_src_folder.png)  

4. Projekt mit Deployment Descriptor erzeugen

  ![Neues Projekt](/assets/jsftutorial/04_faces_project.png)  

5. Bibliotheken laden TODO
## WEB-INF
## web.xml
## faces-config.xml
## FacesServlet

# Links
- Tomcat download
- Eclipse download
- Hello World Projekt auf Github
