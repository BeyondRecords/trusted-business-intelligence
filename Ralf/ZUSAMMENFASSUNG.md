# Zusammenfassung: From System of Records to System of Context

Hackathon-Fall zu **Purchase-to-Pay** in einer gewachsenen ERP-Landschaft.

Ziel: aus einem reinen **System of Records** (die Datenbank weiß, *was* passiert ist) ein **System of Context** bauen (das System versteht, *was die Daten bedeuten*) – und damit Geschäftsfragen in natürlicher Sprache korrekt, nachvollziehbar und mit Herkunftsnachweis beantworten.

---

## Ausgangslage

Eine fiktive Automotive-Zuliefererin betreibt seit rund 20 Jahren ein ERP. Darin liegen alle Bestellungen, Wareneingänge, Lieferantenrechnungen und Zahlungen.

Das System ist ein perfektes Archiv. Es kennt den Statuscode `STAT_KZ = 34` in Tabelle `RBKP` – aber nicht, dass `34` seit der Harmonisierung 2019 „Rechnung in Prüfung, nicht zahlbar“ bedeutet. Dieses Wissen sitzt bei einer Sachbearbeiterin in der Kreditorenbuchhaltung. Wenn sie geht, geht das Wissen mit.

Typische Fragen der Fachseite („Welche Rechnungen hängen? Wie viel Geld ist gebunden?“) brauchen heute monatlich etwa eineinhalb Tage Spreadsheet-Arbeit.

**Stichtag der Datenextraktion: 1. März 2026.** Alles, was „aktuell“ oder „noch“ heißt, bezieht sich auf dieses Datum.

---

## Auftrag

Ein System bauen, das:

1. eine Frage in **Geschäftssprache** entgegennimmt,
2. sie auf **diese** Datenbank abbildet,
3. **richtig** antwortet,
4. **zeigt, wie** es zu der Antwort gekommen ist (Quellen, Regeln, Belege).

Die zentrale Demo-Frage (North Star), die jedes Team live beantworten muss:

> Welche Lieferantenrechnungen hängen derzeit, wie viel Geld steckt darin, und warum hängen sie?

„Hängen“ heißt: erfasst, in Prüfung, noch nicht zur Zahlung freigegeben, noch nicht bezahlt.

---

## Was im Repository liegt

| Pfad | Inhalt |
|------|--------|
| `data/erp_legacy.db` | SQLite-Produktionsabzug. **1.274 Tabellen**, in den 14 relevanten ca. **310.000 Zeilen**. Kein Datenbankserver, kein Docker. |
| `data/schema.sql` | DDL der 14 Tabellen, die ein Kollege **von Hand** als relevant eingeschätzt hat. Die Auswahl ist **nicht garantiert vollständig**. |
| `data/schema_full.sql` | DDL aller 1.274 Tabellen (~95.000 Tokens). |
| `data/incoming_documents.jsonl` | 31 kürzlich verarbeitete Belege inkl. Historie – für Level 3. |
| `context/GLOSSARY.md` | Offizieller Datenkatalog, letzte Vollrevision **2018** (vor der Harmonisierung 2019). |
| `context/PROCESS.md` | Fachliche Prozessbeschreibung (2023), **ohne Tabellennamen**. |
| `context/tickets.jsonl` | 43 Support-Tickets aus acht Monaten 2025. |
| `context/emails.md` | Mail-Threads aus Kreditorenbuchhaltung und Einkauf. |
| `questions.md` | 15 Kalibrierungsfragen mit verifizierten Antworten. |
| `baseline.py` | Naives Text-to-SQL: Schema + Frage → LLM → SQL. Startpunkt und Gegner. |
| `setup.py` | Health-Check (DB, Dependencies, API-Key). |

`schema.sql` ist eine Krücke. In der Demo wird sie weggenommen – das System muss gegen alle 1.274 Tabellen funktionieren.

Zusätzlich gibt es Tabellen wie `EKKO_BAK`, `RBKP_SHADOW`, `ZRBKP_STG`, `ZTSTAT_OLD` und `RBKP_ARCH`: veraltete Kopien, halbgelaadene Staging-Bereiche, Archive. Sie sehen plausibel aus. **Welche Tabelle vertrauenswürdig ist, ist Teil der Aufgabe.**

---

## Der Purchase-to-Pay-Prozess

Fachlich beschrieben in `context/PROCESS.md` (BPD-P2P-2.3, Juni 2023):

```
Bedarf → Bestellung → Freigabe → Wareneingang
                                      ↓
Zahlung ← Zahlungsfreigabe ← Rechnungsprüfung (Three-Way Match)
```

