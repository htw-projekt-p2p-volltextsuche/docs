# Probleme
Das Design des Suchformulars musste abgeändert werden, da aufgrund des Aufbaus nicht genau klar war, in welcher Reihenfolge beziehungsweise mit welcher Priorität einzelne Formularreihen ausgewertet werden. Infolgedessen wurde das Suchformular in drei Hauptbereiche eingeteilt: Freie Suche sowie Partei und Redner, die letzteren beiden bieten jedoch keine Auswahl eines logischen Operators mehr an.

Im Laufe des Projekts wurde klar, dass außerdem ein Backend für den Zugriff auf die MongoDB nötig ist, da ein direkter Zugriff aus dem UI nicht möglich ist. Es wurde als simples Node.js Projekt aufgesetzt, das eine GET-Request mit Dokumenten-Id als Übergabeparameter beantworten kann.

Anfangs war eine Sortierung der Suchergebnisse in Diskussion, diese wurde aber bewusst weggelassen, da die Suchergebnisse standardmäßig nach Relevanz sortiert von der Volltextsuche zurückgegeben werden. Eine alphabetisches Sortieren nach Partei oder Titel der Reden erschien obsolet. Durch die Komplexität der Titel wird bei einer alphabetischen Sortierung kein Mehrwert geboten.


