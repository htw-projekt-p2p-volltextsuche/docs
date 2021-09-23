Leistungsoptimierungen des Indexierungsprozesses
================================================

Bei der Integration der einzelnen Sub-Systeme zum Ende des Projektes, ist klar geworden, dass das Gesamtsystem vor allem beim Indexieren kritische Performance Defizite aufweist.


Das Hauptproblem - Bottleneck: Netwerk
--------------------------------------

Das Indexieren einer einzelnen Rede über das P2P-Netzwerk benötigte fast eine halbe Stunde.
Damit ist uns der vorher erwartete Overhead der hohen Netzwerklast sehr direkt klar geworden und wir haben uns im Projekt gemeinsam dazu entschlossen, Optimierungen vorzunehmen, um den Indexierungsprozess in einer vertretbaren Zeit durchführen zu können.

Zum Vergleich handelte es sich bei der lokalen Ausführung des gleichen Prozesses (also ohne Netzwerk-Overhead) lediglich um Millisekunden.

Durch eine Code-Analyse konnten zwei wesentliche Ursachen der Problematik ausgemacht werden.
1. Die Requests von der Volltextsuche zum P2P-Netwerk wurden sequentiell abgearbeitet.
    * [Hier konnten wir erfolgreich optimieren](#paralleles-senden-der-requests-aus-der-volltextsuche)
2. Das P2P-Netzwerk hat, entgegen dem ursprünglichen Plan, keine Zusammenführung aller auf einen Peer hinauslaufenden Requests angeboten.
    * [Hier werden grundlegende, architektonische Änderung benötigt, die in der übrigen Zeit nicht zu schaffen waren](#minimieren-der-netzwerklast-durch-aggregation-der-requests-pro-peer)



### Lösungsansätze

#### Paralleles Senden der Requests aus der Volltextsuche

Der erste Versuch des Parallelisierens der Requests hat grundsätzlich funktioniert, aber zwei weitere Probleme hervogerufen:
* Aus der Volltextsuche wurden so viele Requests in so kurzer Zeit gesendet, dass das P2P-Netwerk nach kurzer Zeit überlastet war.
* Die Fehlerbehandlung innerhalb der Volltextsuche gestaltete sich als wesentlich komplexer gegenüber der sequentiellen Verarbeitung.

Der folgende Ansatz, um diese Probleme in den Griff zu kriegen, war die Implementierung eines Load-Balancers, mit dem versucht wurde das Leistungs-Limit des P2P-Netzwerks auszunutzen, ohne dieses zu überlasten und Fehler zu provozieren.
Mit diesem Ansatz war es weiterhin sehr schwer, die Fehler anständig zu behandeln und auch das Finden eines passenden Request-Limits gestaltete sich schwierig.

Wesentlich erfolgreicher waren wir mit der Umsetzung der nächsten Idee.
Diese beruhte darauf, die Indexierung vorerst lokal im Hauptspeicher vorzunehmen und den resultierenden lokal gecachten Index anschließend über einen regelmäßigen Batch-Job im Hintergrund ins P2P-Netz zu übertragen.
Der Batch-Job wurde zeitgesteuert angestoßen und auf separaten Threads ausgeführt. 
Bei jedem Lauf wurde nur eine bestimmte Menge an zu übertragenden Postings aus dem lokalen Index gelesen, um die einzelnen Sendedruchläufe möglichst kurz zu halten.
Sowohl das Sendeintervall, als auch die Menge eines Datenblocks wurde konfigurierbar gestaltet, um beim weiteren Experimentieren problemlos Anpassungen vornehmen zu können und die Werte ggF. sogar an die genutzte Umgebung anzupassen.

Auch die Fehlerbehandlung ließ sich mit diesem Ansatz leichter angehen.
Alle Fehler die beim Senden der Requests vorgekommen sind, wurden entweder als _FATAL_ERROR_ oder als _RECOVERABLE_ERROR_ eingestuft.
Die Requests der fatalen Fehler wurden verworfen, um die Sende-Queue nicht weiter zu blockieren und die nicht fatalen Fehler wurden mit einem TTL versehen und so lange zurück in den lokalen Cache geschrieben, bis deren TTL abgelaufen ist und auch diese Verworfen wurden.

Mit diesem Ansatz ist es uns gelungen die Laufzeit der Indexierung einer einzelnen, durchschnittlichen Rede **von einer knappen halben Stunde auf unter 3 Minuten** zu bringen.
Dieses Ergebnis ist immer noch nicht befriedigend, doch für einen einzelnen Optimierungsversuch ein großer Erfolg.


### Minimieren der Netzwerklast durch Aggregation der Requests pro Peer

Uns war klar, um weitere Leistungsverbesserungen zu erreichen müssen wir die Anzahl der Requests minimieren, um das Netzwerk zu entlasten.
Für dieses Problem war die theoretische Lösung bereits gegeben, denn sie entspricht dem, was wir ursprünglich für das P2P-Netwerk geplant haben.
Anstatt für jedes einzelne Posting einen eigenen Request zu senden, müssten wir den Hashwert des jeweiligen Postings errechnen und alle Postings, die gemeinsam auf einem Peer gespeichert werden sollen, in einem einzelnen Request zusammenfassen.

Die Umsetzung dieses Ansatzes verlangt, tief in die Logik des P2P-Frameworks einzugreifen.
Die von uns genutzte Bibliothek für die Implementierung des P2P-Frameworks bot diese Möglichkeit leider nicht nativ an.
Das Austauschen dieser Bibliothek hätte zu dem Zeitpunkt zu viel Zeit gekostet und das Umschreiben der Bibliothek auf eigene Faust birgte zu viele Risiken.

Wir haben uns dann gemeinsam dazu entschlossen [einen anderen Weg](https://github.com/htw-projekt-p2p-volltextsuche/p2p-dht/blob/main/docs/data-distribution-design.md#our-choice) zu gehen.


### Weitere weniger kritische Optimierungen
Im Laufe der Entwicklung haben wir einige weitere Optimierungen vorgenommen, wenn immer es angemessen schien.
Grundsätzlich ist klar geworden, dass in dem von uns gewählten Distibutionsmodell für den Invertierten Index, vorallem die Indexierungsphase Leistungsdefizite aufweist. 
Die Anfragephase hat wie erwartet wesentlich bessere Performance aufweisen können.

Dennoch ergab sich auch für die Anfragephase Optimierungspotential:

z.B. haben wir unter anderem in der Volltextsuche ein Caching der Suchanfragen implementiert.
Wenn immer eine zuvor bereits gestellte Suchanfrage in kurzer Zeit erneut gestellt wurde, hat das System keine vollständig neue Volltextsuche durchgeführt, sondern die gecachte Antwort aus dem letzten Aufruf wiederverwendet.
Dieses war vorallem für die Paginierung wichtig, die zwar im UI nicht implementiert wurde, doch in der Volltextsuche bereits voll funktionsfähig war.

Dieses Caching ließe sich noch weiter verbessern, in dem ganze Postinglisten im Hauptspeicher gecached werden würden, um diese auch für vollkommen neue Requests und sogar unabhängig vom aufrufenden Client nutzen zu können.


## Fazit
Abschließend lässt sich sagen, dass wir mit dem Gesamtergebnis des Projektes und den Erfolgen beim Optimieren sehr zufrieden sind.
Die Problemstellung eine Volltextsuche auf Basis eines P2P-Netzwerks zu implementieren birgt viele versteckte Risiken.
Zu Beginn des Projektes war in der gesamten Gruppe das Vorwissen eher gering ausgeprägt.
Wir sind froh, ein funktionierendes Gesamtsystem mit akzeptabler Leisung umgesetzt zu haben und als gesamte Gruppe tiefe Einblicke in die Thematik erlangen zu haben und große Lernerfolge feiern zu können.
