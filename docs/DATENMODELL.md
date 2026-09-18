# Datenmodell – fachliche Übersicht

Diese Datei dokumentiert die im Projekt verwendeten bzw. ergänzten Tabellen auf fachlicher Ebene. Die konkrete Produktionsstruktur kann je Installationsstand weitere Spalten enthalten.

## Kern

### customers
Kundenstamm.

### licenses
Lizenzdaten; Zuordnung zum Kunden über `assigned_customer_id`.

### contracts
Verträge, u. a. Lizenzbezug, Laufzeit, Modell und wiederkehrende Preise.

### products
Eigene Produkte/Leistungsarten. Ergänzt um fachliche Gruppierung `recharge_group`, z. B.:

- businesscard
- softwarepflege
- support

## Servicevertrag-Inhalte

### contract_included_groups
Welche komplette Leistungsgruppe ein Vertrag abdeckt.

Schlüsselidee:
- `contract_id`
- `group_code`

### contract_included_products
Konkrete Produkte, die ein Vertrag abdeckt.

## Lieferantenbelege

### supplier_invoice_imports
Importkopf eines Lieferantenbelegs.

Enthält je nach Stand u. a.:

- Lieferant
- Dokumenttyp
- Belegnummer
- Belegdatum
- archivierte PDF
- Import-/Verarbeitungsstatus

### supplier_invoice_lines
Einzelpositionen.

Fachlich relevante Werte:

- Kunde
- Produkt
- Lieferantenartikel
- Menge
- Leistungswert/EK
- Kundenpreis
- Billing-/Weiterberechnungsstatus
- Servicevertrag
- Anwender
- Auftrag-/Rechnungsreferenzen
- Kontingentinformation

## Wiederkehrende Lieferantenleistungen

Lieferantenverträge werden aus Belegen abgeleitet. Der früheste bekannte Abrechnungsmonat kann den Vertragsbeginn nach vorne korrigieren. Spätere Belege dürfen einen einmal früher erkannten Beginn nicht wieder nach hinten verschieben.

## Kundenmatching

### customer_aliases
Alternative Kundennamen und lieferantenspezifische Schreibweisen.

### customer_import_meta
Kennzeichnet automatisch aus Belegen angelegte Kunden, die noch geprüft werden müssen.

### customer_supplier_assets
Lieferantenseitige technische Daten eines Kunden, z. B.:

- Dongle-ID
- Lizenz-Code
- Auftragsreferenz
- Lieferscheinreferenz

## BusinessCard

BusinessCards werden kundenbezogen geführt und über Anbieter-Kundennummern wiedergefunden.

Zusätzliche aktuelle Fachwerte:

- erste Sichtung / Startdatum
- beobachtete Provision
- Kennzeichen `Module prüfen`
- Anbieter-Kundennummer

Für gelernte Zuordnungen existiert eine dauerhafte Abbildung:

**Anbieter + Anbieter-Kundennummer → eigener Kunde**

## TSE

TSE-Bestand enthält je Gerät/Seriennummer:

- Seriennummer
- Typ
- Lieferantenbeleg
- Einkaufsdatum
- Ablaufdatum
- Kunde
- Notiz

## Kontingent

Fachlich getrennte Bewegungen:

- Jahres-/Monatsraten bzw. Aufwertungsbelege
- Verbrauch durch `sonstiges.kontin`
- optionale Überträge

Ein Verbrauch muss auf Rechnungs-/Positionsbasis dedupliziert werden.

## Zusatzumsatz

Manuell erfasste eigene Kundenrechnungen:

- Kunde
- Datum
- Rechnungsnummer
- Art
- Nettobetrag
- Beschreibung

## Kundenspezifische Preise

Für wiederkehrende Lieferantenartikel kann ein Preis für die Kombination gespeichert werden:

**Kunde + Lieferant + Lieferantenartikel → Kundenpreis**

## Sonderboni

BusinessCard-Sonderbonus wird separat vom normalen Rechnungsumsatz verwaltet.

Statuslogik:

- vorgemerkt
- nach vollständiger Kundenzahlung freigegeben
- ausgegeben

Nur tatsächlich ausgegebene Boni reduzieren die Rentabilität.
