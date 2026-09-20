# Full-Service-Angebote und Mietverträge

Stand: 19.09.2026

## Ziel

Die bestehende Angebotsverwaltung des FALKE-KASSEN Lizenzmanagers soll zu einem durchgängigen Prozess erweitert werden:

**Kunde → Angebot → interne Kalkulation → Kunden-PDF → Auftrag → Full-Service-/Mietvertrag → Geräte → Support/Austausch → Nachkalkulation**

Die vorhandene Funktion für Update-Angebote bleibt bestehen und wird nicht ersetzt.

## Angebotsarten

1. Kaufangebot
2. Full-Service-/Mietangebot
3. Update-/Erweiterungsangebot aus einer bestehenden Lizenz

## Full-Service-Kalkulation

Die interne Kalkulation muss getrennt von der Kundendarstellung geführt werden.

Pro Position sollen mindestens erfasst werden können:

- Bezeichnung
- Kategorie: Hardware / Software / TSE / Dienstleistung / laufende Kosten
- Menge
- EK einmalig
- VK bzw. kalkulatorischer Wert
- laufender EK pro Monat/Jahr
- geplanter Austausch-EK
- Austauschzyklus in Monaten
- interne Arbeitszeit und interner Stundensatz
- optionale Lieferanten-/Produktreferenz

Vertragswerte:

- Monatsrate Kunde
- Serviceanteil (Standard derzeit 39,00 EUR netto/Monat)
- Mindestlaufzeit (Standard 48 Monate)
- Kündigungsfrist
- jährliche Preisanpassung / Indexierung
- Hardware-Erneuerungszyklus (Standard 48 Monate, je Position anpassbar)
- TSE-Erneuerung (Planwert ca. 60 Monate bzw. technisch/rechtlich erforderliche Laufzeit)

Die Kalkulation soll mindestens zeigen:

- Einnahmen über frei wählbare Laufzeit
- einmalige EK
- laufende Fremd-/Softwarekosten
- Servicekosten bzw. Serviceanteil
- Rückstellungen für Hardwareaustausch
- Rückstellung für TSE
- tatsächliche nachgebuchte Support-/Austauschkosten
- Deckungsbeitrag
- Break-even
- Ergebnis je Monat/Jahr
- Ergebnis zum möglichen Vertragsende

## Kundenansicht

Der Kunde darf keine EK-, Margen-, Rückstellungs- oder internen Kalkulationsdaten sehen.

Kundenseitig werden ausgegeben:

- Kundenanschrift
- Angebotsnummer und Datum
- Leistungsumfang
- monatliche Full-Service-Rate
- Mindestlaufzeit
- Kündigungsregel
- Preisanpassung
- Hardware-Service
- Hardware-Erneuerung
- TSE-Erneuerung
- enthaltene Softwarepflege
- Ausnahmen vom kostenlosen Austausch
- Unterschriftsbereich

## PDF

Serverseitige PDF-Erzeugung mit festem FALKE-KASSEN-Briefpapier.

Je Angebot:

- Kunden-PDF
- interne Kalkulations-PDF

Die erzeugten Dokumente sollen versioniert/archiviert werden, damit ein später geändertes Angebot die ursprünglich versendete Fassung nicht überschreibt.

## Status

Vorgesehene Statuskette:

**Entwurf → versendet → angenommen → Auftrag → aktiver Vertrag → beendet**

Die bestehenden Update-Angebotsstatus müssen kompatibel bleiben.

## Übernahme als Auftrag / Vertrag

Bei Annahme soll eine Aktion **„Als Auftrag übernehmen“** die Angebotsdaten in den operativen Bereich übernehmen.

Zu übernehmen sind insbesondere:

- Kunde
- Lizenzbezug
- Vertragsbeginn
- Monatsrate
- Mindestlaufzeit
- Serviceanteil
- enthaltene Produkte
- Geräte
- geplante Austauschzyklen
- TSE-Laufzeit

## Geräte und Austausch

Geräte sollen vertragsbezogen geführt werden können:

- Gerätetyp
- Hersteller/Modell
- Seriennummer
- Einbaudatum
- EK
- Ersatz-/Austausch-EK
- geplanter Austausch
- tatsächlicher Austausch
- Grund des Austauschs
- Altgerät zurückgenommen ja/nein

Ein Austausch erzeugt einen Nachkalkulationsdatensatz und muss in der Kundenrentabilität berücksichtigt werden.

## Support / Nachkalkulation

Zusätzliche Kosten sollen dem Vertrag zugeordnet werden können, z. B.:

- Supportzeit
- Arbeitszeit
- Ersatzhardware
- Softwarekosten
- TSE
- Fahrtkosten
- sonstige Kosten

Diese Ist-Kosten fließen in die Vertragsrentabilität ein.

