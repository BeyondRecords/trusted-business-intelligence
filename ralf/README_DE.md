# Von System of Records zu System of Context

**Hackathon-Fall – Purchase-to-Pay in einer gewachsenen ERP-Landschaft**

---

## Die Ausgangslage

Ihnen wurde gerade der Zugang zur Produktionsdatenbank eines fiktiven
Automotive-Zulieferers übergeben. Sie läuft seit zwanzig Jahren. Sie enthält
jede Bestellung, jede Lieferung, jede Lieferantenrechnung und jede Zahlung,
die das Unternehmen je verarbeitet hat.

Es ist ein perfektes **System of Records**. Es weiß genau, *was* passiert ist.

Es weiß nichts darüber, was davon *bedeutet*.

Öffnen Sie `data/schema.sql` und Sie finden eine Tabelle namens `RBKP` mit
einer Spalte namens `STAT_KZ`. Einer der Werte in dieser Spalte ist `34`.
Irgendwo im Gebäude sitzt eine Frau in der Kreditorenbuchhaltung, die weiß,
dass `34` bedeutet: Eine Lieferantenrechnung liegt in der Prüfung und kann
nicht bezahlt werden. Das weiß sie, seit das System 2019 harmonisiert wurde
und eine Reihe dieser Codes still ihre Bedeutung geändert hat. Nirgendwo
steht das schriftlich.

Wenn sie in Rente geht, geht dieses Wissen mit ihr.

Inzwischen stellt das Geschäft Fragen wie *„welche Lieferantenrechnungen
hängen?“* und *„wie viel Geld steckt darin?“* – und sie zu beantworten kostet
eine Fachkraft jeden einzelnen Monat eineinhalb Tage manuelle
Spreadsheet-Arbeit.

## Ihr Auftrag

**Bauen Sie ein System of Context auf dem System of Records.**

Etwas, das eine in Geschäftssprache formulierte Frage entgegennehmen, sie im
Hinblick auf diese Datenbank verstehen, sie korrekt beantworten und seine
Arbeit zeigen kann.

Das ist das gesamte Briefing. Wie Sie dorthin kommen, bleibt Ihnen überlassen.

---

## Setup (sollte unter fünf Minuten dauern)

Erfordert **Python 3.10 – 3.14**. Prüfen Sie mit `python --version`, bevor
Sie starten. Alles andere installiert sich in einem Schritt.

**macOS / Linux**

```bash
git clone <repo-url>
cd <repo>

python3 -m venv .venv
source .venv/bin/activate

python -m pip install -r requirements.txt

cp .env.example .env
# den API-Key, den Sie beim Kickoff bekommen haben, in .env einfügen

python setup.py               # Health-Check, sagt Ihnen, was fehlt
python baseline.py
```

**Windows (PowerShell)**

```powershell
git clone <repo-url>
cd <repo>

python -m venv .venv
.\.venv\Scripts\Activate.ps1

python -m pip install -r requirements.txt

Copy-Item .env.example .env
notepad .env                  # den Key vom Kickoff einfügen, dann speichern

python setup.py
python baseline.py
```

Verwenden Sie `python -m pip`, nicht `pip`. Auf einem verwalteten
Firmenlaptop wird `pip.exe` oft komplett blockiert, während das Modul dahinter
einwandfrei funktioniert – siehe Troubleshooting unten.

Kein Datenbankserver. Kein Docker. Keine Zugangsdaten außer dem einen
API-Key. `data/erp_legacy.db` ist eine gewöhnliche SQLite-Datei –
`sqlite3.connect()` und Sie sind drin. Das Repo ist rund 45 MB groß, weil die
Datenbank echt ist.

**Der Extract wurde am 1. März 2026 gezogen.** Nichts in den Daten ist später
datiert. Wenn eine Frage „aktuell“ oder „noch“ sagt, ist das das gemeinte
Datum.

