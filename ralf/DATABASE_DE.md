# Datenbankmodell: Purchase-to-Pay (ERP Legacy)

Dieses Dokument beschreibt das relationale Datenmodell des Purchase-to-Pay (P2P)-Kerns in der SQLite-Datenbank `data/erp_legacy.db`, basierend auf `data/schema.sql` sowie der tatsächlichen Datenbankstruktur.

---

## 1. Übersicht der 14 Kern-Tabellen

| Tabelle | SAP-Kürzel | Fachliche Bezeichnung | Zeilenanzahl (DB) | Primärschlüssel (PK) |
|---|---|---|---|---|
| **LFA1** | Kreditorenstamm | Lieferanten-Stammdaten | 340 | `LIFNR` |
| **MARA** | Materialstamm | Material-Stammdaten allgemein | 2.000 | `MATNR` |
| **T161** | Belegarten Einkauf | Customizing-Tabelle Bestellarten | 4 | `BSART` |
| **TCURR** | Wechselkurse | Währungsumrechnungskurse | 8 | `FCURR, TCURR, GDATU` |
| **EKKO** | Einkaufsbelegkopf | Bestellung Kopfdaten | 15.000 | `EBELN` |
| **EKPO** | Einkaufsbelegposition | Bestellung Positionsdaten | 52.846 | `EBELN, EBELP` |
| **EKET** | Einteilungen | Lieferabrufe / Liefertermine | 52.846 | `EBELN, EBELP, ETENR` |
| **MKPF** | Materialbelegkopf | Wareneingang Kopfdaten | 12.244 | `MBLNR, MJAHR` |
| **MSEG** | Materialbelegsegment | Wareneingang Positionen | 41.017 | `MBLNR, MJAHR, ZEILE` |
| **RBKP** | Rechnungskopf | Eingangsrechnung Kopfdaten | 8.982 | `BELNR, GJAHR` |
| **RSEG** | Rechnungsposition | Eingangsrechnung Positionen | 30.108 | `BELNR, GJAHR, BUZEI` |
| **BKPF** | Buchhaltungsbeleg | Finanzbuchhaltung Belegkopf / Zahlung | 2.949 | `BUKRS, BELNR, GJAHR` |
| **ZTFRG** | Freigabe-Workflow | Custom-Tabelle Bestellfreigaben | 14.401 | `FRGID` |
| **ZTSTAT** | Statushistorie | Custom-Audit-Log für Statuswechsel | 77.330 | `LOGID` |

---

## 2. Mermaid Entity-Relationship-Diagramm (ERD)

