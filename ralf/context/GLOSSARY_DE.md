# Datenkatalog – Einkauf & Kreditorenbuchhaltung

**Dokument-ID:** DK-MM-014
**Letzte Vollrevision:** 2018-11-04
**Fachlicher Eigentümer:** T. Schmidt, IT Applications MM/FI
**Status:** gepflegt

> Redaktioneller Hinweis: Dieses Dokument wurde zuletzt vollständig revidiert,
> bevor das Systemharmonisierungsprogramm 2019 stattfand. Einzelne Abschnitte
> wurden seither ad hoc geändert. Eine vollständige Überarbeitung steht noch
> aus. Im Zweifel bitte die Fachabteilung konsultieren.

---

## 1. Kreditorenstamm (LFA1)

| Feld | Bedeutung |
|-------|---------|
| LIFNR | Lieferantennummer, 10 Stellen mit führenden Nullen |
| NAME1 | Lieferantenname |
| LAND1 | Länderschlüssel (ISO) |
| ORT01 | Ort |
| SPERR | Sperrkennzeichen |
| ERDAT | Anlagedatum |
| KTOKK | Kontengruppe |

## 2. Materialstamm (MARA)

| Feld | Bedeutung |
|-------|---------|
| MATNR | Materialnummer, 18 Stellen |
| MTART | Materialart (ROH = Rohstoff, HALB = Halbfabrikat) |
| MATKL | Warengruppe |
| MEINS | Basismengeneinheit |
| MAKTX | Kurztext |
| LVORM | Löschvormerkung |

## 3. Bestellungen

### EKKO – Einkaufsbelegkopf

| Feld    | Bedeutung |
|---------|---------|
| EBELN   | Bestellnummer |
| BUKRS   | Buchungskreis (in unserem System immer 1000) |
| BSART   | Belegart, siehe Customizing-Tabelle T161 |
| LIFNR   | Lieferant |
| EKGRP   | Einkäufergruppe |
| WAERS   | Belegwährung |
| AEDAT   | Anlagedatum |
| ERNAM   | Angelegt von |
| FRGKE   | Freigabekennzeichen |
| STAT_KZ | Statuskennzeichen, siehe Abschnitt 6 |

### EKPO – Einkaufsbelegposition

| Feld | Bedeutung |
|-------|---------|
| EBELN | Bestellnummer |
| EBELP | Positionsnummer |
| MATNR | Material |
| WERKS | Werk |
| MENGE | Bestellmenge |
| NETPR | Nettopreis |
| PEINH | Preiseinheit |
| ELIKZ | Endlieferungskennzeichen |
| LOEKZ | Löschkennzeichen |

## 4. Wareneingang

### MKPF / MSEG – Materialbeleg

Wareneingänge werden als Materialbelege abgebildet. Kopfdaten liegen in
MKPF, Positionen in MSEG. Die Verknüpfung zurück zur Bestellung erfolgt über
die Felder EBELN und EBELP in MSEG.

BWART ist die Bewegungsart. Wareneingang gegen eine Bestellung verwendet
Bewegungsart 101.

## 5. Rechnungsprüfung

### RBKP – Rechnungskopf

| Feld    | Bedeutung |
|---------|---------|
| BELNR   | Belegnummer |
| GJAHR   | Geschäftsjahr |
| LIFNR   | Lieferant |
| BLDAT   | Belegdatum (Datum auf der Rechnung) |
| BUDAT   | Buchungsdatum |
| RMWWR   | Bruttorechnungsbetrag in Belegwährung |
| SPERR   | Sperrkennzeichen |
| ZLSPR   | Zahlungssperre |
| STAT_KZ | Statuskennzeichen, siehe Abschnitt 6 |

### RSEG – Rechnungsposition

Rechnungspositionen mit Bezug zur Bestellposition (EBELN/EBELP).

## 6. Statuskennzeichen STAT_KZ

Das Feld STAT_KZ hält die Verarbeitungsstufe eines Belegs fest. Der
Wertebereich wurde ursprünglich im Projekt PRISMA definiert.

| Wert | Bedeutung |
|-------|---------|
| 10    | Bestellung angelegt |
| 20    | Bestellung freigegeben |
| 30    | Wareneingang teilweise gebucht |
| 34    | Rechnung geprüft und freigegeben |
| 40    | Wareneingang vollständig |
| 50    | Rechnung erfasst |
| 80    | Prozess abgeschlossen |
| 90    | Storniert |

> Anmerkung: Zusätzliche Werte wurden während der Harmonisierung 2019
> eingeführt. Die zugehörige Dokumentation liegt bei der FI-Abteilung und
> ist hier noch nicht eingearbeitet.

## 7. Finanzbuchhaltung (BKPF)

Der Zahllauf erzeugt Belege mit BLART = 'KZ'. Das Feld AUGBL enthält die
Ausgleichsbelegnummer, AUGDT das Ausgleichsdatum. Eine Position gilt als
ausgeglichen, sobald AUGBL gefüllt ist.

AWKEY stellt den Bezug zurück zur auslösenden Rechnungsprüfungstransaktion
her.

## 8. Customizing

- **T161** – Bestellbelegarten.
- **TCURR** – Wechselkurse. GDATU ist das Gültig-ab-Datum. Der anzuwendende
  Kurs ist der jüngste, der am oder vor dem Belegdatum gültig ist.

---

## Noch nicht dokumentiert

Die folgenden Objekte sind bekannt, aber noch nicht beschrieben:

- `ZTFRG` – Eigenentwicklung, Freigabe-Workflow. Kontakt: Herr Weber.
- `ZTSTAT` – Eigenentwicklung, Protokolltabelle.
- `EKET` – Einteilungen.
- Felder `SHKZG`, `XBLNR`, `USNAM` über mehrere Tabellen hinweg.
- Der Wertebereich von `ZLSPR`.
