# Umsetzungsalternativen für eine interne Suche

## Ziel:
Ziel ist mindestens eine Möglichkeit zur Suche in den Mailman Archiven aller [Mailingklisten](https://internal.innoq.com/mailman/listinfo) zu entwickeln.
Wünschenswert wäre natürlich wenn möglichst alle innoQ Kommunikationskanäle(iBlog, Confluencs, Mails, etc.?) durchsuchbar wären. Noch besser wäre es wohl wenn auch sämtliche Laufwerke, inklusive Code-Repos durchsuchbar wären. Und das ganze Projekt sollte keine Jahre dauern...

## Alternativen
Kurzer Überblick über die Möglichen Alternativen zur Umsetzung einer internen Suche (die mir bisher eingefallen sind):

### Reine Suchen über die Mailman Archive
Der einfachste Fall wäre lediglich eine Suche übver die Mail-Archive einzurichten. 
Eine gute Dokumentation gibt es unter: [http://wiki.list.org/display/DOC/How+do+I+make+the+archives+searchable](http://wiki.list.org/display/DOC/How+do+I+make+the+archives+searchable ) 

Pro: 

- wohl einfach umzusetzen

Cons:

- gilt im Zweifelsfall lediglich für die Mails



### Alles ins Confluence
Das Confluence bietet bereits eine Suchmöglichkeit über alle Inhalte auf Basis von [Lucene](http://www.adaptavist.com/display/ADAPTAVIST/2011/12/07/5+Best+Practice+Tips+to+Improve+Search+in+Atlassian's+Confluence+Wiki). Ob die Suche in Confluence nun gut oder schlecht ist ist wohl Ansichtssache. Aus meiner beschränkten Erfahrung mit ihr würde ich sagen: sie ist vollkommen ok.

Zur Verbesserung der Suche gibt es noch ein Plugin: [https://marketplace.atlassian.com/plugins/com.atlassian.confluence.plugins.awesomesearch](https://marketplace.atlassian.com/plugins/com.atlassian.confluence.plugins.awesomesearch) .
Allerdings scheint es nicht unbedingt mit der neuesten Confluence Version zu [funktionieren](http://stackoverflow.com/questions/8821696/is-there-a-plugin-to-improve-confluences-search). Habe ich allerdings nicht weiter evaluiert. 

Die Grundidee wäre es die Daten aus den verschiedenen Quellen jweils in eigene reine Such-Spaces in Confluence zu migrieren:

**Integration über die Datenbank:**Auch wenn es *always a bad idea* ist wäre es natürlich möglich. Eine gute Übersicht über das Schema findet man [hier](hier: https://confluence.atlassian.com/pages/viewpage.action?pageId=1794). Sieht sogar noch übersichtlich aus.

Pros:

- die bestehende Suchfunktionalität kann ausgenutzt werden.

Cons: 

- Hohe Abhängigkeit von einem Schema das man nicht unter Kontrolle hat.
- Hoher Aufwand
- Für jedes System müssen eigene Migrationsjobs geschrieben werden.
- Es gibt Spaces die eigentlich nur zur Suche verwendet werden (wohl nicht der Sinn von Confluence).
- InnoQ spezifische Suche ist abhängig von Confluence


**Confluence als Mail-Client:**Die Maillisten ließen sich wohl recht einfach integrieren, indem man Confluence über dieses [Plugin](https://marketplace.atlassian.com/plugins/biz.artemissoftware.plugins.confluence.send-email-to-page) als Subscriber einrichtet. Mails zu neuen Threads könnten dann neue Pages in einem speziellen Confluence Space anlegen und weitere Mails würden z.B. die Page erweitern.

Pros:

- Geringer Aufwand für die Integration (wenn das Plugin sich denn ordentlich konfigurieren läßt)
- Geringer Aufwand für eine Suche über die Mails

Cons:

- Abhängig von dem Hersteller eines Confluence Plugins
- Plugin ist weder Open Source noch umsonst
- die Integration würde sich auf die Mailinglisten beschränken
- Es gibt Spaces die eigentlich nur zur Suche verwendet werden (wohl nicht der Sinn von Confluence). 
- InnoQ spezifische Suche ist abhängig von Confluence

### Verwendung einer fetigen Suchanwendung 
Sprich Google fürs Intranet. Gibt es sogar als [Appliance](http://www.google.com/enterprise/search/campaigns/gsa7.html). Kostet nach unsicherer [Forumsquelle](http://forum.golem.de/kommentare/wirtschaft/search-appliance-7.0-google-box-verwaltet-bis-zu-1-milliarde-seiten/preis/67534,3133637,3133637,read.html) für die kleinste Variante (50000 Dokumente) ca. 50000 Euro für 3 Jahre als Rundum-Sorglos-Paket => wohl eher nicht ;)

Möglich wäre noch die freie Suchmaschine [Yacy](http://yacy.net/de/index.html). Neben dem eigentlichen Peer-to-Peer Index gibt es auch die Alternative den Index lediglich über das Intranet (bzw. eine Menge von selbst angegebenen Uris) laufen zu lassen. Diese Alternative wurde vor einem Jahr von phl schon einmal begutachtet.

Pro:

- es ist alles fertig, sieht schick aus und bietet wohl mehr Funktionen als man eigentlich benötigen würde

Cons:

- aktuell noch keine Möglichkeit Credentials sicher zu hinterlegen.
- der Code ist gelinde gesagt unschön. Man holt sich damit 170.000 Zeilen nicht gerade Wartungs- /Erweiterungsfreundlichencode ins Haus. 

Was ich mir in der Richtung noch angesehen habe:
- [Apache Nutch](http://nutch.apache.org): Macht keinen wirlich vertrauenswürdigen und gut dokumentierten Eindruck.

### Entwickelung einer eigenen Anwendung auf Basis von Elastic Search oder Solr 
Eine eigene Entwicklung geht natürlich immer. Zu entscheiden ist:

- Welche search engine wird verwendet? Im Zweifel solr oder elasticsearch?
- Wie kommen die Daten in die search engine? Möglich sind z.B.: crawling unserer sites, importieren mittels rss feeds, Datenbank bzw. Filesystem auslesen.
- Wie errreicht man eine Authentifizierung?

Phl hatte schon einmal eine interne Suchanwendung angefangen. Zu finden [hier](https://intern.innoq.com/svn/Sandbox/phl/playframework/inview/). Es handelt sich um eine Play 1 Anwendung die die zu indizierenden Seiten über einen Crawler ermittelt (im 1. Schritt bereits umgesetzt) und dann an elasticsearch übergeben soll (noch offen).

Weitere mögliche Java Crawler libs:

- [Übersich](http://java-source.net/open-source/crawlers)
- [niocchi](http://www.niocchi.com )
- [droids](http://incubator.apache.org/droids/)

Eine weitere Möglichkeit ist die Daten über einen Rss Feed zu abonieren. Sowohl elasticsearch als auch solr bieten diese Möglichkeit. iBlog und Confluence unterstützen bereits Rss, den Mailinglisten müsste man es wohl noch über [Rome](http://rometools.org) beibringen.

Pro:

- volle Flexibilität
- geringe Kopplung zu Fremdsoftware
- einfach Integration von neuen Systemen und Inhalten.

Cons:

- Aufwand höher als bei fertigen Lösungen
- Aufwand bei Crawling potenziell sehr hoch
- Aufwand für den Client


## Aktuelles Vorgehen
In einem ersten Schritt werde ich mal versuchen einen Rss Feed über die dev Mailingliste zu legen und sie + iBlog in elasticsearch zu importieren.
Wenn das nicht erfolgsversprechend ist, begebe ich mich an phls crawling Lösung.