## Aktuelles Beispielpaket

- 1 × MediaPOS 100
- EUCASOFT BASIC
- 1 × TSE
- 2 × BIXOLON SRP-350
- 1 × Kellnerschloss inkl. 6 Bedienerschlüssel
- 6 Stunden Programmierung
- 3 Stunden Installation/Einrichtung
- Serviceanteil 39,00 EUR netto/Monat
- Mindestlaufzeit 48 Monate
- Hardware-Erneuerung nach ca. 48 Monaten
- TSE-Erneuerung nach ca. 60 Monaten
- monatliche Rate läuft danach weiter und darf nach vereinbarter Indexlogik angepasst werden

## Technische Randbedingung zum Stand V43

Der zuerst bereitgestellte V43-Patch enthielt nicht den vollständigen Anwendungscode. Anschließend wurde der vollständige Stand `LizenzManager_BC-Card (2).zip` bereitgestellt und als Basis für V44 verwendet.


## Implementierungsstand V44

Der vollständige Stand `LizenzManager_BC-Card (2).zip` wurde geprüft. Darin sind die produktiven Angebotsdateien einschließlich `public/admin/offers.php`, `offer_print.php`, Kundenstamm, Vertragsverwaltung und Layout enthalten.

Darauf wurde V44 als Patch umgesetzt. Neu bzw. erweitert sind:

- Full-Service-/Mietangebote direkt unter **Vertrieb → Angebote**
- MediaPOS-100-Vorlage und leeres Full-Service-Angebot
- Angebots-Snapshot der Kundenanschrift
- interne EK-/VK-/Support-/Austausch-Kalkulation
- Standard-Serviceanteil 39,00 EUR netto/Monat
- Mindestlaufzeit 48 Monate
- Hardware-Austauschzyklus standardmäßig 48 Monate
- TSE-Austauschzyklus standardmäßig 60 Monate
- Planwert für jährliche VPI-/Preisanpassung
- serverseitige PDF-Erzeugung ohne externe Composer-Abhängigkeit
- getrennte Kunden-PDF und interne Kalkulations-PDF
- PDF-Versionierung und Archivierung in der Datenbank
- Statuskette Entwurf → versendet → angenommen → Auftrag → aktiver Vertrag → beendet
- Übernahme angenommener Angebote als Auftrag/Full-Service-Vertrag
- Geräte- und Seriennummernverwaltung
- Austauschbuchungen mit altem/neuem Gerät und tatsächlichem EK
- Nachkalkulation von Support, Arbeitszeit, Hardware, Software, TSE, Fahrt und sonstigen Kosten
- Preisänderungshistorie
- öffentlicher tokenisierter Kundenstatus-Link mit aktuellem Preis, Laufzeit, nächstem möglichen Vertragsende, Kündigungstermin und geplanten Erneuerungen; interne EK-/Margenwerte bleiben verborgen

Die neue Datenhaltung verwendet eigene `fs_*`-Tabellen, damit die bestehende Update-Angebotslogik und die vorhandenen `contracts`-Funktionen nicht beschädigt werden.

Die zentrale Kundenrentabilität wird in V44 bewusst noch nicht automatisch um die neuen Plan-/Ist-Kosten erweitert. Grund ist die Gefahr einer Doppelzählung, wenn dieselbe Hardware bzw. Software zusätzlich über Lieferantenrechnungen im bestehenden Einkaufssystem verbucht wird. Die spätere Verknüpfung soll über eindeutige Kosten-/Belegreferenzen mit Deduplizierung erfolgen.

### V44-Patchdateien

Neu:
- `lib/SimplePdf.php`
- `public/admin/_fullservice.php`
- `public/admin/fullservice_offer.php`
- `public/admin/fullservice_pdf.php`
- `public/admin/fullservice_contracts.php`
- `public/service_contract_status.php`

Geändert:
- `public/admin/offers.php`
- `public/admin/_layout_top.php`
- `public/admin/customers.php`

Alle aktiven V44-Dateien wurden mit PHP 8.4 per `php -l` geprüft. Im gelieferten Voll-ZIP existiert unabhängig von V44 eine ältere, nicht aktive Sicherungsdatei `public/admin/contracts3.php` mit einem Parsefehler durch ein Steuerzeichen. Diese Datei gehört nicht zum V44-Patch.


## V45 – Kalkulation aktualisieren, Modulpreise aus DB, Softwarepflege 15 %

Die Full-Service-Kalkulation verwendet für Software und Module den bestehenden zentralen Produkt-/Preislistenstamm:

- `products` = Produkt/Modul
- `pricebooks` = gültiger EK/VK bzw. Neuwert
- `license_modules.product_id` = Modulbezug einer Lizenz