```mermaid
erDiagram
    direction TB

    %% Stammdaten & Customizing
    LFA1 ||--o{ EKKO : "Lieferant (LIFNR)"
    LFA1 ||--o{ RBKP : "Rechnungsaussteller (LIFNR)"
    MARA ||--o{ EKPO : "Material (MATNR)"
    MARA ||--o{ MSEG : "Material (MATNR)"
    MARA ||--o{ RSEG : "Material (MATNR)"
    T161 ||--o{ EKKO : "Bestellart (BSART)"

    %% Einkauf (PO)
    EKKO ||--|{ EKPO : "enthält Positionen (EBELN)"
    EKKO ||--o{ ZTFRG : "Freigabehistorie (EBELN)"
    EKPO ||--|{ EKET : "Einteilungen (EBELN, EBELP)"

    %% Wareneingang (GR)
    MKPF ||--|{ MSEG : "enthält Positionen (MBLNR, MJAHR)"
    EKPO ||--o{ MSEG : "Wareneingang zu Bestellpos (EBELN, EBELP)"

    %% Rechnungsprüfung (IV)
    RBKP ||--|{ RSEG : "enthält Positionen (BELNR, GJAHR)"
    EKPO ||--o{ RSEG : "Rechnung zu Bestellpos (EBELN, EBELP)"

    %% Finanzbuchhaltung / Zahlung (FI)
    RBKP ||--o| BKPF : "Ausgleich/Zahllauf (AWKEY = BELNR + GJAHR)"

    %% Statusprotokoll (Polymorph über OBJTY + OBJKY)
    EKKO ||--o{ ZTSTAT : "OBJTY='EKKO' (OBJKY=EBELN)"
    RBKP ||--o{ ZTSTAT : "OBJTY='RBKP' (OBJKY=BELNR)"

    %% Tabellendefinitionen
    LFA1 {
        string LIFNR PK "Lieferantennummer (10-stellig)"
        string MANDT "Mandant"
        string NAME1 "Lieferantenname"
        string LAND1 "Ländercode (ISO)"
        string ORT01 "Ort"
        string PSTLZ "Postleitzahl"
        string SPERR "Sperrkennzeichen"
        string ERDAT "Anlagedatum"
        string KTOKK "Kontengruppe"
    }

    MARA {
        string MATNR PK "Materialnummer (18-stellig)"
        string MANDT "Mandant"
        string MTART "Materialart (ROH, HALB)"
        string MATKL "Warengruppe"
        string MEINS "Basismengeneinheit"
        string MAKTX "Materialkurztext"
        string ERSDA "Erfassungsdatum"
        string LVORM "Löschvormerkung"
    }

    T161 {
        string BSART PK "Bestellbelegart (NB, UB, FO, ZRB)"
        string MANDT "Mandant"
        string BATXT "Belegartentext"
    }

    TCURR {
        string FCURR PK "Von-Währung"
        string TCURR PK "Nach-Währung"
        string GDATU PK "Gültig-ab-Datum"
        string MANDT "Mandant"
        real UKURS "Umrechnungskurs"
    }

    EKKO {
        string EBELN PK "Einkaufsbelegnummer"
        string MANDT "Mandant"
        string BUKRS "Buchungskreis (1000)"
        string BSART FK "Belegart -> T161"
        string LIFNR FK "Lieferant -> LFA1"
        string EKGRP "Einkäufergruppe"
        string WAERS "Belegwährung"
        string AEDAT "Anlagedatum"
        string ERNAM "Angelegt von"
        string FRGKE "Freigabekennzeichen"
        string STAT_KZ "Statuskennzeichen"
    }

    EKPO {
        string EBELN PK "Einkaufsbelegnummer -> EKKO"
        string EBELP PK "Positionsnummer"
        string MANDT "Mandant"
        string MATNR FK "Material -> MARA"
        string WERKS "Werk"
        real MENGE "Bestellmenge"
        string MEINS "Bestellmengeneinheit"
        real NETPR "Nettopreis"
        integer PEINH "Preiseinheit"
        string ELIKZ "Endlieferungskennzeichen"
        string LOEKZ "Löschkennzeichen"
    }

    EKET {
        string EBELN PK "Einkaufsbelegnummer -> EKPO"
        string EBELP PK "Positionsnummer -> EKPO"
        string ETENR PK "Einteilungszeile"
        string MANDT "Mandant"
        string EINDT "Liefertermin"
        real MENGE "Liefermenge"
    }

    MKPF {
        string MBLNR PK "Materialbelegnummer"
        string MJAHR PK "Materialbelegjahr"
        string MANDT "Mandant"
        string BLDAT "Belegdatum"
        string BUDAT "Buchungsdatum"
        string USNAM "Benutzername"
        string XBLNR "Referenzbeleg"
    }

    MSEG {
        string MBLNR PK "Materialbelegnummer -> MKPF"
        string MJAHR PK "Materialbelegjahr -> MKPF"
        string ZEILE PK "Positionszeile im Materialbeleg"
        string MANDT "Mandant"
        string BWART "Bewegungsart (101=WE)"
        string MATNR FK "Material -> MARA"
        string WERKS "Werk"
        real ERFMG "Erfasste Menge"
        string ERFME "Erfassungsmengeneinheit"
        string EBELN FK "Bestellung -> EKPO"
        string EBELP FK "Bestellposition -> EKPO"
        string SHKZG "Soll/Haben-Kennzeichen (S/H)"
    }

    RBKP {
        string BELNR PK "Rechnungsbelegnummer"
        string GJAHR PK "Geschäftsjahr"
        string MANDT "Mandant"
        string LIFNR FK "Lieferant -> LFA1"
        string BLDAT "Belegdatum (Rechnungsdatum)"
        string BUDAT "Buchungsdatum"
        string WAERS "Währung"
        real RMWWR "Bruttorechnungsbetrag"
        string SPERR "Sperrkennzeichen"
        string ZLSPR "Zahlungssperre"
        string USNAM "Erfasser"
        string XBLNR "Rechnungsnummer Lieferant"
        string STAT_KZ "Statuskennzeichen (z. B. 34)"
    }

    RSEG {
        string BELNR PK "Rechnungsbelegnummer -> RBKP"
        string GJAHR PK "Geschäftsjahr -> RBKP"
        string BUZEI PK "Buchungszeile"
        string MANDT "Mandant"
        string EBELN FK "Bestellung -> EKPO"
        string EBELP FK "Bestellposition -> EKPO"
        string MATNR FK "Material -> MARA"
        real MENGE "Fakturierte Menge"
        real WRBTR "Positionsbetrag in Belegwährung"
        string SHKZG "Soll/Haben-Kennzeichen"
    }

    BKPF {
        string BUKRS PK "Buchungskreis"
        string BELNR PK "FI-Belegnummer"
        string GJAHR PK "Geschäftsjahr"
        string MANDT "Mandant"
        string BLART "Belegart (KZ = Zahllauf)"
        string BLDAT "Belegdatum"
        string BUDAT "Buchungsdatum"
        string WAERS "Währung"
        string AWKEY FK "Referenzschlüssel (BELNR || GJAHR)"
        string AUGBL "Ausgleichsbelegnummer"
        string AUGDT "Ausgleichsdatum"
        real DMBTR "Betrag in Hauswährung (EUR)"
    }

    ZTFRG {
        string FRGID PK "Freigabe-ID"
        string MANDT "Mandant"
        string EBELN FK "Bestellnummer -> EKKO"
        string FRGST "Freigabestatus"
        string FRGCO "Freigabecode"
        string FRGDT "Freigabedatum"
        string FRGUS "Freigebender Benutzer"
        string FRGBE "Bemerkung"
    }

    ZTSTAT {
        integer LOGID PK "Laufende Protokoll-ID"
        string MANDT "Mandant"
        string OBJTY "Objekttyp ('EKKO', 'RBKP')"
        string OBJKY "Objektschlüssel (EBELN, BELNR)"
        string STAT_ALT "Vorheriger Status"
        string STAT_NEU "Neuer Status"
        string CPUDT "Änderungsdatum"
        string CPUTM "Änderungsuhrzeit"
        string USNAM "Benutzer"
    }
```

