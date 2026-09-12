# Postfach-Export – Kreditorenbuchhaltung / Einkauf

Export ausgewählter Mail-Threads, bereitgestellt von der Fachabteilung für
die Initiative Prozessklarheit. Personenbezogene Daten wurden reduziert. Die
Threads stehen in der Reihenfolge, in der sie geliefert wurden, und sind
nicht vollständig.

---

## Thread 1 – „Rechnungsliste für das Montags-Review“

**Von:** Sandra Weber (Kreditorenbuchhaltung)
**An:** Kai Mueller (Einkaufscontrolling)
**Datum:** 2025-09-02

Kai,

ich brauche wieder die Liste der hängenden Rechnungen für das Montags-Review.
Wie letzten Monat bitte: alles, was in der Prüfung sitzt und noch nicht zur
Zahlung freigegeben ist, mit Lieferant und Betrag.

Bitte nimm nicht einfach alles Unbezahlte – letztes Mal standen Bestellungen
drin, die nie genehmigt waren, und Thomas hat das vor allen auseinandergenommen.

Sandra

**Von:** Kai Mueller
**Datum:** 2025-09-02

Sandra,

ich stelle das zusammen. Nur damit du Bescheid weißt: Die Zahlen werden nicht
zu dem passen, was der alte Bericht geliefert hat. Ich habe bei IT nachgefragt,
und die Statuscodes im Datenkatalog sind nicht das, was das System tatsächlich
tut. Das Dokument ist von 2018 und wurde nach der Migration nie korrigiert.

Ich baue die Liste stattdessen aus dem Änderungsprotokoll, das spiegelt
wenigstens wider, was mit einem Beleg wirklich passiert ist.

Kai

---

## Thread 2 – „Frage zum Lieferantenreport“

**Von:** Julia Hofmann (Einkauf)
**An:** Kai Mueller
**Datum:** 2025-07-18

Kai, dein Report zeigt für Q2 ein Bestellvolumen von 4,2 Millionen, meine
eigene Zahl liegt bei etwa 3,9. Woher kommt der Unterschied?

**Von:** Kai Mueller
**Datum:** 2025-07-18

Zwei Dinge, und beide beißen irgendwann jeden.

Erstens, gelöschte Positionen. Wenn ein Einkäufer eine einzelne Zeile
storniert, wird die Zeile nicht entfernt, sie wird nur gekennzeichnet. Filterst
du die nicht raus, zählst du Geschäft, das nie stattgefunden hat.

Zweitens, das Preisfeld. Der Nettopreis ist nicht pro Stück, er ist pro
Preiseinheit, und die Preiseinheit ist nicht immer eins. Bei vielen unserer
C-Teile ist sie 100 oder 1000. Multiplizierst du Menge mal Preis, ohne durch
die Preiseinheit zu teilen, landest du bei Zahlen, die um Größenordnungen
danebenliegen.

Kai

---

## Thread 3 – „Lieferant Steinwerk gesperrt?“

**Von:** Ralf Bauer (Werk 1100)
**An:** Kreditorenstammdaten
**Datum:** 2025-05-27

Kolleginnen und Kollegen,

der Einkauf sagt mir, Steinwerk sei gesperrt, aber ich habe drei Rechnungen
von denen, die letzten Monat bezahlt wurden. Was gilt jetzt?

**Von:** Kreditorenstammdaten
**Datum:** 2025-05-27

Herr Bauer,

hier liegt ein Missverständnis vor, das ständig vorkommt. Eine
Lieferantensperre und eine Zahlungssperre sind zwei völlig verschiedene
Dinge, und sie liegen an zwei völlig verschiedenen Stellen. Dazu kommt, dass
beide Felder im System denselben Namen tragen.

Steinwerk ist als Lieferant nicht gesperrt. Einzelne ihrer Rechnungen tragen
eine Zahlungssperre wegen Preisdifferenzen. Das Geschäft mit ihnen läuft
normal weiter.

---

## Thread 4 – „USD-Rechnungen im Aging-Report“

**Von:** Markus Keller (Controlling)
**An:** Sandra Weber
**Datum:** 2025-08-11

Sandra,

der Aging-Report summiert in Belegwährung. Wir haben dort USD- und
CNY-Lieferanten. Das zusammenzuzählen ergibt eine Zahl, die gar nichts
bedeutet.

Im System gibt es eine Umrechnungskurstabelle mit einem Gültig-ab-Datum. Der
geltende Kurs ist der letzte, der vor dem Belegdatum gültig war, nicht der
heutige Kurs. Wer die nächste Version dieses Reports baut, bitte das
berücksichtigen.

Markus

---

## Thread 5 – „Übergabenotiz“

**Von:** Thomas Schmidt (IT Applications)
**An:** Anna Fischer
**Datum:** 2025-04-30

Anna,

wie besprochen ein paar Dinge, die du wissen solltest, bevor ich das übergebe.

Der Datenkatalog ist das Dokument, mit dem alle anfangen, und er ist unsere
mit Abstand größte Fehlerquelle. Besonders Abschnitt 6. Die Statuswerte wurden
bei der Harmonisierung 2019 teilweise neu definiert, und das Dokument wurde
nie aktualisiert. Ich habe das mehrfach angemeldet, es wurde nie priorisiert.

Wenn du wissen musst, was ein Statuswert heute tatsächlich bedeutet, schau in
die Protokolltabelle. Sie zeichnet jede Statusänderung mit Zeitstempel auf,
sodass du die tatsächliche Sequenz siehst, die Belege durchlaufen. Diese
Sequenz ist die Wahrheit. Der Katalog ist das, was jemand vor sieben Jahren
aufgeschrieben hat.

Die beiden Custom-Tabellen sind überhaupt nicht dokumentiert. Die
Freigabe-Workflow-Tabelle erklärt sich ziemlich von selbst, sobald man in die
Daten schaut. Die Protokolltabelle ist die wichtige.

Thomas

---

## Thread 6 – „Aw: Aw: Aw: Eskalation Hagenbach“

**Von:** Sandra Weber
**An:** Julia Hofmann
**Datum:** 2025-09-09

Julia,

Hagenbach eskaliert schon wieder. Die sagen, vier Rechnungen seien überfällig.
Ich habe nachgeschaut, alle vier sind in Prüfung, weil die fakturierte Menge
höher ist als das, was wir empfangen haben.

Genau das ist der Fall, den ich immer wieder anspreche. Aus Sicht des
Lieferanten haben sie uns fakturiert und nichts gehört. Aus unserer Sicht ist
die Rechnung in einem vollkommen normalen Verarbeitungszustand. Keine Seite
sieht die Sicht der anderen, deshalb eskaliert es jedes einzelne Mal.

Wenn wir einfach antworten könnten „Ihre Rechnung ist in Prüfung, Grund ist
eine Mengendifferenz, erwartetes Klärungsdatum X“, würden wir uns die Hälfte
dieser Anrufe sparen.

Sandra
