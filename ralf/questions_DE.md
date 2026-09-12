# Entwicklungs-Fragensatz

Das sind die Fragen, die die Fachabteilung tatsächlich stellt. Sie sind so
formuliert, wie ein Mensch sie formulieren würde, nicht so, wie eine
Datenbank es tun würde.

Nutzen Sie diesen Satz, um Ihre Lösung zu kalibrieren. **Die Jury bewertet
gegen einen anderen, verborgenen Fragensatz im gleichen Stil.** Die Antworten
unten fest einzuprogrammieren hilft Ihnen nicht und wird in der Demo offensichtlich.

Zu jeder Frage steht die erwartete Antwort, damit Sie selbst prüfen können.

---

## Aufwärmen – können Sie das Schema überhaupt lesen?

**Q1.** Wie viele unserer Lieferanten sitzen in Deutschland?
> **177** von 340

**Q2.** Wie viele Bestellungen sind insgesamt im System?
> **15.000**

**Q3.** Welche Bestellbelegarten verwenden wir?
> **NB** Standardbestellung, **UB** Umlagerungsbestellung,
> **FO** Rahmenbestellung, **ZRB** Lieferplanabruf

---

## Kern – können Sie Prozesszustände abbilden?

**Q4.** Wie viele Lieferantenrechnungen hängen derzeit in der Prüfung?
> **2.227**
> Das ist die mit Abstand wichtigste Frage in diesem Fall. „Hängt in der
> Prüfung“ bedeutet: Die Rechnung wurde erfasst und ist in die Prüfung
> eingetreten, wurde aber nicht zur Zahlung freigegeben und nicht bezahlt.

**Q5.** Wie viele Bestellungen warten noch auf Freigabe?
> **1.136**

**Q6.** Bei wie vielen Bestellungen ist nur eine Teillieferung eingegangen?
> **1.731**

**Q7.** Bei wie vielen Rechnungen ist der Three-Way Match fehlgeschlagen und
muss eine Sachbearbeitung sie ansehen?
> **1.510**
> Beachten Sie: Das ist eine andere Menge als Q4. Q4 ist die normale
> Verarbeitung, Q7 ist der Ausnahmezweig.

**Q8.** Wie viele Rechnungen sind zur Zahlung freigegeben, aber noch nicht
bezahlt?
> **1.069**

---

## Fortgeschritten – bekommen Sie die Zahlen richtig hin?

**Q9.** Wie hoch ist der Gesamtwert der hängenden Rechnungen aus Q4, in Euro?
> **294.814.064,51 EUR**
> Nicht alle Rechnungen sind in Euro. Die Umrechnung nutzt die
> Wechselkurstabelle und nimmt den jüngsten Kurs, der am oder vor dem
> Buchungsdatum gültig ist.

**Q10.** Welche fünf Lieferanten haben den höchsten Wert in hängenden
Rechnungen gebunden?
> 1. Rosenthal Technologies AG – 12 Rechnungen, 2.886.307,65 EUR
> 2. Auerbach Industrial Systems KG – 16 Rechnungen, 2.688.879,21 EUR
> 3. Birkenfeld Manufacturing KG – 16 Rechnungen, 2.400.566,44 EUR
> 4. Kaltbrunn Components GmbH – 14 Rechnungen, 2.281.376,75 EUR
> 5. Pflueger Components GmbH – 8 Rechnungen, 2.205.928,58 EUR

**Q11.** Wie hoch ist der gesamte Nettowert aller Bestellpositionen, die
tatsächlich gültig sind?
> **1.998.331.067,40 EUR**
> Wenn Ihre Antwort in den Hunderten von Milliarden liegt, sind Sie in zwei
> Fallen gleichzeitig getappt. Beide sind irgendwo in den Kontextquellen
> beschrieben.

**Q12.** Welche Lieferanten sind gesperrt?
> **29 Lieferanten**, darunter Eichstaedt Technologies AG, Neuhaus
> Technologies AG, Pflueger Automotive GmbH, Sonntag Manufacturing KG und
> Zellweger Polymers GmbH.
> Vorsicht: 3.737 Rechnungen tragen eine Zahlungssperre. Das ist ein
> anderes Konzept und eine andere Frage. Wenn Sie 3.737 geantwortet haben,
> haben Sie die falsche Frage beantwortet.

**Q13.** Wie lange verbringt eine Rechnung durchschnittlich in der Prüfung?
> **7,36 Tage**
> Die einzige zuverlässige Quelle dafür ist die Verarbeitungshistorie, nicht
> der aktuelle Zustand der Belege.

**Q14.** Bei wie vielen Bestellungen haben wir die gesamte Ware erhalten,
aber nie eine Rechnung bekommen?
> **1.464**

**Q15.** Welche Rechnung sitzt am längsten in der Prüfung?
> Beleg **0005103537**, Lieferant Neuhaus Technologies AG, gebucht
> 2025-01-20, 290.262,48 EUR.

---

## Was gut aussieht

Eine starke Antwort liefert mehr als eine Zahl. Bei Q4 will das Geschäft
nicht nur „2.227“ – es will wissen, welche Rechnungen, von welchen
Lieferanten, wie viel Geld gebunden ist, wie lange sie dort sitzen und warum.

Und es will das prüfen können. Eine Antwort, die niemand zu den
zugrundeliegenden Belegen zurückverfolgen kann, ist in einer
Finanzabteilung sehr wenig wert.
