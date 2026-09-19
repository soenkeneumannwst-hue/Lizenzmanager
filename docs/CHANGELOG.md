## V49 – interne PDF-KPI-Tabelle und Diagramm

- Überlappung langer Bezeichnungen/Werte behoben
- zweispaltiges, rechtsbündiges KPI-Raster mit alternierenden Zeilen
- grafische Kalkulationsübersicht direkt unter den Kennzahlen
- Monatsrate, Break-even, Ergebnis 48/72 Monate als Kennzahlenboxen
- Einnahmen, Kosten und Ergebnis als 72-Monats-Diagramm
- interne HW-/TSE-Kalkulationsmarker

## V47 – Interner 48-Monats-Zyklus ohne Hardwaretausch-Zusage

- 48 Monate bleiben interne Rückstellungs-/Kalkulationsbasis
- kein turnusmäßiger Hardwaretausch im Kundenvertrag oder Kunden-PDF
- Reparatur/Austausch bei normalem technischem Defekt bleibt Full-Service-Leistung
- freiwillige Modernisierung funktionsfähiger Hardware liegt im Ermessen von FALKE-KASSEN
- Kundenstatus zeigt keine internen Ersatztermine
- TSE kundenextern nur bei technischer Erforderlichkeit beschrieben

## V46 – Positions-UI und Softwareauswahl

- zweizeilige, farblich abgesetzte Positionsblöcke statt breiter Kalkulationstabelle
- DB-Software-/Modulauswahl nur noch bei Kategorie Software
- Produktbezug wird beim Kategorienwechsel weg von Software automatisch entfernt
- serverseitiger Schutz: product_id nur für Softwarepositionen
- DB-Preisrefresh nur für Softwarepositionen

## V45 – DB-Modulpreise und automatische Softwarepflege

- Button „Preise aus DB & neu kalkulieren“ im Full-Service-Angebot
- Software-/Modulpreise direkt aus products + pricebooks
- Produktbezug je Full-Service-Position
- Softwarepflege verbindlich 15 % vom Software-Neuwert pro Jahr
- DATEV-BusinessCard: 50 % Rabatt auf Softwarepflege plus aktueller BusinessCard-Monatswert aus DB
- BusinessCard-Prüfung bei Kunde/Vertragsstart
- Kalkulationslogik auch in interner PDF und Vertragsrentabilität übernommen

## V44 – Full-Service-Angebote und Mietverträge

- bestehende Update-Angebote bleiben erhalten
- neue Full-Service-/Mietangebote direkt unter Vertrieb > Angebote
- MediaPOS-100-Vorlage mit 48 Monaten Mindestlaufzeit, 39 EUR Serviceanteil, Hardwarezyklus 48 Monate und TSE-Zyklus 60 Monate
- interne EK-, Support-, Austausch- und Rückstellungskalkulation
- serverseitige Kunden- und interne PDF-Erzeugung ohne externe Composer-Abhängigkeit
- PDF-Versionierung
- Status Entwurf → versendet → angenommen → Auftrag → aktiver Vertrag → beendet
- Übernahme angenommener Angebote in Full-Service-Verträge
- Geräte-/Seriennummernverwaltung und Austauschhistorie
- Nachkalkulation tatsächlicher Support-/Hardware-/Software-/TSE-/Fahrtkosten
- Preisänderungshistorie
- tokenisierte Kundenstatus-Seite mit nächstem möglichem Vertragsende und Kündigungstermin
- zentrale Kundenrentabilität noch nicht automatisch mit fs_* Plan-/Ist-Kosten vermischt, um Doppelzählungen gegenüber Lieferantenbelegen zu vermeiden

# Änderungshistorie

Dokumentiert die wesentlichen Entwicklungsstufen des aktuellen Lizenzmanager-Projekts.

## V43 – Umsatzreport identische Logik

- Umsatzreport auf dieselbe zentrale Umsatzlogik wie Kundenrentabilität umgestellt
- 2026 wird auch ohne alte `transactions` angeboten
- Vertragsumsatz, Zusatzumsatz, BC-Gutschrift und Sonderbonus berücksichtigt

## V42 – Preislogik und BC-Sonderbonus

- Standard: kundenspezifischer Preis → Preisliste → EK × 2
- kundenspezifische Preise je Kunde/Lieferant/Artikel merkbar
- BusinessCard-Aktionsrabatt als separater Sonderbonus
- Freigabe des Bonus erst nach vollständiger Kundenzahlung

