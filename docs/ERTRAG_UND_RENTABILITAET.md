# Ertrag, Kosten und Kundenrentabilität

## Ziel

Für jeden Kunden soll beantwortet werden:

- Welche Umsätze wurden erzielt?
- Welche direkten Lieferantenkosten sind entstanden?
- Wie viel Kontingent wurde für den Kunden eingesetzt?
- Welche BusinessCard-Kosten/Gutschriften gibt es?
- Welche Supportkosten sind angefallen?
- Wurde ein Sonderbonus ausgegeben?
- Welches Ergebnis und welche Marge bleiben?

## Lieferantenleistung und Kontingent

Beispiel:

- `U10000 Update: EUCASOFT Basic`
- Leistungswert / wirtschaftlicher EK: **351,00 €**
- darunter:
  `sonstiges.kontin – Verrechnung mit Betragskontingent Nr. 3 -351,00 €`

Fachliche Bedeutung:

- wirtschaftliche Kosten der Leistung: **351,00 €**
- Zahlung an CASPOS: über Kontingent Nr. 3
- Kontingentverbrauch: **351,00 €**
- Gegenbuchung ist keine zweite Kostenposition

Der auf der Lieferantenrechnung verbleibende Zahlbetrag kann 0,00 € oder nur 10,00 € Belegpauschale sein. Das ändert nichts am wirtschaftlichen Leistungswert.

## Jahreskontingent

Für 2026 gilt aktuell:

- Jahreskontingent: **17.880,00 €**
- 12 Zahlungen à **1.490,00 €**
- Restkontingent = Jahreskontingent + ggf. Übertrag – kumulierter Verbrauch
- fehlende Monatsrate ist eine Rechnungskontrolle, nicht automatisch ein anderes verfügbares Kontingent

Monatliche Karten sollen zeigen:

- Rate erkannt
- Verbrauch im Monat
- Verbrauch kumuliert
- Rest Jahreskontingent

## Kundenpreis für Lieferantenleistungen

Preispriorität:

1. kundenspezifischer gemerkter Preis
2. definierte Preisliste
3. Standard-Fallback: **EK × 2**

Der vorgeschlagene Kundenpreis darf manuell überschrieben werden.

Ein individueller Preis kann für **Kunde + Lieferant + Artikel** gemerkt werden.

## BusinessCard-Kunden und zusätzlicher Rabatt

CASPOS kann bei BusinessCard-Kunden einen zusätzlichen Rabatt gewähren, z. B.:

`BC8100v – Verrechnung Business Card Aktion`

Dieser Rabatt gehört zunächst zu FALKE-KASSEN und senkt nicht automatisch den normalen Verkaufspreis an den Kunden.

Stattdessen kann er als **BusinessCard-Sonderbonus** verwendet werden.

### Sonderbonus-Regel

1. normale Kundenrechnung wird erstellt
2. Kunde zahlt vollständig
3. erst danach wird der zusätzliche BC-Rabatt als Bonus freigegeben
4. Bonus kann separat ausgegeben/verbucht werden
5. ausgegebener Bonus reduziert in der Rentabilität den Ertrag des Kunden

Nicht freigegebene bzw. nur vorgemerkte Boni dürfen den Umsatz noch nicht reduzieren.

## Zusatzumsatz / eigene Kundenrechnung

Wenn FALKE-KASSEN dem Kunden z. B. ein Softwareupdate separat verkauft, kann eine eigene Kundenrechnung erfasst werden.

Felder:

- Rechnungsdatum
- Art, z. B. Software-/Update-Verkauf
- eigene Rechnungsnummer
- Netto-Betrag
- Beschreibung

Diese Rechnung fließt in **Zusatzumsatz** und damit in Umsatz/Ertrag, Ergebnis und Marge ein.

Doppelerfassung vermeiden: Wurde eine Lieferantenposition bereits über die Weiterberechnung als Kundenumsatz erfasst, darf dieselbe Kundenrechnung nicht zusätzlich manuell als Zusatzumsatz erfasst werden.

## Servicevertrag

Wenn ein Produkt im aktiven Servicevertrag enthalten ist:

- Lieferantenkosten bleiben Kosten
- keine zusätzliche Weiterberechnung notwendig, sofern Vertrag die Leistung deckt
- Vertragsumsatz wird separat als Umsatz/Ertrag berücksichtigt

## Berichtsgleichheit

Für denselben Zeitraum muss gelten:

**Summe Umsatz/Ertrag der Kundenrentabilität = Umsatzreport**

Der Umsatzreport darf nicht mehr nur auf alten `transactions`-Daten basieren.

Berücksichtigte Umsatzquellen:

- Vertragsumsatz
- Weiterberechnung
- Software-/Update-Verkauf
- berechneter Support
- manuell erfasste Kundenrechnung
- BusinessCard-Provision/Gutschrift
- abzüglich tatsächlich ausgegebener Sonderboni