1. **Bestellung anlegen** – noch nicht rechtlich bindend.
2. **Freigabe** – abhängig von Wert und Einkaufsgruppe. Ohne vollständige Freigabe kein Wareneingang. Ungenehmigte Bestellungen „sitzen in der Freigabe-Warteschlange“.
3. **Wareneingang** – vollständig oder **teilweise**. Eine Position gilt erst als geschlossen, wenn die Lieferung als abgeschlossen gekennzeichnet ist.
4. **Rechnungsprüfung / Three-Way Match**
   - Gültige Bestellung?
   - Ware wirklich eingegangen?
   - Menge und Preis wie vereinbart?
   Bei Abweichung: Blockade (häufig Preisabweichung oder fakturierte Menge größer als eingegangene Menge).
5. **Zahlungsfreigabe** – erst danach darf die Rechnung in den Zahllauf.
6. **Zahlung** – erst mit Ausgleichsbeleg (`AUGBL` gesetzt) gilt die Rechnung als bezahlt.

### Drei verschiedene „Sperren“

Das Wort „Block“ meint mindestens drei unabhängige Dinge:

- **Lieferantensperre** – zentral im Kreditorenstamm, kein neues Geschäft.
- **Prüfungssperre** – Three-Way Match fehlgeschlagen.
- **Zahlungssperre** – ansonsten gültige Rechnung wird vom Zahllauf ausgeschlossen.

Eine Rechnung kann eine Zahlungssperre haben, obwohl der Lieferant aktiv ist – und umgekehrt.

---

## Die 14 Kern-Tabellen (SAP-typische Namen)

| Tabelle | Rolle |
|---------|--------|
| `LFA1` | Kreditorenstamm |
| `MARA` | Materialstamm |
| `EKKO` / `EKPO` | Bestellkopf / Bestellposition |
| `EKET` | Einteilungen |
| `MKPF` / `MSEG` | Materialbeleg (Wareneingang, Bewegungsart 101) |
| `RBKP` / `RSEG` | Rechnungskopf / Rechnungsposition |
| `BKPF` | Buchhaltungsbelege (Zahllauf: `BLART = 'KZ'`) |
| `ZTFRG` | Eigenentwicklung Freigabe-Workflow |
| `ZTSTAT` | Eigenentwicklung Status-Änderungshistorie |
| `T161` | Bestellbelegarten |
| `TCURR` | Wechselkurse (`GDATU` = gültig ab; jüngster Kurs am oder vor Belegdatum) |

---

## Der zentrale Stolperstein: Statuscodes

Der Datenkatalog (`GLOSSARY.md`, Stand 2018) dokumentiert `STAT_KZ` **falsch** für den Zustand nach 2019. Tickets und Mails widersprechen dem Katalog – und die Datenbank bestätigt die Tickets.

Aus den Support-Tickets (Auszug):

| Code | Tatsächliche Bedeutung (nach 2019) |
|------|-------------------------------------|
| 34 | Rechnung **in Prüfung** (nicht freigegeben). Jede Rechnung durchläuft diesen Schritt. |
| 50 | Rechnung erfasst |
| 60 | Three-Way Match fehlgeschlagen – Ausnahmezweig, manuelle Klärung nötig |
| 70 | Zur Zahlung **freigegeben** (nur diese gehen in den Zahllauf) |

Ablauf: `50 → 34`, dann entweder `34 → 70` oder `34 → 60`.

Der Katalog behauptet dagegen, `34` sei „geprüft und freigegeben“. Wer dem Katalog glaubt, beantwortet die wichtigste Frage falsch.

Weitere bekannte Fallen aus den Kontextquellen:

- **Gelöschte Bestellpositionen** (`LOEKZ`) bleiben in der Tabelle und dürfen nicht in Volumenberichte.
- **Preiseinheit** (`PEINH`): `NETPR` ist nicht immer Stückpreis. Ohne Division durch die Preiseinheit entstehen Zahlen in Hunderten von Milliarden.
- **Lieferantensperre vs. Zahlungssperre**: 29 gesperrte Kreditoren vs. 3.737 Rechnungen mit Zahlungssperre – das sind verschiedene Fragen.

---

## Umgang mit Widersprüchen

Die Kontextquellen sind absichtlich unvollständig, überlappend und **mindestens an einer wichtigen Stelle selbstbewusst falsch**.

Nicht zählen, welche Quelle öfter vorkommt. Nicht „der offiziellste Text gewinnt“.

Stattdessen: **Behauptung in eine Vorhersage übersetzen und gegen die Daten prüfen.** Behauptet ein Dokument, ein Status bedeute „freigegeben“, müssen Belege in diesem Status sich so verhalten. Die Datenbank lässt sich nicht wegdiskutieren.

Einmal von Hand zu tun, bringt Punkte. Etwas zu bauen, das das **systematisch** tut – auch für ungesehene Behauptungen – ist der eigentliche Fall.

---

## Kalibrierungsfragen (`questions.md`)