**Wenn Sie innerhalb von zehn Minuten nach dem Hinsetzen keine Daten
abfragen, hören Sie auf und holen Sie eine Betreuungsperson.** Das ist ein
Problem unseres Setups, nicht Ihres.

### Troubleshooting

Vier Fehler erklären fast alles, was auf einem Firmen-Windows schiefgeht.
Keiner davon ist Ihre Schuld, und keiner dauert länger als eine Minute zu
beheben.

**`pip` schlägt fehl mit „Zugriff verweigert“ / „Access is denied“**

Ihr Endpoint Protection blockiert `pip.exe` als Paketmanager. Das Python-Modul
darunter ist nicht blockiert. Nutzen Sie es stattdessen:

```powershell
python -m pip install -r requirements.txt
```

**`python` öffnet den Microsoft Store**

Python ist nicht im PATH, und Windows bietet Ihnen sein eigenes an. Entweder
von python.org neu installieren und **Add python.exe to PATH** anhaken, oder
die Aliase unter *Einstellungen > Apps > Erweiterte App-Einstellungen >
Ausführungsaliase für Apps* abschalten – sowohl `python.exe` als auch
`python3.exe`.

**`Activate.ps1 cannot be loaded because running scripts is disabled`**

PowerShell-Ausführungsrichtlinie. Das ist eine Einstellung pro Benutzer, keine
Systemänderung:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

**Legen Sie `.venv` nicht in OneDrive**

Wenn Ihr `Documents`-Ordner nach OneDrive synchronisiert, klonen Sie stattdessen
irgendwo lokal, z. B. nach `C:\hack\`. Eine synchronisierte virtuelle Umgebung
bedeutet Tausende Dateien, die durch den Sync-Client laufen, und OneDrive-
Platzhalterdateien können die Ausführung verweigern. Die Datenbank und die
Kontextdateien sind in OneDrive in Ordnung; die virtuelle Umgebung nicht.

Wenn keiner dieser Fälle Ihr Problem ist, holen Sie eine Betreuungsperson,
statt den Vormittag daran zu verlieren.

---

## Was im Repo liegt

```
data/
  erp_legacy.db       die Produktionsdatenbank. 1.274 Tabellen, 310k Zeilen in
                      den vierzehn, die zählen.
  schema.sql          DDL der 14 Tabellen, die ein Kollege für relevant hält.
                      Er hat die Liste von Hand zusammengestellt. Er ist nicht
                      sicher, dass sie vollständig ist.
  schema_full.sql     DDL aller 1.274 Tabellen. ~95.000 Tokens.
  incoming_documents.jsonl
                      31 Dokumente, die kürzlich verarbeitet wurden. Für Level 3.

context/
  GLOSSARY.md         der offizielle Datenkatalog. Letzte Vollrevision 2018.
  PROCESS.md          so beschreibt das Geschäft den Prozess. Keine Tabellennamen.
  tickets.jsonl       43 Support-Tickets über acht Monate 2025.
  emails.md           Mail-Threads aus Kreditorenbuchhaltung und Einkauf.

