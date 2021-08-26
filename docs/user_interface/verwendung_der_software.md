# Verwendung der Software
Für volle Funktionalität muss das User Interface über das [Ops](https://github.com/htw-projekt-p2p-volltextsuche/ops) gestartet werden.

Für die Nutzung des User Interface benötigt man [Node.js](https://nodejs.org/en/).
Das User Interface ist intern in Frontend und Backend aufgeteilt, diese müssen wenn man sie lokal und alleinstehend ausführen will seperat gestartet werden.
Das Frontend wurde mit [Create-React-App](https://github.com/facebook/create-react-app) initialisiert und lässt sich z.B. mit dem Befehl <code>npm start</code> im "frontend" Ordner starten.

Das Backend ist eine simple Node.js Anwendung das sich mit dem Befehl <code>node index.js</code> im "backend" Ordner starten lässt.

Es stellt einen Endpunkt für einen GET-Request bereit, dieser kann folgendermaßen <code>http://{hostadress}:{hostport}/api/protocol/doc_id</code> erreicht werden und gibt das Protokoll mit der passenden doc_id zurück, falls dieses gefunden wurde.