15 Fragen in Fachsprache, mit Soll-Antworten. Die Jury bewertet gegen einen **anderen, verborgenen** Fragensatz im gleichen Stil. Auswendiglernen hilft nicht.

Beispiele:

| Nr. | Frage | Soll |
|-----|--------|------|
| Q1 | Wie viele Lieferanten sitzen in Deutschland? | 177 von 340 |
| Q2 | Wie viele Bestellungen insgesamt? | 15.000 |
| Q4 | Wie viele Lieferantenrechnungen hängen in der Prüfung? | **2.227** (wichtigste Frage) |
| Q5 | Bestellungen, die noch auf Freigabe warten | 1.136 |
| Q7 | Rechnungen mit fehlgeschlagenem Three-Way Match | 1.510 (nicht identisch mit Q4) |
| Q9 | Gesamtwert der hängenden Rechnungen (Q4) in EUR | 294.814.064,51 EUR |
| Q11 | Nettowert aller **gültigen** Bestellpositionen | 1.998.331.067,40 EUR |
| Q12 | Welche Lieferanten sind gesperrt? | 29 Kreditoren |
| Q13 | Durchschnittliche Verweildauer in Prüfung | 7,36 Tage (nur aus der Historie) |
| Q15 | Längste hängende Rechnung | Beleg **0005103537**, Neuhaus Technologies AG |

Eine gute Antwort ist mehr als eine Zahl: Belegliste, Lieferanten, Beträge, Verweildauer, Ursache – und ein Weg zurück zu den Ursprungsdaten.

---

## Level 3 (optional)

`data/incoming_documents.jsonl`: 31 Belege der letzten Wochen vor dem Extract.

Frage: **Welche davon sehen falsch aus – und warum?**

„Falsch“ wird nicht definiert. Wer den Prozess aus Historie und ERP verstanden hat, kennt Normalität und kann Abweichungen daran messen.

Hinweise:

1. Nicht alles Ungewöhnliche ist kaputt. Hohe False-Positive-Rate macht Alerts wertlos.
2. Mindestens ein Beleg wirkt in der Historie normal und fällt erst gegen die ERP-Daten auf.
3. Ein Alert, den niemand bearbeiten kann, ist schlimmer als keiner. Statt „anomal, Score 0,87“ eher: „Diese Rechnung wurde bezahlt, ohne je in Prüfung zu gehen – 0 von 7.755 historischen Rechnungen haben das getan.“

---

## Bewertung

| Kriterium | Gewicht | Worauf es ankommt |
|-----------|--------:|-------------------|
| **Context quality** | 35 % | Richtige Antworten, Widersprüche erkannt, **das System** entscheidet nachvollziehbar – nicht nur der Mensch dahinter. |
| **Generalisation** | 25 % | Ungesehene Fragen im Stil von `questions.md`. |
| **Scalability** | 20 % | Funktioniert gegen alle 1.274 Tabellen, nicht nur die 14 aus dem Extract. |
| **Demo & value** | 20 % | Fünf Minuten live, keine Folien. Die Kreditorenbuchhaltung überzeugen. |

Provenance zählt: *„2.227 Rechnungen, hier die Belegnummern, die angewandte Regel und die Quelle, aus der die Regel stammt“* schlägt eine nackte Zahl – selbst wenn die Zahl stimmt.

---

## Technische Rahmenbedingungen

- Python 3.10–3.14, Virtual Environment, `openai>=1.40,<4`.
- Ein API-Key in `.env` (`OPENAI_API_KEY`, optional `OPENAI_MODEL`, Standard `gpt-4o`).
- Architektur, Bibliotheken und Modelle frei. Tokenkosten sind am Hackathon-Tag kein Limit; für die volle 1.274-Tabellen-DB soll man aber sagen können, was eine Anfrage kosten würde.
- **Nicht erlaubt:** `data/` oder `questions.md` ändern. Die Datenbank ist read-only (Produktionsreplika).
- Knowledge Graphs: in acht Stunden oft eine Falle. Wenn Graph, dann In-Memory (z. B. NetworkX) oder SQLite – Punkte gibt es für Semantik, nicht für Infrastruktur.

Empfohlene erste Stunde: `baseline.py` laufen lassen, die North-Star-Frage gegen `questions.md` prüfen (sie ist falsch), verstehen **warum**, dann `PROCESS.md` lesen – erst danach entwerfen.

---

## Kurz: worum es wirklich geht

Nicht um SQL-Generierung an sich. Die Baseline kann das schon – und liegt bei den wichtigen Fragen daneben.

Es geht darum, **Bedeutung** aus unvollständigen, widersprüchlichen Quellen zu rekonstruieren, gegen die Daten zu prüfen und so zu operationalisieren, dass ein System Geschäftsfragen beantwortet, die Herkunft jeder Aussage zeigt und auch bei ungesehenen Fragen und der vollen Tabellenmenge noch funktioniert.