---

## 3. Die fachlichen Kernbeziehungen (Join-Pfade)

### 3.1 Three-Way Match (Bestellung -> Wareneingang -> Rechnung)
Das Herzstück des P2P-Prozesses ist der Abgleich zwischen Bestellung, Wareneingang und Eingangsrechnung:

1. **Bestellung (`EKKO`/`EKPO`)**:
   - `EKKO.EBELN = EKPO.EBELN`
2. **Wareneingang (`MKPF`/`MSEG`)**:
   - Verknüpfung zur Bestellposition über:
     ```sql
     MSEG.EBELN = EKPO.EBELN AND MSEG.EBELP = EKPO.EBELP
     ```
   - Standard-Bewegungsart für Wareneingang zur Bestellung: `MSEG.BWART = '101'`.
3. **Rechnungseingang (`RBKP`/`RSEG`)**:
   - Verknüpfung zur Bestellposition über:
     ```sql
     RSEG.EBELN = EKPO.EBELN AND RSEG.EBELP = EKPO.EBELP
     ```

### 3.2 Ausgleich & Bezahlung (`RBKP` -> `BKPF`)
Ein Buchhaltungsbeleg (`BKPF`) verknüpft sich über das zusammengesetzte Feld `AWKEY`:
```sql
BKPF.AWKEY = RBKP.BELNR || RBKP.GJAHR
```
- Zahllauf-Belege haben `BKPF.BLART = 'KZ'`.
- Eine Rechnung gilt als **vollständig ausgeglichen / bezahlt**, sobald `BKPF.AUGBL` gefüllt ist.

### 3.3 Freigaben & Historie (`ZTFRG` / `ZTSTAT`)
- **Freigaben**: `ZTFRG.EBELN = EKKO.EBELN` dokumentiert, wer wann welche Freigabestufe erteilt hat.
- **Statusverlauf**: `ZTSTAT` protokolliert Statuswechsel polymorph:
  - Für Bestellungen: `ZTSTAT.OBJTY = 'EKKO' AND ZTSTAT.OBJKY = EKKO.EBELN`
  - Für Rechnungen: `ZTSTAT.OBJTY = 'RBKP' AND ZTSTAT.OBJKY = RBKP.BELNR`

---

## 4. Häufige Fallstricke bei Abfragen

1. **Gültige Bestellpositionen (`EKPO`)**:
   - Gelöschte Positionen haben `LOEKZ IS NOT NULL` (z. B. `'L'` oder `'X'`).
   - Abgeschlossene Lieferungen tragen `ELIKZ = 'X'`.
   - Der Positionswert errechnet sich aus `(NETPR * MENGE / PEINH)`. Achtung: `PEINH` kann `> 1` sein (z. B. Preis pro 100 oder 1000 Stück).
2. **Rechnungsprüfung / Hängende Rechnungen (`RBKP`)**:
   - Nach der Systemharmonisierung 2019 bedeutet `RBKP.STAT_KZ = '34'`: *Rechnung in Prüfung / blockiert* (2.227 Belege).
   - Das Feld `SPERR = 'X'` kennzeichnet Prüfungssperren.
   - `ZLSPR` enthält Zahlungssperren im Zahllauf.
3. **Lieferantensperren (`LFA1`)**:
   - `LFA1.SPERR = 'X'` sperrt den Lieferanten generell für Neubestellungen.
