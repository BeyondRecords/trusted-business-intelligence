# Purchase-to-Pay – Prozessbeschreibung

**Dokument:** BPD-P2P-2.3
**Autor:** Externe Beratung, Initiative Prozessklarheit
**Datum:** 2023-06-15
**Zielgruppe:** Fachabteilungen, Interne Revision

Dieses Dokument beschreibt den Soll-Purchase-to-Pay-Prozess, wie er mit
Einkauf und Kreditorenbuchhaltung abgestimmt wurde. Es enthält bewusst keine
Verweise auf technische Systemobjekte. Eine Abbildung auf die zugrunde
liegenden Anwendungstabellen war als Arbeitspaket 4 geplant, wurde aber
aus dem Scope genommen.

---

## 1. Überblick

Der Purchase-to-Pay-Prozess umfasst alles vom Moment, in dem ein Bedarf
entsteht, bis das Geld des Lieferanten das Gebäude verlässt.

```
  Bedarf  ->  Bestellung  ->  Freigabe  ->  Wareneingang
                                                  |
                                                  v
        Zahlung  <-  Zahlungsfreigabe  <-  Rechnungsprüfung
```

## 2. Prozessschritte

### 2.1 Bestellung anlegen

Ein Einkäufer legt eine Bestellung gegen einen Lieferanten an. Die Bestellung
trägt eine oder mehrere Positionen, jeweils mit Material, Menge und
vereinbartem Preis. Zu diesem Zeitpunkt ist die Bestellung auf unserer Seite
noch nicht rechtlich bindend.

### 2.2 Freigabe

Abhängig von Bestellwert und Einkaufsgruppe braucht eine Bestellung die
Freigabe durch einen oder mehrere Genehmiger. Bis die Bestellung vollständig
freigegeben ist, dürfen keine Waren dagegen angenommen werden. Bestellungen
können wochenlang in diesem Zustand bleiben – das ist eine der häufigsten
Ursachen für verspätete Lieferungen.

Die Fachseite nennt eine ungenehmigte Bestellung **„in der Freigabe-
Warteschlange“** oder **„wartet auf Freigabe“**.

### 2.3 Wareneingang

Wenn die Ware physisch im Werk ankommt, bucht das Lager einen Wareneingang.
Ein Eingang kann **teilweise** sein – der Lieferant liefert weniger als
bestellt – oder **vollständig**. Eine Bestellposition gilt erst als
geschlossen, wenn die Lieferung als abgeschlossen gekennzeichnet ist.

Teillieferungen, die nie abgeschlossen werden, heißen **„offene Eingänge“**
und sind ein wiederkehrendes Thema in der monatlichen Operations-Review.

### 2.4 Rechnungsprüfung

Der Lieferant schickt eine Rechnung. Die Kreditorenbuchhaltung prüft sie
gegen die Bestellung und den Wareneingang. Diese Prüfung heißt
**Three-Way Match**:

1. Verweist die Rechnung auf eine gültige Bestellung?
2. Wurde die Ware tatsächlich empfangen?
3. Stimmen Menge und Preis mit dem Vereinbarten überein?

Wenn alle drei übereinstimmen, darf die Rechnung weiterlaufen. Weicht eines
davon ab, wird die Rechnung **gesperrt** und muss mit dem Lieferanten oder
dem Einkäufer geklärt werden. Der häufigste Grund für eine Sperre ist eine
**Preisabweichung** oder eine fakturierte Menge, die die eingegangene Menge
übersteigt.

**Wichtig:** Eine im System erfasste Rechnung ist nicht dasselbe wie eine
zur Zahlung freigegebene Rechnung. Zwischen diesen beiden Zuständen sitzt
der Prüfungsschritt, und Rechnungen verbringen darin routinemäßig Wochen.
Wenn die Fachseite fragt, welche Rechnungen **„hängen“**, meint sie genau
das: erfasst, noch nicht zur Zahlung freigegeben, noch nicht bezahlt.

### 2.5 Zahlungsfreigabe

Sobald die Prüfung abgeschlossen und alle Abweichungen geklärt sind, wird
die Rechnung zur Zahlung freigegeben. Erst ab diesem Punkt ist die Rechnung
für den Zahllauf berechtigt.

### 2.6 Zahlung

Der Zahllauf wählt alle freigegebenen, fälligen Rechnungen aus und begleicht
sie. Der Ausgleich erzeugt einen Buchhaltungsbeleg. Eine Rechnung gilt erst
dann als **bezahlt**, wenn sie durch einen solchen Beleg ausgeglichen wurde.

---

## 3. Sperren – eine Anmerkung zur Terminologie

Das Wort „Sperre“ / „Block“ wird im Alltag locker verwendet und meint
mindestens drei verschiedene Dinge:

- Eine **Lieferantensperre** stoppt jedes neue Geschäft mit einem Lieferanten
  vollständig. Sie wird zentral von Kreditorenstammdaten gesetzt, meist aus
  Compliance-Gründen.
- Eine **Prüfungssperre** wird während der Rechnungsprüfung gesetzt, wenn der
  Three-Way Match fehlschlägt.
- Eine **Zahlungssperre** verhindert, dass eine ansonsten gültige Rechnung
  vom Zahllauf aufgegriffen wird. Sie kann automatisch vom System oder
  manuell von einem Buchhalter gesetzt werden.

Diese sind unabhängig voneinander. Eine Rechnung kann eine Zahlungssperre
tragen, während der Lieferant selbst völlig aktiv ist – und umgekehrt.

---

## 4. Bekannte Schmerzpunkte

Von der Fachseite in den Workshops wiederholt vorgebracht:

- Niemand kann die Frage *„wo genau ist diese Rechnung gerade“* beantworten,
  ohne drei verschiedene Personen anzurufen.
- Das Reporting zu hängenden Rechnungen entsteht manuell in Spreadsheets und
  ist in der Regel zwei Wochen überholt, wenn es verteilt wird.
- Die Bedeutung der Statuswerte im System ist nicht zuverlässig dokumentiert.
  Mehrere Teilnehmende sagten, der bestehende Datenkatalog sei **„seit der
  Migration teilweise falsch“**, aber niemand konnte sagen, welche Teile.
- Stornierte und gelöschte Positionen werden häufig versehentlich in Berichte
  aufgenommen, was das ausgewiesene Bestellvolumen aufbläht.
