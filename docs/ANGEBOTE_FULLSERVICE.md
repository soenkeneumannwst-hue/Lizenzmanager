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
