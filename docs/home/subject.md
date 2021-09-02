# Subject

The subject for our fulltext search are the speeches of the _Bundestag_. This set of data was chosen due to its Open Data nature combined with the size of the dataset and the fact that these protocols are recorded live into an XML formatted document. 

The XML format _should_ mean, that these files are easier to parse, but this was not always the case. There's a clear distinction between the 19th legistlative period and the 18th and earlier periods.

```
<?xml version="1.0" encoding="utf-8"  ?>
<?xml-stylesheet href="dbtplenarprotokoll.css" type="text/css" charset="UTF-8"?>
<!DOCTYPE dbtplenarprotokoll SYSTEM "dbtplenarprotokoll.dtd">
<dbtplenarprotokoll vertrieb="Bundesanzeiger Verlagsgesellschaft mbH, Postfach 1 0 05 34, 50445 Köln, Telefon (02 21) 97 66 83 40, Fax (02 21) 97 66 83 44, www.betrifft-gesetze.de" herstellung="H. Heenemann GmbH  Co. KG, Buch- und Offsetdruckerei, Bessemerstraße 83–91, 12103 Berlin, www.heenemann-druck.de" sitzung-ort="Berlin" herausgeber="Deutscher Bundestag" issn="0722-7980" wahlperiode="19" sitzung-nr="237" sitzung-datum="25.06.2021" sitzung-start-uhrzeit="9:00" sitzung-ende-uhrzeit="18:16" sitzung-naechste-datum="07.09.2021" start-seitennr="30883">
	<vorspann>
		<kopfdaten>
			<plenarprotokoll-nummer>Plenarprotokoll <wahlperiode>19</wahlperiode>/<sitzungsnr>237</sitzungsnr>
			</plenarprotokoll-nummer>
			<herausgeber>Deutscher Bundestag</herausgeber>
			<berichtart>Stenografischer Bericht</berichtart>
			<sitzungstitel>
				<sitzungsnr>237</sitzungsnr>. Sitzung</sitzungstitel>
			<veranstaltungsdaten>
				<ort>Berlin</ort>, <datum date="25.06.2021">Freitag, den 25. Juni 2021</datum>
			</veranstaltungsdaten>
		</kopfdaten>
		<inhaltsverzeichnis>
			<ivz-titel>Inhalt:</ivz-titel>
			<ivz-eintrag>
				<ivz-eintrag-inhalt>Absetzung des Zusatzpunktes 21</ivz-eintrag-inhalt>
				<a href="S30883" typ="druckseitennummer">
					<seite>30883</seite>
					<seitenbereich>A</seitenbereich>
				</a>
```

This first excerpt is an example from the 19th period. The document follows the XML structure well and provides good groundwork to allow easy parsing.

```
<?xml version="1.0" encoding="UTF-8"?>
<DOKUMENT>
  <WAHLPERIODE>18</WAHLPERIODE>
  <DOKUMENTART>PLENARPROTOKOLL</DOKUMENTART>
  <NR>18/179</NR>
  <DATUM>23.06.2016</DATUM>
  <TITEL>Plenarprotokoll vom 23.06.2016</TITEL>
  <TEXT>Plenarprotokoll 18/179

Deutscher Bundestag
Stenografischer Bericht

179. Sitzung

Berlin, Donnerstag, den 23. Juni 2016

Inhalt:

Wahl der Abgeordneten Nina Warken als or-
dentliches Mitglied des Gemeinsamen Aus­
schusses   .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  17575 A

Wahl des Abgeordneten Steffen Bilger als
ordentliches Mitglied des Vermittlungsaus­
schusses   .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  17575 B

Erweiterung und Abwicklung der Tagesord-
nung .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  17575 B

Absetzung der Tagesordnungspunkte 14, 15 b
und 25  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  .  17576 D

Begrüßung des Botschafters der Republik
Polen, Herrn Jerzy Jozef Marganski  .  .  .  .  .  .  17613 C
```

This second excerpt is an example from the 18th period. Without the few XML tags at the start of the document, it would be impossible to tell that this is indeed an XML file and not a plain text file. This made these documents quite a lot more difficult to parse.

For more information about these problems and their solutions, please find the documentations about these respective components.
