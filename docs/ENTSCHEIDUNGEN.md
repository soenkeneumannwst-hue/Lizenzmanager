# Verbindliche Entscheidungen und Regeln

## 1. Dokumenttyp

- `RECHNUNG_...` ist Rechnung.
- `AUFTRAG_...` ist Auftrag.
- `GUTSCHRIFT_...` ist Gutschrift.
- „Übernahme von Auftrag Nr.“ innerhalb einer Rechnung ist nur eine Referenz.

## 2. Aufträge

Aufträge lösen keine endgültige Weiterberechnung aus. Die spätere Rechnung ist führend.

## 3. Sammelbelege

Jede Position wird über den zugehörigen `Anwender:` einem Kunden zugeordnet.

## 4. Kundenmatching

Anbieter-Kundennummer hat Vorrang vor Namensheuristik. Einmal bestätigte Zuordnungen werden dauerhaft gemerkt.

## 5. BusinessCard

Wenn Kunde eindeutig erkannt und keine passende BusinessCard vorhanden:

- automatisch anlegen
- Startdatum = erste Sichtung
- 5 € → Basis
- andere Provision → anlegen + „Module prüfen“
- keine Module raten

## 6. Kontingent

- Kontingentverbrauch = absoluter Wert der negativen `sonstiges.kontin`-Gegenbuchung
- Gegenbuchung ist keine zweite Kostenposition
- 2026: 17.880 € Jahreskontingent, 12 × 1.490 €
- Verbrauch wird über das ganze Jahr kumuliert

## 7. Lieferantenleistung und Verkaufspreis

Standardpreisfolge:

**kundenspezifischer Preis → Preisliste → EK × 2**

Manuelle Preise dürfen gespeichert und wieder vorgeschlagen werden.

## 8. BusinessCard-Sonderbonus

Zusätzlicher BusinessCard-Rabatt des Softwarehauses wird nicht automatisch an den Kunden weitergegeben.

Er kann als Sonderbonus ausgegeben werden, aber erst wenn die betreffende Kundenabrechnung vollständig bezahlt ist.

## 9. Kundenrentabilität

Kosten werden dem Kunden nach wirtschaftlicher Ursache zugeordnet, nicht nur nach dem finalen Zahlbetrag des Lieferantenbelegs.

Beispiel: 351 € Update + -351 € Kontingent = 351 € Kosten beim Kunden und 351 € Kontingentverbrauch, nicht 0 € Kosten.

## 10. Berichte

Cloud-/Berichtsseiten innerhalb des Lizenzmanagers müssen dieselben Definitionen und Rechenwege verwenden.

Für denselben Zeitraum muss insbesondere der Umsatzreport zur Summe der Kundenrentabilität passen.

## 11. Gutschriften-Arbeitskorb

Eine BusinessCard-Gutschrift gilt erst als erledigt, wenn alle erkannten Vorgänge fachlich verarbeitet sind, z. B. 49/49.

## 12. PDF-Neuauslesen

Neuparsen darf vorhandene manuelle Arbeit nicht unnötig überschreiben.

## 13. Datenbankschema

DDL wie `CREATE TABLE` oder `ALTER TABLE` darf nicht innerhalb einer aktiven Importtransaktion ausgeführt werden.

## 14. TSE

Seriennummern werden einzeln in den Bestand übernommen und danach einem Kunden zugewiesen. Ablaufdaten werden nur übernommen, wenn sie bekannt sind; keine Schätzung aus der Seriennummer.
