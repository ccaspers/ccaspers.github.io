---
layout: post
title:  "Ein JSF-Projekt konfigurieren"
date:   2016-06-25 12:00:00 +0200
categories: lehre
tags: java, jsf
excerpt_separator: <!--more-->
---

JavaServer Faces ist ein UI-Framework zur Entwicklung Java-basierter Webanwendungen, die innerhalb eines Java Applikations Servers ausgeführt werden. Im folgenden wird ein einfaches JSF-Projekt in Eclipse angelegt und über Apache Tomcat deployt um einen leichten Einstieg in die Entwicklung von JSF-Applikationen zu ermöglichen.
<!--more-->

# Vorraussetzungen
- Java 7+
- Eclipse 4.5.x
- [Tomcat 8](https://tomcat.apache.org/download-80.cgi)

## Apache Tomcat
Apache Tomcat ist Java-basierte Webserver und Servletcontainer. Er wird ihre Webapplikaton ausliefern.

### In Eclipse konfigurieren
Damit Sie ihre Applikation über die IDE zum Testen in einen lokalen Container deployen können muss dieser zunächst konfiguert werden. Dazu wird das heruntergeladen Archiv (Tomcat 8) entpackt und an einen Ort *außerhalb* Ihres Eclipse-Workspaces verschoben (z.B. `C:\tools`, `/opt` oder `/usr/local`).

Im nächsten Schritt wird der Container für das Deployment aus Eclipse eingerichtet.

1. Neuen Server erzeugen  
  ![Neuen Server Erzeugen](/assets/jsftutorial/tomcat_01_new_server.png)

2. Tomcat 8.0 wählen  
  ![Apache > Tomcat v8.0 Server](/assets/jsftutorial/tomcat_02_80.png)

3. Pfad einstellen. Hier wird der Pfad gewält, an den den das Tomcat-Archiv entpackt wurde.  
  ![Neuen Server Erzeugen](/assets/jsftutorial/tomcat_03_config.png)  

# Java Server Faces
Wie bereits angesprochen, handelt es sich bei JavaServer Faces um ein Framework. Es enthält eine Reihe sinnvoller Abstraktionen zur Erfüllung üblicher Anforderungen an Webapplikatonen und strukturiert die Kommunikation mit dem Nutzer über Request-Response-getriebene Protokolle. Bei der Entwicklung von JSF standen die folgenden Aspekte im Vordergrund:

>JSF’s core architecture is designed to be **independent of specific protocols and markup**. However it is also aimed directly
at **solving many of the common problems** encountered when writing applications **for HTML clients that communicate via
HTTP** to a Java application server that supports servlets and JavaServer Pages (JSP) based applications. These
applications are typically **form-based**, and are comprised of **one or more HTML pages** with which the **user interacts to
complete a task or set of tasks**.
>
> Auszug aus *JSR-334 - Chapter 1.1 Solving Practical Problems of the Web*

JSR-334 ist die Spezifikation für JSF, [frei verfügbar](https://jcp.org/aboutJava/communityprocess/final/jsr344/index.html) und wird von mehreren Anbietern implementiert. Die Referenz-Implementierung heißt Mojarra und wird von Oracle gepflegt.

Zu den Kernbestandteilen des Frameworks gehören

- der Request-Verarbeitungs-Lebenszyklus
- definierte Standard-UI-Komponenten
- Integriertes Session-Management
- XML-Dialekt zur Interface-Erzeugung
- DSL zur Datenbindung zwischen XML und Java-Beans (Expression Language)

# Faces Projekt anlegen
Um ein korrekt konfiguriertes JSF-Projekt zu erzeugen gehen so folgedermaßen vor

1. File > New Project  
  ![Neues Projekt](/assets/jsftutorial/faces_01_new_project.png)

2. Tomcat als Webserver wählen, JSF 2.2 einstellen  
  ![Tomcat](/assets/jsftutorial/faces_02_faces_project.png)  

3. Source Folder auswählen (keine Änderung notwendig)  
  ![Source Folder](/assets/jsftutorial/faces_03_source_folders.png)  

4. Projekt mit Deployment Descriptor erzeugen  
  ![Generate Deployment Descriptor](/assets/jsftutorial/faces_04_web-xml.png)  

5. JSF konfigurieren,
  ![JSF Konfiguration](/assets/jsftutorial/faces_05_faces_capabilities.png)

6. Bibliotheken herunterladen  
  - [JSF - Mojarra 2.2.11](https://maven.java.net/content/repositories/releases/org/glassfish/javax.faces/2.2.11/javax.faces-2.2.11.jar)
  - [JSTL API 1.2.1](http://search.maven.org/remotecontent?filepath=javax/servlet/jsp/jstl/javax.servlet.jsp.jstl-api/1.2.1/javax.servlet.jsp.jstl-api-1.2.1.jar)
  - [JSTL-Implementierung 1.2.4](http://search.maven.org/remotecontent?filepath=org/glassfish/web/javax.servlet.jsp.jstl/1.2.4/javax.servlet.jsp.jstl-1.2.4.jar)

7. Bibliotheken in Projekt kopieren  
   Die zuvor heruntergeladenen JAR-Dateien müssen nun in den Ordner `WebContent/WEB-INF/lib` kopiert und unter Umständen manuell zum Buildpath hinzugefügt werden.  
  ![JSF Konfiguration](/assets/jsftutorial/faces_06_libraries.png)

Auf [Github](https://github.com/ccaspers/HelloFaces) finden sie ein vollständiges Beispielprojekt inklusive kurzer Anleitung zur Konfiguration.
