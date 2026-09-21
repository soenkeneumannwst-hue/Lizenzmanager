## V75 – Unified Document Pipeline

- E-Mail und Upload verwenden dieselbe Lieferanten-Parsing-/Fachlogik
- CASPOS/EUCASOFT → zentrale Lizenz-/Rechnungsprüfung
- CASPOS/EUCASOFT-Belege zusätzlich in document_intake_items gespiegelt
- QUAD → ausschließlich Dolibarr, kein neuer Lizenzmanager-Kunde, keine Lizenzmanager-Rechnung
- andere Lieferanten standardmäßig Dolibarr-only
- manuelle Dolibarr-ID-Zuordnung für Dolibarr-only-Fälle
- neue QUAD-Importe in supplier_invoices serverseitig gesperrt
- bestehende historische QUAD-Daten werden nicht automatisch gelöscht

## V74 – CASPOS/EUCASOFT Lizenzpreise + Updatekosten

- Hersteller-Artikelnummer an Produkten/Lizenzen ergänzt
- Systemfamilie: EUCASOFT, CASPOS, CASPOS + EUCASOFT, Sonstiges
- Updatefähigkeit pro Produkt/Lizenz schaltbar
- Preisliste zeigt Hersteller-Art.-Nr., System, EK und Neupreis/VK
- zentrale Updatekostenregeln je CASPOS-/EUCASOFT-Softwarehaus
- Berechnung vom aktuellen Neupreis/VK: 1J 15 %, 2J 30 %, 3J 45 %, 4J 60 %, 5J 90 %
- 5-Jahres-Regel bewusst 90 % und nicht linear 75 %
- Schemaerweiterung idempotent über die PHP-Seiten; zusätzliche Migration 074 vorgesehen

## V73 – Dokumentenimport Stabilität / Matching

- Dolibarr-HTTP-500 und HTML-Fehlerseiten kompakt behandeln
- temporäre Dolibarr-Fehler automatisch erneut versuchen
- alte 500-Fehler wieder in Queue einreihen
- bestätigte Dokumentzuordnungen und FALKE-Kundennummern fürs Matching nutzen
- kanonisch mit Dolibarr verknüpften Kunden bei Gleichstand bevorzugen
- TSE-Zertifikatsdatum kann nicht mehr als Rechnungsdatum erscheinen
- Reupload derselben PDF aktualisiert Erkennungsmetadaten ohne Doppelübertragung

## V72 – Ausgangsrechnung: Adresse + TSE

- Empfängerblock sauber in Firma/Ansprechpartner/Straße/PLZ/Ort getrennt
- FALKE-Telefon-/Faxdaten werden nicht mehr als Kundenstraße übernommen
- Ansprechpartner-Feld im Kundenstamm
- TSE Hardware-ID, technische Seriennummer, Ablaufdatum und Hersteller aus Ausgangsrechnung
- TSE wird automatisch dem Rechnungskunden zugeordnet
- TSE-Konflikte werden nicht automatisch umgehängt

## V72 – Ausgangsrechnungen Kundendaten/TSE

- Empfängeradresse positionsbezogen lesen
- Ansprechpartner nicht mehr als Straße übernehmen
- führende Nullen bei Kundennummern nur fürs Matching ignorieren
- vorhandener Dolibarr-Stamm bleibt führend
- TSE Hardware-ID / Seriennummer / Ablaufdatum aus Ausgangsrechnung übernehmen
- TSE dem Kunden zuordnen
- identische PDFs können zur Metadaten-Neuanalyse erneut hochgeladen werden

## V71 – Warteschlangen-Autoworker

- Queue verarbeitet immer den nächsten Beleg frisch aus der DB
- problematischer Einzelbeleg stoppt den Stapel nicht mehr
- automatische Fortsetzung beim Öffnen der Importseite
- automatische Fortsetzung nach Massen-Upload
- neuer Status „Wird verarbeitet“
- stale processing-Einträge werden erneut eingereiht

## V70 – Alias-/Betriebsnamen beim Dokumentenimport

- Dolibarr name_alias in Kundenmatching aufgenommen
- lokale Kundenaliasse werden berücksichtigt
- Treffer desselben Geschäftspartners nach Dolibarr-ID dedupliziert
- Dolibarr wird vor lokaler Kunden-Neuanlage geprüft
- erkannte Betriebsnamen werden nach erfolgreicher Zuordnung als Alias gelernt
- manuelle Auswahl zeigt Aliasnamen an
- bestehende Fehlerbelege können über „erneut prüfen“ neu ausgewertet werden

