# Dolibarr-Integration

Stand: 19.09.2026

## Ziel

Der Lizenzmanager soll mit Dolibarr ERP/CRM verbunden werden, ohne doppelte Stammdatenpflege und ohne widersprüchliche Rechnungs-/Zahlungsstände.

Grundidee:

**Dolibarr = kaufmännisches führendes System**  
**Lizenzmanager = technisches Vertrags-, Lizenz-, Lieferantenkosten- und Rentabilitätssystem**

## Führende Daten je System

### Dolibarr ist führend für

- Kundenstammdaten / Geschäftspartner
- Anschrift, E-Mail, Ansprechpartner
- Dolibarr-Kundennummer
- Produkte/Services, soweit sie fakturiert werden
- Kundenrechnungen
- Rechnungsnummern
- Zahlungsstatus / Zahlungseingänge
- Gutschriften an Kunden
- optional Lieferantenrechnungen, sobald die Übergabe stabil ist

### Lizenzmanager ist führend für

- EUCASOFT-/CASPOS-Lizenzdaten
- Dongle-ID
- Lizenz-Code
- BusinessCard und Apps/Module
- Anbieter-Kundennummern
- TSE-Bestand, Seriennummern, Ablaufdaten, Kundenzuordnung
- Service-/Updatevertrag-Fachlogik
- Lieferanten-PDF-Parser
- Auftrag → Rechnung Matching
- Kontingentverbrauch
- BusinessCard-Provisionen
- BC-Sonderbonus
- technische Kundenrentabilität

## Technische Anbindung

Dolibarr stellt eine REST-API bereit.

Basis:
- REST-Modul in Dolibarr aktivieren
- eigener API-Benutzer mit minimal notwendigen Rechten
- API-Key nicht im GitHub speichern
- Aufruf über HTTPS
- Dolibarr API Explorer zur Prüfung der auf der konkreten Installation verfügbaren Endpunkte verwenden

Typische API-Bereiche:
- thirdparties
- products/services
- invoices
- supplier invoices
- contracts
- payments

Zusätzlich kann das Dolibarr-Webhook-Modul Ereignisse als JSON an den Lizenzmanager senden.

## Verknüpfungsschlüssel

In der Lizenzmanager-Datenbank sollen stabile Dolibarr-IDs gespeichert werden.

Beispiele:

### Kunde
- `customers.dolibarr_thirdparty_id`
- optional `dolibarr_customer_code`
- `dolibarr_synced_at`

### Kundenrechnung
Für eine aus dem Lizenzmanager erzeugte oder zugeordnete Rechnung:
- Dolibarr Invoice-ID
- Dolibarr Rechnungsnummer
- Status
- Zahlungsstatus
- letzter Sync

### Produkt
- internes Produkt
- Dolibarr Product/Service-ID
- Dolibarr Ref

IDs sind maßgeblich, nicht nur Namen.

## Synchronisationsrichtung

### 1. Dolibarr → Lizenzmanager

Regelmäßig bzw. per Webhook:

- neue/geänderte Kunden
- Kundenadressen und Kontakte
- validierte Kundenrechnungen
- Zahlungen / vollständig bezahlt
- Storno/Gutschrift
- optional Produkte/Services

Besonders wichtig:
Der Zahlungsstatus einer Kundenrechnung steuert im Lizenzmanager die Freigabe eines **BusinessCard-Sonderbonus**.

### 2. Lizenzmanager → Dolibarr

Nach fachlicher Freigabe:

- neuen Kunden nur dann anlegen, wenn er nicht bereits in Dolibarr existiert
- freigegebene Weiterberechnungen als Rechnungsentwurf erzeugen
- Software-/Update-Verkäufe als Rechnungszeilen
- Service-/Vertragsabrechnungen
- Supportabrechnungen
- optional BC-Sonderbonus als Gutschrift/Rabattbeleg nach Freigabe
- optional geprüfte Lieferantenrechnung an Dolibarr übertragen

## Rechnungsworkflow

Empfohlener Ablauf:

1. Lieferantenrechnung kommt in den Lizenzmanager.
2. Parser erkennt Kunde, Produkt, EK, Kontingent, BusinessCard-Rabatt usw.
3. Lizenzmanager entscheidet:
   - Servicevertrag deckt Leistung
   - separat weiterberechnen
   - individueller Kundenpreis
   - sonst Preisregel EK × 2
4. Benutzer prüft/freigibt.
5. Lizenzmanager erzeugt **Dolibarr-Rechnungsentwurf**.
6. Dolibarr ist ab diesem Zeitpunkt führend für:
   - Rechnungsnummer
   - Validierung
   - Versand
   - Zahlung
7. Dolibarr meldet Zahlung zurück.
8. Lizenzmanager aktualisiert Rentabilität und ggf. Sonderbonus-Freigabe.

Wichtig:
Der Lizenzmanager darf eine Rechnung nicht zusätzlich als manuellen Zusatzumsatz zählen, wenn sie bereits als Dolibarr-Rechnung verknüpft ist.

## BusinessCard-Sonderbonus

Workflow:

1. Lieferantenbeleg enthält zusätzlichen BC-Aktionsrabatt.
2. Lizenzmanager merkt möglichen Sonderbonus vor.
3. Normale Kundenrechnung wird in Dolibarr erstellt.
4. Dolibarr meldet vollständige Zahlung.
5. Lizenzmanager setzt Bonus auf **freigegeben**.
6. Benutzer entscheidet, ob Bonus tatsächlich ausgegeben wird.
7. Falls gewünscht, erzeugt Lizenzmanager daraus eine Dolibarr-Gutschrift oder Rabattrechnung.
8. Erst die tatsächliche Ausgabe reduziert die Kundenrentabilität.

## Verträge

Die detaillierte Vertragslogik bleibt zunächst im Lizenzmanager.

Dolibarr kann optional eine Spiegelung im Vertragsmodul erhalten, insbesondere:
- Vertragsnummer
- Kunde
- Laufzeit
- aktive Services
- wiederkehrender Betrag

Die fachliche Entscheidung, ob eine eingehende CASPOS/EUCASOFT-Leistung im Vertrag enthalten ist, bleibt im Lizenzmanager.

## Lieferantenrechnungen

Phase 1:
- Lieferantenbelege bleiben im Lizenzmanager führend
- Dolibarr erhält noch keine automatische Lieferantenrechnung

Phase 2:
- nach erfolgreicher Prüfung kann eine freigegebene Lieferantenrechnung in Dolibarr erzeugt werden
- Original-PDF und Lieferantenbelegnummer verknüpfen
- Kontingentgegenbuchungen dürfen nicht zu doppelten Kosten führen

## Konfliktregeln

- gleicher Kunde in beiden Systemen: über feste Dolibarr-ID verbinden
- Name allein ist kein dauerhafter Schlüssel
- Änderungen an Kundenstammdaten kommen primär aus Dolibarr
- technische Lizenzdaten werden nicht von Dolibarr überschrieben
- Kundenrechnung nach Erzeugung in Dolibarr nicht parallel im Lizenzmanager verändern
- Zahlung wird nur aus Dolibarr übernommen, nicht im Lizenzmanager erfunden

## Synchronisationsprotokoll

Jeder Lauf sollte protokollieren:

- Zeit
- Richtung
- Objekttyp
- lokale ID
- Dolibarr-ID
- Aktion create/update/read
- HTTP-Status
- Erfolg/Fehler
- Fehlermeldung
- Payload-Hash bzw. Änderungsstand

Fehler müssen im Bereich **Aufgaben** des Lizenzmanagers sichtbar werden.

## Admin-Seite im Lizenzmanager

Geplant:

**System → Dolibarr**

Bereiche:
- API-URL
- API-Key
- Verbindung testen
- letzte Synchronisation
- Kundenabgleich
- Produktabgleich
- Rechnungsabgleich
- Fehlerprotokoll
- Webhook-Geheimnis
- Sync ein/aus