## V41 – Auftrag-Autoerkennung

- `AUFTRAG_...` automatisch als Auftrag
- `RECHNUNG_...` automatisch als Rechnung
- `GUTSCHRIFT_...` automatisch als Gutschrift
- Reparatur älterer `unknown`-Einträge

## V40 – Gutschrift-Arbeitskorb

- Fortschritt je Gutschrift, z. B. 47/49
- erst bei 49/49 vollständig erledigt

## V39 – BusinessCard automatisch anlegen

- bei eindeutigem Kunden BusinessCard automatisch anlegen
- erste Sichtung = Startdatum
- 10/15 € usw. mit „Module prüfen“
- beobachtete Provision gespeichert

## V38 – Eigene Kundenrechnung / Zusatzumsatz

- separate Software-/Update-Rechnung beim Kunden erfassbar
- fließt in Kundenrentabilität

## V37 – Jahreskontingent

- 17.880 € Jahreskontingent / 12 × 1.490 €
- Monatsrate nicht mehr als monatlich neues Kontingent behandelt
- kumulierter Jahresverbrauch und Rest

## V36 / V35 – Rechnung/Auftrag-Reparatur

- echte Rechnungen mit „Übernahme von Auftrag“ nicht mehr als Auftrag klassifizieren
- falsch gespeicherte Belegtypen reparieren

## V34 / V33 – Kontingentverbrauch

- `sonstiges.kontin` als echte Verbrauchsbewegung
- Teilverrechnungen berücksichtigt
- negativer Gegenbuchungsbetrag ist maßgeblich

## V32 – BusinessCard erste Sichtung

- automatische Basis-BusinessCard bei eindeutigem 5-€-Fall
- Startdatum rückwirkend auf erste bekannte Sichtung

## V31 – Kundenmatching

- Umlaute/Schreibweisen robuster
- alle Kunden auswählbar
- Kunde kann eindeutig zugeordnet sein, auch wenn BusinessCard noch fehlt

## V30 – Anbieter-Kundennummer merken

- dauerhafte Zuordnung Anbieter + Kundennummer → eigener Kunde

## V29 – Transaktionsfehler behoben

- Schemaänderungen aus laufender PDF-Importtransaktion entfernt
- Fehler „There is no active transaction“ beseitigt

## V28 – Kontingent-Aufwertung und TSE

- CASPOS-Kontingent-Aufwertungen erkannt
- TSE-Seriennummern in Bestand übernommen
- Kunden- und Ablaufzuordnung

## V27 – Alle PDFs neu auslesen

- Massenauswertung archivierter PDFs mit Fortschritt und Fehlerisolierung

## V26 – Kontingent-Jahresauswertung

- Jahres-/Monatsauswertung Kontingent
- Drilldown nach Kunde/Produkt/Beleg

## V25 – MySQL Fehler 1093

- problematischen Self-Update-Abgleich auf kompatibles Join-Update umgebaut

## V22 – Serviceverträge, Aufgaben, Dubletten

- Vertragsleistungen nach Gruppen/Produkten
- automatische Servicevertragsdeckung
- zentrale Aufgabenliste
- Kundenaliase und Dublettenzusammenführung

## V20/V21 – Auftrag → Rechnung und E-Mail-Loop

- Auftrag wartet auf Rechnung
- rückwirkendes Matching
- Endlosschleife beim Öffnen einer BusinessCard-Gutschrift aus E-Mail behoben

## V17–V19 – Sammelbelege und automatische Debitoren

- mehrere Anwender je Beleg
- unbekannte Kunden automatisch anlegen
- Dongle-/Lizenz-/Auftrags-/Lieferscheindaten

## V16 – Aufträge

- Auftrags-PDFs und Kontingentgegenbuchungen eingeführt

## V15 – Mail-Sicherheit

- Absender-Allowlist
- SHA-256-Anhang-Deduplizierung

## Frühe Ausbaustufen

V6–V14 führten schrittweise Eingangsrechnungen, Debitorenzuordnung, Weiterberechnung, Produkt-/Preislogik und den lesbaren E-Mail-PDF-Import ein.

Ältere Patchstände V1–V5 sind in dieser Datei nicht vollständig rekonstruiert; maßgeblich ist der aktuelle Fachstand.