## V69 – Dokumentenimport Status/Namen

- queued ist nicht mehr gleichzeitig „erledigt“
- nur assigned zeigt erfolgreichen Dolibarr-Abschluss
- Warteschlangen-Belege können einzeln verarbeitet werden
- doppelt erkannte Firmennamen werden sicher normalisiert
- vorhandene Importzeilen werden ohne Kunden-Merge bereinigt

## V68 – Stabiler Dokumenten-Massenimport

- Upload und Dolibarr-Verarbeitung in getrennte Requests aufgeteilt
- Status/Warteschlange für sicher gespeicherte Belege
- Verbindungsabbruch verliert keine bereits hochgeladene Datei mehr
- Upload- und Verarbeitungsfehler getrennt
- automatischer einmaliger Netzwerk-Retry
- Button „Warteschlange verarbeiten“

## V67 – Dokumenten-Massenimport

- zentraler Massenupload für PDF/XML
- automatische Erkennung von Eingangsrechnung, Ausgangsrechnung, Lieferschein und Auftrag
- eingehend/ausgehend getrennt
- Ordnerauswahl und Drag&Drop
- eigene FALKE-Lieferadresse erzwingt manuelle Kundenzuordnung
- Dubletten/unklare Kunden werden nie automatisch neu angelegt
- eindeutige Belege werden als Kundenanhang in Dolibarr gespeichert
- Ausgangsrechnungen können als Dolibarr-Rechnungsentwurf angelegt werden
- E-Rechnungs-XML wird bevorzugt strukturiert ausgewertet

## V65 – QUAD-Rechnung → Dolibarr-Kunde + Dokument

- QUAD automatisch erkennen
- Lieferanschrift als Endkunde auswerten
- ZUGFeRD/Factur-X/XRechnung bevorzugt strukturiert lesen
- fehlenden Endkunden lokal und in Dolibarr automatisch anlegen
- Dolibarr-Kundennummer zurück in den Lizenzmanager übernehmen
- Original-PDF direkt am Dolibarr-Kunden ablegen
- E-Rechnungs-XML zusätzlich am Kunden ablegen
- Syncstatus/Fehler pro Beleg und Kunde protokollieren
- manueller Wiederholungsbutton in der Eingangsrechnung

## V64 – Systemkunden-Abgleich Dolibarr

- SystemKunde als führendes Aktiv-Kennzeichen
- EUCASOFT/CASPOS über Dolibarr-Kategorien
- neue Seite Stammdaten → Systemkunden-Abgleich
- Dolibarr-Kundennummer wird als führende Kundennummer übernommen
- Matching nach Dolibarr-ID, Kundennummer, E-Mail, Name
- eindeutige fehlende aktive Systemkunden können lokal angelegt werden
- Konflikte/Mehrfachtreffer werden nicht automatisch überschrieben
- keine automatische Kundenlöschung

## V63 – TSE Hardware-ID / Seriennummer getrennt

- Hardware-ID und technische TSE-Seriennummer als getrennte Stammdatenfelder
- Hersteller, HW-/SW-Version, Zertifikatsdatum und Kassen-Seriennummer ergänzt
- bestehende TSE-Altwerte automatisch klassifiziert
- Platzhalter wie „1“ vom automatischen Abgleich ausgeschlossen
- Dolibarr-Abgleich prüft Hardware-ID und TSE-Seriennummer getrennt und exakt

## V62 – TSE-Freitext/Präfix-Normalisierung

- TSE-IDs aus Dolibarr-Freitexten extrahieren
- optionale numerische Präfixe wie 607645- beim Vergleich berücksichtigen
- TSE-Kern bleibt exakter Abgleich
- Originalfundstelle im Prüfergebnis anzeigen
- „noch nicht geprüft“ als Scan-Hinweis verdeutlicht

## V61 – TSE-Dolibarr-Abgleich

- exakter TSE-ID-/Seriennummernabgleich mit Dolibarr
- Geschäftspartner/Extrafields und Kundenrechnungen/Positionen werden geprüft
- Serien aus Lizenzdaten werden in den zentralen TSE-Bestand gespiegelt
- eindeutige Zuordnung kann übernommen werden
- Konflikte und Mehrfachtreffer werden nie automatisch überschrieben
- Dolibarr-Rechnungsreferenz und Prüfhistorie am TSE-Datensatz

## V60 – Dolibarr Entity-Helper Fix

- fehlende Funktion dol_entity_id() ergänzt
- PHP-Abbruch direkt nach „Dolibarr Entity-ID“ behoben
- Diagnose-/Aktionsbuttons werden wieder gerendert

