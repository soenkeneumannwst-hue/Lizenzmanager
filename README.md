# Lizenzmanager – FALKE-KASSEN

Zentrale Projektdokumentation für den Lizenzmanager von FALKE-KASSEN.

**Produktionssystem:** `https://www.pc-kassen.net/lizenzmanager/`  
**Dokumentationsstand:** 18.09.2026  
**Aktueller dokumentierter Patchstand:** V80

## Zweck

Der Lizenzmanager verbindet Kunden, Lizenzen, Verträge, BusinessCards, Lieferantenbelege, Softwarepflege, Support, TSE-Bestand, Kontingente und Kundenrentabilität.

Ziel ist eine durchgängige Sicht vom Lieferantenbeleg bis zur Kundenmarge:

**Lieferantenbeleg → Kunde → Produkt/Vertrag → Weiterberechnung/Service → Zahlung/Bonus → Umsatz/Kosten/Marge**

## Hauptmodule

- **BusinessCard** – Karten, Provisionen/Gutschriften, Anbieter-Kundennummern, Module und Laufzeiten
- **Stammdaten** – Kunden, Produkte, Preise, TSE-Bestand
- **Vertrieb** – Verträge, Zusatzumsätze/Kundenrechnungen, Sonderboni
- **Wartung** – Service-/Updateverträge und Support
- **Einkauf** – E-Mail-PDF-Import, Eingangsrechnungen, Weiterberechnung, Aufgaben
- **Berichte** – Umsatzreport, Kundenrentabilität, Kontingentverbrauch

## Verbindliche Grundsätze

1. **Eine fachliche Wahrheit:** Umsatzreport und Kundenrentabilität verwenden dieselben Rechenwege.
2. **Lieferantenkosten bleiben sichtbar**, auch wenn sie durch ein Jahreskontingent auf der Rechnung auf 0,00 € verrechnet werden.
3. **Kontingentgegenbuchungen sind keine zweite Kostenposition.**
4. **Aufträge sind Vorstufen.** Erst die zugehörige Rechnung löst den endgültigen Kosten-/Weiterberechnungsprozess aus.
5. **BusinessCard-Zuordnungen werden dauerhaft über Anbieter-Kundennummern gelernt.**
6. **Manuelle Entscheidungen haben Vorrang** und sollen beim erneuten PDF-Auslesen möglichst erhalten bleiben.
7. Datenbank-Erweiterungen werden – soweit im Patch vorgesehen – automatisch angelegt; keine manuellen SQL-Schritte ohne ausdrückliche Dokumentation.

## Dokumentation

- [Projektstand](docs/PROJEKTSTAND.md)
- [Belegimport und Abgleich](docs/BELEGIMPORT_UND_ABGLEICH.md)
- [Ertrag, Kosten und Kundenrentabilität](docs/ERTRAG_UND_RENTABILITAET.md)
- [Datenmodell](docs/DATENMODELL.md)
- [Entscheidungen und Regeln](docs/ENTSCHEIDUNGEN.md)
- [Änderungshistorie](docs/CHANGELOG.md)

## Pflege

Neue fachliche Entscheidungen, Parserregeln, Datenbankänderungen und bestätigte Fehlerbehebungen sollen ab jetzt in diesem Repository dokumentiert werden. Offene Fehler oder Entwicklungsaufgaben können zusätzlich als GitHub Issues geführt werden.
