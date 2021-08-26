# Installation und Ausführung

## Ohne Docker
1. Stellen Sie sicher, dass .NET 5.0 SDK auf Ihrem System installiert ist
2. Klonen Sie das Repository
3. Bauen Sie die Anwendung mit `dotnet publish ./Crawler -c Release`
4. Wechseln Sie zum Ausgabeverzeichnis des Builds `./Crawler/bin/Release/net5.0/publish`
5. Folgen Sie dem Anleitungsschritt "Konfiguration"
6. Führen Sie die Anwendung aus `./Crawler.exe`

## Mit Docker Compose
### Bauen
1. Klonen Sie das Repository
2. Führen Sie folgenden Befehl im Root-Verzeichnis des Repositories aus, um das Docker-Image zu bauen: `docker build . --tag crawler`

### Ausführen
1. Erstellen Sie eine appsettings.json mit Ihren gewünschten Einstellungswerten.
2. Führen Sie den Docker-Container aus mit `docker-run -v ./appsettings.json:/app/appsettings.json crawler`

### Mounting Points
- Der Pfad `/app/data/` muss zum Hostsystem persistiert werden, damit der Crawler tracken kann, welche Protokolle bereits indexiert wurden.
- Die `appsettings.json` Datei muss nach `/app/appsettings.json` gemounted werden, falls die Konfiguration via .json-Datei geschehen soll. Alternativ kann dieser Schritt weggelassen werden, wenn die Konfiguration via Umgebungsvariablen geschieht

## Konfiguration
Die Konfiguration geschieht über die in der Standard-Konfigurationsdatei bereitgestellen Parameter. Über den Einsellungen sind Kommentare zur Erläuterung vorhanden. Es muss entweder die appsettings.json existieren oder es müssen alle vom Standard abweichenden Einstellungen über gleichnamige Umgebungsvariablen übergeben werden. Umgebungsvariablen überschreiben die Werte der appsettings.json.

Wenn z.B. die Einstellung `Interval` überschrieben werden soll mit einer Umgebungsvariable, dann muss die Umgungsvariable auch `Interval` heißen.


| **Name** | **Standardwert** | **Beschreibung** |
|---|---|---|
| Interval | `* * * * *` | Intervall als CRON-Expression, die bestimmt in welchem Intervall der Crawler die Seite des Bundestages überprüft. |
| InitialDelay | `0` | Einmalige Verzögerung des Anwendungsstarts in Sekunden. |
| ChunkDelay | `0` | Verzögerung zwischen den POST Anfragen an die Indexing-API in Sekunden. |
| MaximumBatchSize | `5` | Maximale Anzahl der Reden die in einer POST Anfrage an die Indexing-API  übergeben werden. |
| MongoConnectionString | `mongodb://0.0.0.0:8430` | Verbindungsstring zur Mongo-DB Datenbank, in der der Crawler extrahierte Reden ablegen wird. |
| MongoDatabase | `crawler` | Name der Datenbank. |
| MongoCollection | `protocols` | Name der Collection. |
| LocalDbConnectionString | `Data Source=data/local.db` | Verbindungsstring zur SQLite Datenbank, die der Crawler nutzt um abzuspeichern welche Dokumente bereits indexiert wurden. |
| IndexingApiEndpoint | `http://0.0.0.0:8421/api` | Endpunkt des P2P Netzwerkes, an den der Crawler Reden zur Indexierung schickt. |
| IndexingApiTimeout | `300` | Timeout der HTTP Anfrage der vorangehenden Einstellung. |