questions.md          15 Fragen mit verifizierten Antworten. Darauf kalibrieren.
setup.py              Health-Check.
baseline.py           naives Text-to-SQL. Ihr Startpunkt und Ihr Gegner.
```

**Lesen Sie den ersten Eintrag noch einmal.** Die Datenbank hat 1.274 Tabellen.
`schema.sql` enthält 14 davon, weil jemand sich hingesetzt und geraten hat,
welche zählen. Sie können den ganzen Tag mit diesem Extract arbeiten und weit
kommen – die Fragen in `questions.md` sind aus diesen 14 Tabellen
beantwortbar.

Aber der Extract ist eine Krücke, und in der Demo nehmen wir sie weg.

Es gibt dort auch Tabellen namens `EKKO_BAK`, `RBKP_SHADOW`, `ZRBKP_STG`,
`ZTSTAT_OLD` und `RBKP_ARCH`. Manche sind veraltete Kopien, manche halb
geladene Staging-Bereiche, eine ist ein Archiv. Sie enthalten Daten, die
völlig plausibel aussehen. Zu entscheiden, welcher Tabelle man traut, ist
Teil der Aufgabe.

Die Kontextquellen sind absichtlich unordentlich. Sie sind unvollständig, sie
überlappen, sie wurden zu unterschiedlichen Zeiten von unterschiedlichen
Personen geschrieben, und **mindestens eine von ihnen liegt bei etwas
Wichtigem selbstbewusst falsch.** Herauszufinden, welcher Quelle man traut,
wenn sie sich widersprechen, ist keine lästige Nebenquest. Es ist der Fall.

### Widersprüche auflösen

Sie werden Uneinigkeiten zwischen den Quellen finden. Der naheliegende Zug
ist, die Quelle zu nehmen, die vertrauenswürdiger wirkt. Widerstehen Sie dem.

Quellen zählen funktioniert auch nicht – drei Dokumente, die dasselbe sagen,
können alle dieselbe veraltete Vorlage kopiert haben. Eine Mehrheit ist kein
Beweis.

Was funktioniert: **die Behauptung in eine Vorhersage übersetzen und gegen
die Daten prüfen.** Behauptet ein Dokument, ein Statuscode bedeute
„freigegeben“, dann sollten Dokumente in diesem Status sich auf eine bestimmte
Weise verhalten. Entweder tun sie das oder nicht. Mit der Datenbank kann man
nicht streiten.

Das einmal von Hand zu tun, ist ein paar Punkte wert. Etwas zu bauen, das es
systematisch tut – sodass es auch bei einer Behauptung funktioniert, die Sie
noch nicht gesehen haben – ist das, worum der Fall eigentlich bittet.

---

## Die North-Star-Frage

Jedes Team muss diese eine am Ende des Tages live beantworten können:

> **„Welche Lieferantenrechnungen hängen derzeit, wie viel Geld steckt
> darin, und warum hängen sie?“**

Wenn Ihr System das korrekt beantworten und zeigen kann, woher jeder Teil der
Antwort stammt, haben Sie etwas Echtes gebaut.

---

## Level 3 – optional, nur wenn Sie so weit kommen

Niemand wird erwartet, das zu erreichen. Es existiert, damit Teams, die früh
fertig sind, wohin können, und es ist der Punkt, an dem der Fall aufhört,
eine Reporting-Übung zu sein.

`data/incoming_documents.jsonl` enthält 31 Dokumente, die in den Wochen vor
dem Extract durch das System gelaufen sind. Jedes trägt seine
Verarbeitungshistorie.

> **Welche davon sehen falsch aus, und warum?**

Niemand wird Ihnen sagen, was „falsch“ bedeutet. Aber wenn Ihr System den
Prozess aus den historischen Daten verstanden hat, weiß es bereits, wie
normal aussieht – und alles, von dem es weiß, dass es normal ist, kann es als
Maßstab nutzen.

Drei Warnungen, in steigender Wichtigkeit:

1. Nicht alles Ungewöhnliche ist kaputt. Manche dieser Dokumente sind selten,
   aber vollkommen legitim. Ein System, das jeden Ausreißer markiert, ist
   nutzlos – auf einem echten System mit 1,5 Millionen Dokumenten bedeuten
   5 % False Positives 75.000 Alerts und ein Postfach, das niemand öffnet.
2. Nicht jede Anomalie ist in der Historie allein sichtbar. Mindestens ein
   Dokument sieht völlig normal aus, bis Sie es gegen die ERP-Daten prüfen.
3. **Ein Alert, den niemand bearbeiten kann, ist schlimmer als keiner.**
   „Dieses Dokument ist anomal, Score 0,87“ ist Rauschen. „Diese Rechnung
   wurde bezahlt, ohne je in Prüfung zu gehen – 0 von 7.755 historischen
   Rechnungen haben das getan“ ist etwas, bei dem jemand zum Hörer greifen
   kann.

---

## Regeln

**Erlaubt:** alles. Jede Bibliothek, jede Architektur, jedes Modell, das über
den Key verfügbar ist, den Sie bekommen haben. Knowledge Graphs, Vector
Stores, feinabgestimmte Klassifikatoren, eine handgeschriebene YAML-Datei –
kein Ansatz ist ausgeschlossen.

**Tokenkosten sind heute kein Limit.** Das Budget ist gesponsert und
großzügig. Geben Sie es aus. Seien Sie aber bereit zu sagen, was Ihr Ansatz
pro Anfrage gegen die volle 1.274-Tabellen-Datenbank kosten würde.

**Nicht erlaubt:** `data/` oder `questions.md` bearbeiten. Die Datenbank ist
read-only – behandeln Sie sie als Produktionssystem, von dem Sie eine Replika
bekommen haben.

**Eine Warnung zu Knowledge Graphs.** Mehrere von Ihnen werden Neo4j
aufsetzen wollen. Bei einer achtstündigen Veranstaltung ist das eine Falle.
Wenn ein Graph für Sie die richtige Antwort ist, bauen Sie ihn im Speicher
mit NetworkX oder in SQLite und verwenden Sie Ihre Zeit für die Semantik
statt für Infrastruktur. Wir vergeben keine Punkte für das Logo auf Ihrer
Datenbank.

---

## Wie Sie bewertet werden

| Kriterium | Gewicht | Worauf wir achten |
|---|---:|---|
| **Context quality** | 35 % | Stimmen die Antworten? Haben Sie die Widersprüche zwischen den Quellen erkannt – und können Sie zeigen, *wie Ihr System* entschieden hat, nicht wie Sie entschieden haben? |
| **Generalisation** | 25 % | Wir stellen Fragen, die Sie nicht gesehen haben, im gleichen Stil wie die in `questions.md`. |
| **Scalability** | 20 % | Funktioniert Ihr System gegen alle 1.274 Tabellen, nicht nur die 14 im Extract? Wir werden Sie bitten, es zu versuchen. |
| **Demo & value** | 20 % | Fünf Minuten, live, keine Folien. Überzeugen Sie die Frau in der Kreditorenbuchhaltung. |

Level 3 wird nicht gesondert bewertet. Es zählt zur Context Quality, und es
ist der stärkste Beleg, den Sie anbieten können, dass Ihr System den Prozess
verstanden hat, statt einen Satz Antworten auswendig zu lernen.

### Zur Herkunft (Provenance)

Eine Antwort, die niemand prüfen kann, ist in einer Finanzabteilung wertlos.
Ein System, das sagt *„2.227 Rechnungen, und hier sind die Belegnummern, die
Regel, die ich angewandt habe, und die Quelle, aus der ich diese Regel gelernt
habe“*, schlägt ein System, das *„2.227“* sagt – selbst wenn die Zahl
identisch ist.

---

Die Case Ownerin ist den ganzen Tag im Raum. **Stellen Sie Fragen.**
Herauszufinden, was `ZTFRG` bedeutet, indem Sie es anstarren, ist keine gute
Verwendung Ihres Nachmittags; herauszufinden, wie man ein System dazu bringt,
das für Sie zu tun, schon.

---

## Eine vorgeschlagene erste Stunde

Keine Anforderung, nur das, was wir tun würden.

1. Führen Sie `baseline.py` aus. Sehen Sie zu, wie es die North-Star-Frage
   beantwortet. Prüfen Sie die Antwort gegen `questions.md`. Stellen Sie fest,
   dass sie falsch ist.
2. Finden Sie heraus, *warum* sie falsch ist. Das sind die wertvollsten
   zwanzig Minuten Ihres Tages.
3. Lesen Sie `context/PROCESS.md` – ohne zu verstehen, was ein Three-Way Match
   ist, kommen Sie nicht weit.
4. Erst dann mit dem Entwurf beginnen. Die Teams, die bei solchen Events
   gewinnen, sind die, die das Problem verstanden haben, bevor sie angefangen
   haben zu tippen.

Viel Erfolg.