Damit werden Softwarepreise nicht nochmals separat im Full-Service-Modul gepflegt.

Neu in V45:

- Button **„Preise aus DB & neu kalkulieren“** direkt im Angebotseditor
- DB-Produkt/Modul je Angebotsposition auswählbar
- DB-VK/Neuwert ist für verknüpfte Softwaremodule führend
- Softwarepflege wird verbindlich mit **15 % vom Software-Neuwert pro Jahr** berechnet
- alle Angebotspositionen der Kategorie `software` fließen in den Software-Neuwert ein
- vorhandener manueller Software-EK bleibt erhalten; bei EK 0 kann der vorhandene DB-EK übernommen werden
- bei aktiver **DATEV-BusinessCard**: 50 % Rabatt auf die Softwarepflege; der aktuelle BusinessCard-Monatswert aus der BusinessCard-Datenbank wird zusätzlich berücksichtigt (typisch 19,90 EUR, DB-Wert ist führend)
- BusinessCard-Prüfung erfolgt bei Kundenauswahl und nochmals serverseitig beim Speichern
- interne PDF und Vertragsnachkalkulation verwenden dieselbe Softwarepflege-/BusinessCard-Logik
- Produktbezug und kalkulierter Neuwert werden bei Übernahme als Vertrag mitgespeichert


## V46 – zweizeilige Positionsblöcke und Softwareauswahl nur bei Software

- interne Angebotspositionen werden als optisch getrennte Blöcke statt als breite Ein-Zeilen-Tabelle dargestellt
- erste Zeile: Kategorie, Software/Modul, Bezeichnung, Menge, Kundensichtbarkeit
- zweite Zeile: EK, Neuwert/VK, laufende EK, Austausch-EK, Zyklus, Stunden und interner Stundensatz
- Software/Modul-Auswahl aus der zentralen DB wird nur bei Kategorie `software` angezeigt
- bei Hardware, TSE, Service, Dienstleistung und Sonstiges wird kein Softwaremodul angeboten
- beim Wechsel weg von Software wird der Modulbezug auf 0 zurückgesetzt
- serverseitig wird `product_id` nur für Softwarepositionen akzeptiert; dadurch können manipulierte Formulardaten keine Hardwareposition mit einem Softwaremodul verknüpfen
- DB-Preisübernahme durch „Preise aus DB & neu kalkulieren“ wird nur auf Softwarepositionen angewendet


## V47 – 48 Monate nur interne Kalkulation, kein Kundenanspruch auf Hardwaretausch

- 48 Monate bleiben als konservativer interner Kalkulations-/Rückstellungszyklus für Hardware bestehen.
- Im Kundenangebot, Kunden-PDF und öffentlichen Vertragsstatus wird kein turnusmäßiger Hardwaretausch nach 48 Monaten mehr zugesagt.
- Kundenregel: Bei normalem technischen Defekt wird die vereinbarte Hardware repariert oder durch geeignete Hardware ersetzt.
- Funktionsfähige Hardware muss nicht nach einer festen Laufzeit erneuert werden.
- FALKE-KASSEN kann während eines aktiven Full-Service-Vertrags freiwillig nach eigenem Ermessen technisch sinnvolle Modernisierungen vornehmen; daraus entsteht kein Anspruch des Kunden.
- Interne Entscheidung über freiwillige Modernisierung kann technischen Zustand, Wirtschaftlichkeit und ordnungsgemäßen Vertrags-/Zahlungsverlauf berücksichtigen.
- TSE: kundenextern nur Austausch bei technischer Erforderlichkeit; der interne Kalkulationszyklus bleibt erhalten.
- Kundenstatus-Seite zeigt keine internen Hardware-/TSE-Plantermine mehr.
- Admin-Beschriftung kennzeichnet Hardware-/TSE-Zyklen ausdrücklich als interne Kalkulationswerte.


## V49 – interne PDF mit sauberem KPI-Raster und Grafik

- interne Kalkulationswerte werden in einem zweispaltigen Raster ausgegeben
- lange Bezeichnungen und Werte überlappen nicht mehr
- Werte stehen rechtsbündig in einer eigenen Spalte
- abwechselnd leicht hinterlegte Zeilen verbessern die Lesbarkeit
- grafische Kalkulationsübersicht wird direkt unter den KPI-Werten ausgegeben
- Kennzahlen: Monatsrate, Break-even, Ergebnis 48 Monate, Ergebnis 72 Monate
- Diagramm: kumulierte Einnahmen, kumulierte Kosten, kumuliertes Ergebnis
- interne Marker für Monat 48 (HW-Kalkulationspunkt) und Monat 60 (TSE-Kalkulationspunkt)
- Grafik erscheint ausschließlich in der internen PDF