API-Key nur serverseitig speichern und in der Oberfläche maskieren.

## Umsetzung in Phasen

### Phase 1 – sicherer Leseabgleich
- Verbindungstest
- Kunden aus Dolibarr suchen/zuordnen
- Dolibarr-ID am Kunden speichern
- Kundenrechnungen und Zahlungsstatus lesen
- BC-Sonderbonus an echten Zahlungsstatus koppeln

### Phase 2 – Rechnungsentwürfe
- Produkte/Services abgleichen
- freigegebene Weiterberechnung als Dolibarr-Entwurf erzeugen
- Dolibarr-Rechnungs-ID zurückspeichern
- Schutz gegen Doppelerzeugung

### Phase 3 – Verträge und wiederkehrende Leistungen
- Vertrags-/Serviceinformationen spiegeln
- wiederkehrende Abrechnung integrieren

### Phase 4 – Lieferantenrechnungen
- geprüfte Eingangsrechnungen optional an Dolibarr übertragen
- PDF/Referenz verknüpfen

## Sicherheitsregeln

- API-Key niemals committen
- eigener technischer Dolibarr-Benutzer
- nur benötigte Berechtigungen
- HTTPS erzwingen
- Webhook mit eigenem Secret schützen
- idempotente Synchronisation
- keine automatische Löschung auf der Gegenseite
- produktive Schreiboperationen erst nach erfolgreichem Test gegen eine Testinstanz oder klar abgegrenzte Testdaten


## E-Mail-Archivierung und Dokumentverknüpfung

Der Lizenzmanager soll zusätzlich als zentraler Eingang für relevante E-Mails dienen.

### Zielbild

Eine E-Mail kann an ein definiertes Lizenzmanager-Postfach weitergeleitet oder direkt dorthin geschickt werden.

Der Lizenzmanager speichert:
- originale E-Mail als `.eml`
- Absender
- Empfänger
- Betreff
- Sende-/Empfangsdatum
- Message-ID
- Text-/HTML-Inhalt
- Anhänge
- SHA-256-Hash zur Dublettenprüfung

Danach wird die E-Mail einem Dolibarr-Objekt zugeordnet.

### Mögliche Dolibarr-Ziele

Priorität je nach erkannter E-Mail:

1. konkrete Kundenrechnung
2. konkreter Vertrag
3. konkreter Auftrag/Angebot
4. Kunde/Geschäftspartner
5. Lieferant
6. allgemeiner Dokumenteingang zur manuellen Zuordnung

Der Lizenzmanager speichert dauerhaft:
- Dolibarr Objekttyp
- Dolibarr Objekt-ID
- Dolibarr Dokumentpfad bzw. Dokumentreferenz
- Synchronisationsstatus
- Zeitpunkt der Übergabe

### Dokumente in Dolibarr

Die Original-E-Mail soll möglichst als `.eml` hochgeladen werden. Anhänge wie PDF, XML, Bilder oder Office-Dateien werden zusätzlich einzeln übertragen.

Dadurch bleibt sowohl die originale Kommunikation als auch das eigentliche Dokument erhalten.

Beispiel:

**E-Mail von CASPOS**
- Original: `mail_2026-09-19_12345.eml`
- Anhang: `RECHNUNG_4260xxxx.pdf`
- Zuordnung im Lizenzmanager: Lieferantenrechnung 4260xxxx
- Dolibarr: Dokumente der zugehörigen Lieferantenrechnung oder des Lieferanten

**E-Mail vom Kunden**
- Original-E-Mail
- ggf. Anhänge
- Zuordnung zum Kunden
- optional zusätzlich zu Rechnung, Vertrag oder Supportvorgang

Die Dolibarr Documents REST API unterstützt objektbezogene Dokumente und Datei-Uploads. Welche `modulepart`-Werte auf der produktiven Dolibarr-Version verfügbar sind, wird vor Aktivierung über den Dolibarr API Explorer geprüft.

### Automatisches Matching

Die Zuordnung erfolgt schrittweise:

1. bekannte E-Mail-Adresse → Kunde/Lieferant
2. Kundennummer / Rechnungsnummer / Vertragsnummer im Betreff oder Inhalt
3. erkannter PDF-Anhang
4. bekannte Anbieter-Kundennummer
5. bei Eindeutigkeit automatisch verknüpfen
6. bei Mehrdeutigkeit als Aufgabe anzeigen

Manuelle Zuordnungen werden gelernt und künftig wiederverwendet.

### Arbeitskorb

Im Lizenzmanager soll ein Bereich entstehen:

**Einkauf / Kommunikation → E-Mail-Dokumente**

Status je E-Mail:
- neu
- Kunde erkannt
- Dokument erkannt
- Dolibarr zugeordnet
- Dokumente synchronisiert
- vollständig erledigt
- Fehler / Prüfung nötig

Beispielanzeige:

**3/3 Dokumente übertragen · Kunde zugeordnet · Dolibarr verknüpft · ✓ erledigt**

Erst wenn alle notwendigen Schritte abgeschlossen sind, verschwindet die E-Mail aus dem offenen Arbeitskorb.

### Deduplizierung

Zur Vermeidung doppelter Ablage:
- Message-ID der E-Mail
- Hash der vollständigen `.eml`
- Hash je Anhang
- Dolibarr-Zielobjekt + Dateiname/Hash

Eine bereits archivierte E-Mail darf bei erneutem Weiterleiten nicht doppelt als Dokument angelegt werden.

### Aufbewahrung

Der Lizenzmanager behält eine lokale Referenz/Archivkopie, auch wenn das Dokument zusätzlich an Dolibarr übertragen wurde.

Dolibarr ist dann das kaufmännische Dokumentarchiv am jeweiligen Objekt; der Lizenzmanager hält die technische Importhistorie und die Verknüpfung.

### Sicherheit

- HTML-Inhalte nur bereinigt anzeigen
- Anhänge niemals ungeprüft ausführen
- nur erlaubte Dateitypen direkt anzeigen
- API-Key bleibt serverseitig
- Upload nach Dolibarr nur über HTTPS
- kein automatisches Überschreiben bestehender Dolibarr-Dokumente ohne eindeutige Hashprüfung


## Implementierungsstand V55 – Phase 1 live vorbereitet

Phase 1 ist im Lizenzmanager als read-first Integration umgesetzt:

- neue Admin-Seite **System → Dolibarr**
- produktive REST-Basis standardmäßig `https://erp.falke-kassen.de/htdocs/api/index.php`
- API-Key ausschließlich serverseitig; im Lizenzmanager verschlüsselt gespeichert und in der UI nie angezeigt
- Verbindungstest gegen die Thirdparty-API
- klare Erkennung der Dolibarr-IP-Sperre `API_RESTRICT_ON_IP`
- Kundenimport/-abgleich Dolibarr → Lizenzmanager
- feste Speicherung von `customers.dolibarr_thirdparty_id`, Dolibarr-Kundennummer und Sync-Zeitpunkt
- automatische Zuordnung nur bei eindeutiger Dolibarr-ID, exakter E-Mail oder eindeutigem normalisiertem Namen
- manuelle Zuordnung bleibt möglich
- lokale Neuanlage eines ausgewählten, noch nicht vorhandenen Dolibarr-Kunden
- Kundenakte und Kundenliste zeigen Dolibarr-Verknüpfung
- Dolibarr-Kundenrechnungen und Zahlungsstatus können pro verknüpftem Kunden lesend geladen werden
- Synchronisationsprotokoll und Fehleranzeige im Aufgabenbereich
- BusinessCard-Lieferantenbonus-Seite verlinkt bei verbundenen Kunden auf die Dolibarr-Rechnungsansicht

Phase 1 schreibt bewusst **keine Rechnungen oder Kundenänderungen nach Dolibarr**. Nach erfolgreichem produktivem Verbindungstest folgt Phase 2 mit Rechnungsentwürfen, eindeutigen Rechnungslinks und automatischer Zahlungsrückmeldung.