## V59 – Dolibarr-Navigation

- System-Menü enthält Dolibarr wieder zuverlässig
- V58/V57 Diagnose kumulativ im Patch enthalten
- _layout_top.php wird mit ausgeliefert

## V58 – Dolibarr Diagnose-UI Fix

- Diagnose-/Aktionsbuttons wieder dauerhaft sichtbar
- Entity-ID 0 klar als Standard
- API-Diagnose als primäre Aktion hervorgehoben
- Konfiguration und Diagnose optisch getrennt

## V57 – Dolibarr API-Diagnose / Entity

- API-Diagnose zeigt tatsächlichen API-Benutzer, Entity, socid und Status
- mode=0/mode=1 sowie paginierte Thirdparty-Aufrufe werden getrennt geprüft
- technische REST-Antworten aufklappbar
- optionale Dolibarr Entity-ID mit DOLAPIENTITY-Header
- Diagnosehinweis für externen Benutzer oder falschen MultiCompany-/Entity-Kontext

## V56 – Dolibarr Geschäftspartner-Vorschau

- Vorschau liest alle Thirdparties mit mode=0
- Typanzeige Kunde / Interessent / Lieferant / Sonstiger
- Summenkarten für alle Geschäftspartner-Typen
- Verbindungstest unabhängig vom Kundenkennzeichen
- automatischer Sync bleibt auf echte Dolibarr-Kunden beschränkt
- Nicht-Kunden werden nicht automatisch lokal importiert

## V55 – Dolibarr Phase 1

- System → Dolibarr mit API-URL, verschlüsseltem API-Key und Verbindungstest
- Kundenabgleich Dolibarr → Lizenzmanager mit fester Thirdparty-ID
- eindeutiges Matching nach ID, E-Mail oder normalisiertem Namen
- manuelle Verknüpfung und gezielte lokale Neuanlage
- Dolibarr-Status in Kundenliste/Kundenakte
- Rechnungen und Zahlungsstatus verknüpfter Kunden lesend anzeigen
- Sync-Protokoll und Aufgabenkarte für API-Fehler
- Phase 1 ohne Schreibzugriffe nach Dolibarr

## V54 – BusinessCard-Lieferantenbonus klar benannt

- Sonderboni in der UI in Lieferantenbonus umbenannt
- Seite heißt „Zusätzlicher BusinessCard-Lieferantenbonus“
- Hinweis ergänzt: nicht die monatliche BusinessCard-Gebühr
- Berichte/Belegansichten sprachlich vereinheitlicht
- keine Änderung an Tabellen oder Berechnungslogik

## V53 – Briefpapier für Angebots-PDFs

- A4-Briefpapier im Stil der POSCLOUD-Rechnungen
- Firmenkopf mit FALKE-KASSEN Wortmarke und Kontaktdaten
- Absenderzeile und Kundenanschriftenfeld
- Dokumentdatenblock rechts
- hervorgehobene Full-Service-Monatsrate
- feste Fußzeile auf jeder Seite
- identisches Grundlayout für Kunden-PDF und interne Kalkulations-PDF

## V52 – Angebotsnummer JJMMTTAAA

- neue Full-Service-Angebote verwenden JJMMTTAAA
- Beispiel: 260920001 für erstes Angebot am 20.09.2026
- AAA läuft pro Kalendertag dreistellig hoch
- bestehende Angebotsnummern bleiben unverändert

## V51 – Kosten-Zyklus Einmalig/Monatlich/Jährlich

- Kosten-Zyklus pro Angebotsposition
- einmalige Kosten → Erst-EK/Amortisation
- monatliche Kosten → 1:1 laufender EK
- jährliche Kosten → automatisch /12 für Monatskalkulation
- Vertragsnachkalkulation und interne PDF auf dieselbe Logik umgestellt
- Ersatz-Zyklus Hardware/TSE bleibt separat
- 15-%-Softwarepflege nur auf einmalig lizenzierte Software-/Modul-Neuwerte

## V50 – sämtliche Hardware/TSE mit EK kalkulieren

- Hardware-/TSE-EK wird für jede Position verpflichtend
- Speichersperre bei fehlendem EK
- automatische Übernahme EK → Austausch-EK, wenn kein separater Wert hinterlegt ist
- Menge vollständig in Erst-EK und Rückstellung berücksichtigt
- interner Standardzyklus 48 Monate Hardware / 60 Monate TSE als Fallback
- Warnung in der internen PDF bei älteren unvollständigen Angeboten

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
