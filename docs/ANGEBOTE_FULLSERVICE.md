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

Das bereitgestellte V43-ZIP ist ein Patch-Paket und enthält nicht den vollständigen Anwendungscode. Insbesondere fehlt die im Produktionssystem vorhandene Datei `public/admin/offers.php` sowie die vollständige Basis-/PDF-Infrastruktur. Die Implementierung soll deshalb auf dem vollständigen aktuellen Quellstand erfolgen, nicht durch Neuerfindung der bestehenden Angebotslogik.