## V56 – Geschäftspartner-Vorschau statt leerem Kundenfilter

Die Dolibarr-Vorschau liest jetzt bewusst **alle Thirdparties mit mode=0** und zeigt deren Typkennzeichen an.

Anzeige je Dolibarr-Datensatz:
- Dolibarr-ID
- Name / E-Mail
- Kundennummer
- Kunde ja/nein
- Interessent ja/nein
- Lieferant ja/nein
- lokale Zuordnung und Erkennungsgrund

Zusätzlich wird eine Zusammenfassung angezeigt:
**Geschäftspartner gesamt · Kunden · Interessenten · Lieferanten · Sonstige**.

Der automatische Kundenabgleich bleibt sicher: Er verarbeitet ausschließlich Thirdparties, die in Dolibarr tatsächlich als Kunde gekennzeichnet sind. Nicht-Kunden sind nur in der Vorschau sichtbar und werden nicht automatisch in den lokalen Kundenstamm importiert.

Auch der Verbindungstest verwendet jetzt mode=0. Dadurch hängt ein erfolgreicher REST-Verbindungstest nicht mehr davon ab, ob in Dolibarr mindestens ein Datensatz als Kunde markiert ist.


## V57 – API-Diagnose und Entity-Kontext

Da die Thirdparty-API trotz HTTP 200 keine Geschäftspartner lieferte, obwohl die Berechtigungen korrekt gesetzt wurden, wurde eine Diagnose ergänzt.

Neu:
- Button **API-Diagnose** unter System → Dolibarr
- liest `GET /users/info?includepermissions=1` für den tatsächlich zum API-Key gehörenden Benutzer
- zeigt Benutzer-ID, Login, Entity, socid, Adminstatus und Status
- vergleicht Thirdparty-Aufrufe:
  - mode=0 ohne Pagination
  - mode=1 nur Kunden
  - mode=0 mit Pagination
- technische REST-Antworten können aufgeklappt werden
- Warnung, wenn der API-Benutzer eine `socid` besitzt und damit wahrscheinlich extern/beschränkt ist
- Warnung, wenn der Thirdparty-Endpunkt HTTP 200 liefert, aber leer bleibt
- optionales Feld **Dolibarr Entity-ID**; bei Wert > 0 wird der offizielle Header `DOLAPIENTITY` gesendet
- Standard bleibt 0 = Entity des API-Benutzers

Dolibarr 22.0 setzt ohne `DOLAPIENTITY` den API-Kontext auf die Entity des Benutzers. Die Thirdparty-API filtert Geschäftspartner über `getEntity('societe')`. Damit ist bei HTTP 200 + leerer Liste neben Benutzerrechten insbesondere ein falscher MultiCompany-/Entity-Kontext zu prüfen.


## V58 – Diagnose-UI sichtbar und eindeutig

Korrektur der Dolibarr-Adminseite:
- API-Konfiguration in klar getrennte Bereiche aufgeteilt
- Entity-ID 0 als Standard/Platzhalter sichtbar
- Sync-Schalter und API-Key-löschen bleiben sichtbar
- eigener Bereich **Verbindung und Diagnose**
- **API-Diagnose** als primäre Aktion hervorgehoben
- Verbindungstest, Geschäftspartner-Vorschau und Kundenabgleich weiterhin sichtbar
- Hinweis: zunächst Entity-ID 0 lassen und API-Diagnose ausführen


## V59 – Dolibarr-Navigation kumulativ korrigiert

V58 enthielt nur die Diagnose-Dateien. Wenn zuvor noch eine ältere `_layout_top.php` aktiv war, erschien **System → Dolibarr** nicht im Menü.

V59 enthält deshalb kumulativ:
- `public/admin/_layout_top.php` mit Menüpunkt **System → Dolibarr**
- aktuelle Dolibarr-Diagnoseseite
- aktuelle Dolibarr-API-Hilfe mit Entity-Diagnose

Nach Installation ggf. Browsercache mit Strg+F5 aktualisieren.