## V50 – vollständige Hardware-/TSE-EK-Kalkulation

- jede Hardware- und TSE-Position mit Menge > 0 benötigt einen tatsächlichen EK/Stück
- Speichern wird verhindert, wenn bei Hardware/TSE der EK fehlt
- fehlende EK-Felder werden im Editor deutlich markiert und in einer Warnbox aufgeführt
- ohne separaten Austausch-EK wird der aktuelle EK automatisch als konservative Basis der internen Ersatzrückstellung übernommen
- ohne individuellen Zyklus gilt intern der Standardzyklus des Angebots: Hardware aktuell 48 Monate, TSE aktuell 60 Monate
- Mengen werden vollständig berücksichtigt, z. B. zwei Bondrucker mit 2 × EK im Erst-EK und in der Rückstellung
- bestehende Altangebote mit fehlenden Hardware-EK erhalten in der internen PDF eine Warnung
- die 48-/60-Monatswerte bleiben ausschließlich interne Kalkulationswerte und erzeugen keinen turnusmäßigen Austauschanspruch des Kunden


## V51 – Kosten-Zyklus je Position

Jede Kalkulationsposition erhält einen Kosten-Zyklus:

- **Einmalig**: EK fließt in den Erst-EK und wird über die Mindestlaufzeit amortisiert
- **Monatlich**: EK wird jeden Monat als laufender Kostenblock berücksichtigt
- **Jährlich**: EK wird für die Monatskalkulation automatisch durch 12 geteilt

Beispiele:
- Hardwarekauf: einmalig
- Programmierung/Installation: einmalig
- BusinessCard: monatlich
- jährliche Wartungs-/Lizenzgebühr: jährlich

Technisch:
- `fs_offer_items.cost_cycle`
- `fs_contract_items.cost_cycle`
- automatische Schema-Erweiterung
- Altbestände erhalten `once`
- Vertragsnachkalkulation, Angebotskalkulation und interne PDF verwenden dieselbe Zykluslogik
- der Hardware-/TSE-Ersatzzyklus bleibt davon getrennt und ist weiterhin nur eine interne Rückstellungs-/Planungsgröße
- die automatische Softwarepflege von 15 % wird nur auf einmalig lizenzierte Software-/Modul-Neuwerte berechnet; monatliche/jährliche Abos erhöhen diesen Software-Neuwert nicht


## V52 – Angebotsnummer JJMMTTAAA

Neue Full-Service-Angebote erhalten die Angebotsnummer im Format **JJMMTTAAA**.

Beispiel für den 20.09.2026:
- 260920001
- 260920002
- 260920003

Bedeutung:
- JJ = zweistelliges Jahr
- MM = Monat
- TT = Tag
- AAA = dreistellige, pro Kalendertag fortlaufende Nummer

Die Tagesnummer startet jeweils wieder bei 001. Bestehende Angebotsnummern werden nicht rückwirkend geändert.


## V53 – FALKE-KASSEN Briefpapier für Angebots-PDFs

- Kunden- und interne Angebots-PDF erhalten ein einheitliches A4-Briefpapier im Stil der POSCLOUD-Rechnungs-PDFs
- blauer Akzent und klarer FALKE-KASSEN Firmenkopf
- Absenderzeile und Anschriftenfeld links
- Dokumentdaten rechts mit Angebotsnummer, Datum, Gültigkeit und geplantem Start
- Full-Service-Monatsrate als hervorgehobener Bereich
- feste Fußzeile auf jeder Seite
- Folgeseiten behalten Firmenkopf und Fußzeile
- Kundenfassung ist als ANGEBOT gekennzeichnet; interne Fassung als INTERNE KALKULATION
- aktuelle Briefpapierdaten: FALKE-KASSEN GmbH, Mozartstr. 3, 26215 Wiefelstede, +49 (0)4402-60696, falke-kassen.com
- aktuell wird eine typografische FALKE-KASSEN Wortmarke verwendet, da im Repository noch keine Logo-Bilddatei hinterlegt ist


## V54 – BusinessCard-Lieferantenbonus verständlich benannt

- Navigation: **Lieferantenbonus** statt **Sonderboni**
- Seitentitel: **Zusätzlicher BusinessCard-Lieferantenbonus**
- Erklärung stellt ausdrücklich klar, dass dies **nicht** die monatliche BusinessCard-Gebühr ist
- Aktionen und Statusmeldungen verwenden dieselbe Bezeichnung
- Lieferantenbeleg-/Weiterberechnungsansichten wurden sprachlich angeglichen
- Berichte zeigen **BC-Lieferantenbonus** statt **Sonderbonus**
- Datenbanktabellen und Berechnungslogik bleiben unverändert; es handelt sich nur um eine UI-/Begriffsänderung
