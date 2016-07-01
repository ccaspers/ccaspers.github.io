---
layout: post
title:  "Q&A - JSF Sessions"
date:   2016-07-01 16:10:00 +0200
categories: lehre
tags: java, jsf
excerpt_separator: <!--more-->
---

> Hallo Christian,  
> (...)  
> Wie verwendet man denn in JSF sessions? Ich versteh das nicht so ganz.
> Wir sollen ja keine Servlets verwenden, also ist HttpSession ja raus.
>
> Gibt es da eine Alternative?

Jein. JSF verwendet ebenfalls HTTP-Sessions, allerdings versteckt unter der Abstraktionsschicht namens SessionScope.

<!--more-->

Folgende Scopes sind von zentraler Bedeutung:

- ApplicationScope  
  Kann mit Attributen befüllt werden, die zur Laufzeit der Applikation für alle User zugänglich und identisch sein sollen.
- SessionsScope
  Kann mit Attributen befüllt werden, die für Dauer der Sitzung eines Nutzers vorhanden sein sollen.
- RequestScope  
  Kann mit Attributen befüllt werden, die nur während eines laufenden HTTP-Requests vorhanden sein sollen.

Es existieren weitere Scopes, auf die hier nicht weiter eingegangen wird.

Um eine Bean automatisch durch das Framework instanziieren zu lassen und eine Referenz in die Session des Users zu legen, können Sie sie wie folgt auszeichnen:

```` java
@ManagedBead
@SessionScoped
public class MyBean{
  /* ... */
}
````

Danach ist sie über EL-Ausdrücke automatisch verfügbar. Der Ausdruck `#{myBean.property}` sucht zunächst nach einem Attribut namens *myBean* im RequestScope, danach im SessionScope und zuletzt im ApplicationScope. (Siehe auch: http://docs.oracle.com/javaee/6/tutorial/doc/bnahu.html#bnahw).

Sobald ein Attribut mit dem Namen gefunden wurde, wird die Methode `getProperty`des Attributs gesucht, und bei Erfolg ausgeführt.

Da die Bean per Annotation dem SessionScope zugewiesen wurde, wird Sie genau einmal erzeugt, falls sie noch nicht in der Session vorhanden ist. Die Instanz bleibt dann für die gesamte Dauer der Session identisch.

Der Zugriff auf die Session per Java-Code ist ein wenig umständlich:

```java
FacesContext context = FacesContext.getCurrentInstance();
Map<String, Object> sessionMap = context.getExternalContext().getSessionMap();
```

So kann auf Attribute der Session lesend und schreiben zugegriffen werden, nicht vorhandene Attribute können manuell in die Session gelegt werden.

**Nicht empfohlen** aber dennoch möglich ist folgender Aufruf:

``` java
FacesContext context = FacesContext.getCurrentInstance();
HttpSession session = (HttpSession)context.getExternalContext().getSession(true);
```

Nachtrag:
Es ist ebenfalls möglich, mittels EL explizit auf Attribute einzelner Scopes zuzugreifen. Ein Beispiel:
```
#{sessionScope.myProperty}
```
Sucht das Attribut mit dem Schlüssel "myProperty" im SessionScope. Sollten sie versuchen, eine ManagedBean mit SessionScope auf diese Weise zu referenzieren, wird das nicht funktionieren, da die so **nicht** automatisch instanziiert wird. 
