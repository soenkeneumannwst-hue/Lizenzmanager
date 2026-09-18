# Belegimport und Abgleich

## Dokumenttypen

Der Dokumenttyp wird aus mehreren Signalen bestimmt. Eindeutige Dateinamen haben Vorrang:

- `RECHNUNG_...` → Rechnung
- `AUFTRAG_...` → Auftrag
- `GUTSCHRIFT_...` → Gutschrift

Wichtig: Text wie **„Übernahme von Auftrag Nr.“** innerhalb einer Rechnung macht den Beleg nicht zum Auftrag.

Als zusätzliche Plausibilitätsregel gelten bei CASPOS/EUCASOFT typischerweise:

- Belegnummer `4xxxxxxx` → Rechnung
- Belegnummer `2xxxxxxx` → Auftrag

Diese Regel dient als Reparatur-/Fallbacklogik und ersetzt nicht die eigentliche Dokumentüberschrift bzw. den eindeutigen Dateinamen.

## Auftrag → Rechnung

Aufträge werden als Vorstufe gespeichert.

Ein Auftrag:

- darf noch keine endgültige Weiterberechnung auslösen
- wartet auf die spätere Rechnung
- kann Leistungswerte, Anwender, Dongle- und Lizenzdaten enthalten

Kommt die Rechnung später, wird über **„Übernahme von Auftrag Nr.“** verknüpft.

Die spätere Rechnung:

- bestätigt den Lieferantenvorgang
- ist Grundlage für Kosten-/Servicevertrag-/Weiterberechnungsprüfung
- darf den Auftrag nicht doppelt als Kosten erfassen

Der Abgleich muss auch rückwirkend funktionieren, unabhängig davon, ob zuerst Auftrag oder Rechnung importiert wurde.

## Sammelbelege

Ein Beleg kann mehrere Anwender enthalten.

Regel:

- `Anwender:` gilt positionsbezogen
- jede Position muss dem korrekten Kunden/Debitor zugeordnet werden
- bekannte Kunden werden wiederverwendet
- unbekannte Anwender können automatisch als Kunde angelegt werden
- 2 bekannte + 2 unbekannte Anwender bedeutet: 2 zuordnen + 2 neu anlegen, nicht den gesamten Beleg nur einem Kunden zuordnen

## Kundenmatching

Prioritäten:

1. gespeicherte Anbieter-Kundennummer
2. Kunde/Alias
3. tolerant normalisierter Name

Normalisierung berücksichtigt u. a.:

- ä ↔ ae
- ö ↔ oe
- ü ↔ ue
- ß ↔ ss
- `&`, `und`, `/`, `+`
- Zusätze wie `Inh.`
- zusätzliche Orts-/Firmenbestandteile

Einmal gelernte Anbieter-Kundennummern werden dauerhaft gespeichert.

## BusinessCard-Gutschriften

Die Gutschrift kann viele Kundenpositionen enthalten.

Pro Position werden z. B. erkannt:

- Vertragsnummer
- zugehörige Rechnung
- Anbieter-Kundennummer
- Kunde
- Abrechnungszeitraum
- Gutschriftbetrag

Eine Gutschrift ist im E-Mail-Arbeitskorb erst erledigt, wenn alle Positionen fachlich verarbeitet sind, z. B. **49/49 erledigt**.

### Automatische BusinessCard-Anlage

Wenn der Kunde eindeutig erkannt ist und keine passende BusinessCard existiert:

- BusinessCard automatisch anlegen
- Startdatum = erste Sichtung in den Gutschriften
- 5,00 € Basisprovision → Basis-BusinessCard
- 10,00 €, 15,00 € usw. → ebenfalls anlegen, aber **„Module prüfen“**
- beobachtete Provision speichern
- keine Module erfinden

## Kontingent-Gegenbuchungen

Beispiel:

`sonstiges.kontin – Verrechnung mit Betragskontingent Nr. 3 –351,00 €`

Regel:

- absoluter negativer Betrag = Kontingentverbrauch
- als eigene Kontingentbewegung speichern
- nicht als eigenständiges Produkt weiterberechnen
- nicht ein zweites Mal als Kosten zählen
- mit der zugehörigen positiven Leistungsposition logisch verknüpfen

## TSE-Einkäufe

Bei TSE-Artikeln werden Seriennummern einzeln extrahiert.

Eine Position mit Menge 10 und 10 Seriennummern erzeugt 10 TSE-Bestandsdatensätze.

## Neuauslesen

Funktion **„Alle archivierten PDFs neu auslesen“**:

- nutzt aktuelle Parserlogik
- verarbeitet Fehler je PDF isoliert
- kann nach aktueller PDF gestoppt werden
- Belege ohne archivierte Original-PDF werden übersprungen
- manuelle Entscheidungen sollen soweit möglich erhalten bleiben

Wichtig: DDL-/Schemaänderungen dürfen nicht innerhalb einer laufenden Belegtransaktion ausgeführt werden, da MySQL/MariaDB sonst einen impliziten Commit auslösen kann.
