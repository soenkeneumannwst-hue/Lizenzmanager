# Projektstand

Stand: 18.09.2026 / V43

## Aktueller Funktionsumfang

### Kunden und Lizenzen

- Kundenstamm mit Suche und Aliasen
- automatisch aus Lieferantenbelegen angelegte Kunden werden als **„Stammdaten prüfen“** markiert
- Dubletten können zusammengeführt werden
- Kundenakte zeigt u. a. Lieferanten-/Dongle-Daten und Serviceverträge
- Lizenz- und Vertragsverknüpfung

### Serviceverträge

Ein Vertrag kann enthalten:

- komplette Leistungsgruppen:
  - BusinessCard
  - Softwarepflege
  - Support
- konkrete Einzelprodukte

Beim Rechnungsimport kann eine Position automatisch einem Servicevertrag zugeordnet werden, wenn genau ein aktiver Vertrag die Leistung abdeckt. Bei mehreren möglichen Verträgen bleibt die Position zur Prüfung offen.

### Lieferantenbelege

Unterstützt werden:

- Rechnungen
- Aufträge
- Gutschriften
- Sammelbelege mit mehreren Anwendern/Kunden

Erkannte Zusatzdaten:

- Anwender/Kunde
- Lieferanten-/Anbieter-Kundennummer
- Auftrag Nr. / Auftragsdatum
- Lieferschein Nr. / Datum
- Dongle-ID
- Lizenz-Code
- Leistungszeitraum
- Kontingentnummer und Kontingentverbrauch
- TSE-Seriennummern

### E-Mail-PDF-Import

- IMAP-Import
- Absender-Allowlist
- SHA-256-Deduplizierung
- Dokumenttyp-Erkennung
- Dateiname hat bei `AUFTRAG_...`, `RECHNUNG_...`, `GUTSCHRIFT_...` Vorrang
- Altbestände mit `unknown` werden nach Möglichkeit repariert
- BusinessCard-Gutschriften bleiben offen, solange Vorgänge ungeklärt sind
- Fortschritt z. B. **47/49 erledigt**, erst **49/49** gilt als vollständig abgearbeitet

### PDF-Neuauslesen

Alle archivierten PDFs können mit aktueller Parserlogik erneut ausgewertet werden.

Dabei sollen vorhandene manuelle Produkt-, Preis-, Servicevertrag- und Berechnet-Zuordnungen möglichst erhalten bleiben.

### TSE

TSE-Einkäufe, z. B. Artikel `35181 – TSE Swissbit - USB-Format`, werden erkannt.

- jede Seriennummer wird als eigener TSE-Datensatz angelegt
- TSE kann einem Kunden zugewiesen werden
- Ablaufdatum kann je Charge gemeinsam gesetzt werden
- vorhandenes Ablaufdatum aus dem Beleg wird übernommen
- kein Ablaufdatum wird aus Seriennummern geraten

### Kontingent

CASPOS-Kontingent-Aufwertungen und -Verbräuche werden getrennt behandelt.

Aktuelles Jahresmodell:

- Jahreskontingent 2026: **17.880,00 €**
- Zahlung in **12 Raten à 1.490,00 €**
- Monatsrate ist **keine monatliche neue Aufwertung**
- Verbrauch läuft über das gesamte Kalenderjahr kumuliert
- Gegenbuchung `sonstiges.kontin – Verrechnung mit Betragskontingent Nr. 3` ist der echte Kontingentverbrauch

### Kundenrentabilität

Pro Kunde werden zusammengeführt:

- Service-/Vertragsumsatz
- Zusatzumsatz
- BusinessCard-Provision/Gutschrift
- BusinessCard-Kosten
- Software-/Updatekosten
- Supportkosten
- sonstige Lieferantenkosten
- ausgegebene Sonderboni

Daraus entstehen:

- Umsatz/Ertrag
- direkte Kosten
- Ergebnis
- Marge
- Supportzeit

### Umsatzreport

Seit V43 verwendet der Umsatzreport dieselbe zentrale Umsatzlogik wie die Kundenrentabilität.

Er berücksichtigt:

- Vertragsumsatz
- Weiterberechnung
- Software-/Update-Verkäufe
- berechneten Support
- manuelle Kundenrechnungen/Zusatzumsatz
- BusinessCard-Provisionen/Gutschriften
- ausgegebene Sonderboni

## Wichtige Produktionsprüfung nach Updates

Nach Änderungen an Parser oder Berichtslogik besonders prüfen:

1. Dokumenttyp Rechnung/Auftrag korrekt?
2. Sammelbeleg-Anwender korrekt getrennt?
3. Kontingentverbrauch nur einmal gezählt?
4. manuelle Kunden-/Produktzuordnungen erhalten?
5. Umsatzreport = Kundenrentabilität für denselben Zeitraum?
6. Gutschrift erst erledigt, wenn alle Vorgänge abgearbeitet sind?
