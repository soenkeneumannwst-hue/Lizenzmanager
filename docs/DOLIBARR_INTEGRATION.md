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


## V60 – Entity-Hilfsfunktion ergänzt

V57/V58/V59 enthielten im Dolibarr-Admin bereits das Feld `dolibarr_entity_id`, aber die Hilfsfunktion `dol_entity_id(PDO $pdo)` fehlte in `lib/Dolibarr.php`. Dadurch brach die Seite beim Rendern direkt nach der Beschriftung **Dolibarr Entity-ID** ab; nachfolgende Felder, Buttons und Diagnosebereiche wurden nicht mehr ausgegeben.

V60 ergänzt die fehlende Funktion. Standardwert ist 0 = Entity des API-Benutzers.


## V61 – TSE-ID/Seriennummer mit Dolibarr abgleichen

Der bestehende zentrale TSE-Bestand wird mit Dolibarr über die eindeutige TSE-Seriennummer abgeglichen.

Regeln:
- Seriennummer ist der führende Abgleichsschlüssel
- keine automatische TSE-Zuordnung anhand ähnlicher Kundennamen
- Dolibarr-Geschäftspartnerdaten inklusive Extrafeldern werden nach exakten Seriennummern durchsucht
- zusätzlich werden Kundenrechnungen inklusive Positionsdaten durchsucht
- Serien aus `license_tse_serials` werden vor der Prüfung in `tse_devices` gespiegelt, sofern sie dort noch fehlen
- eindeutiger Treffer + TSE bisher ohne Kunde → **eindeutig zuweisbar**
- bestehende gleiche Zuordnung → **stimmt überein**
- bestehende abweichende Zuordnung → **Konflikt**, niemals automatisch überschreiben
- Treffer unter mehreren Dolibarr-Kunden → **mehrdeutig**, keine automatische Änderung
- Dolibarr-Thirdparty noch nicht lokal verknüpft → Hinweis zur Kundenverknüpfung
- nicht gefundene Serien bleiben unverändert

Die Aktion **Eindeutige Treffer übernehmen** ändert ausschließlich bisher unzugeordnete TSEs mit genau einem konfliktfreien Dolibarr-Kundentreffer.

Im TSE-Datensatz werden Prüfergebnis, Dolibarr-Thirdparty-ID, ggf. Rechnungs-ID/-Ref, Quelle und Prüfzeitpunkt gespeichert.

Standardmäßig werden bis zu 10 Rechnungsseiten à 250 Rechnungen geprüft; der Umfang ist bis 40 Seiten konfigurierbar.


## V62 – TSE-Freitext/Präfix-Normalisierung

Beim TSE-Abgleich wurde die Erkennung an die reale Dolibarr-Datenpflege angepasst. TSE-IDs stehen dort teilweise in Freitextfeldern zusammen mit Angaben wie `TSE1:`, `TSE2:`, `ID:`, Ablaufdatum oder BSI-Kennung. Zusätzlich tragen manche Einträge einen führenden numerischen Präfix, z. B. `607645-602603730189-0325-5`, während im Lizenzmanager nur der Kern `602603730189-0325-5` gespeichert sein kann.

Neu:
- TSE-Serien werden aus Dolibarr-Freitexten per Mustererkennung extrahiert
- Leerzeichen um Bindestriche werden toleriert
- ein optionaler 5- bis 8-stelliger numerischer Präfix wird nur für den Vergleich ignoriert, wenn der verbleibende Kern exakt einer lokalen TSE-Seriennummer entspricht
- der numerische TSE-Kern selbst bleibt ein strenger 1:1-Abgleich
- gefundene Originaldarstellung aus Dolibarr wird im Prüfergebnis angezeigt
- Status `noch nicht geprüft` weist nun ausdrücklich auf **Scan starten** hin

Sicherheitsregel bleibt unverändert: Kunden werden niemals aufgrund eines ähnlichen Namens oder eines nur teilweise passenden Seriennummernfragments automatisch zugewiesen.


## V63 – Hardware-ID und TSE-Seriennummer getrennt

Die TSE-Stammdaten orientieren sich jetzt am realen Swissbit-TSE-Aufkleber und unterscheiden ausdrücklich zwischen:
- Hardware-ID, z. B. `602884530157-2326-5`
- technischer TSE-Seriennummer, z. B. einem 64-stelligen Hexwert
- Hersteller
- HW-Version
- SW-Version
- Zertifikat gültig bis
- Kassen-Seriennummer

Bestehende Altwerte werden automatisch klassifiziert: Hardware-ID-Muster landen in `hardware_id`, lange Hexwerte in `tse_serial_number`. Nicht klassifizierbare Platzhalter wie `1` bleiben als Altwert sichtbar, werden aber nicht mehr für den Dolibarr-Abgleich verwendet.

Der Dolibarr-Abgleich prüft Hardware-ID und TSE-Seriennummer getrennt. Ein zusätzlicher Präfix wie `607645-` wird nur beim Hardware-ID-Vergleich ignoriert; der eigentliche Hardware-ID-Kern muss exakt stimmen. Technische TSE-Seriennummern werden ebenfalls exakt verglichen und auch erkannt, wenn ein 64-stelliger Hexwert im Freitext über zwei Zeilen verteilt ist.


## V64 – Aktive EUCASOFT-/CASPOS-Systemkunden abgleichen

Dolibarr ist für den Aktiv-/Systemstatus führend:
- Kategorie **SystemKunde** = aktiver Systemkunde
- Kategorie **EUCASOFT** = EUCASOFT-Kunde
- Kategorie **CASPOS** = CASPOS-Kunde
- automatisch berücksichtigt werden nur Mitglieder von SystemKunde, die zusätzlich EUCASOFT und/oder CASPOS zugeordnet sind

Neu unter **Stammdaten → Systemkunden-Abgleich**:
- direkte REST-Auswertung der Dolibarr-Kategorien und ihrer Kundenmitglieder
- Summen für SystemKunde, EUCASOFT, CASPOS, beide Systeme, lokal zuordenbar, lokal fehlend und manuell zu prüfen
- Matching-Priorität: Dolibarr-ID → Dolibarr-Kundennummer exakt → E-Mail exakt → normalisierter Name exakt
- bestehende Verknüpfung mit abweichender Dolibarr-ID wird niemals automatisch überschrieben
- mehrdeutige Treffer bleiben zur manuellen Prüfung offen
- „Alle aktiven Systemkunden abgleichen“ aktualisiert eindeutige Bestandskunden und legt eindeutig fehlende aktive Systemkunden lokal neu an
- Kunden werden durch diesen Abgleich niemals automatisch gelöscht

### Kundennummer
Die Dolibarr-Kundennummer (`code_client`) ist führend und wird bei jedem Abgleich in den Lizenzmanager übernommen.
Dafür wird `customers.customer_number` automatisch ergänzt und parallel zu `dolibarr_customer_code` gepflegt. Die Kundennummer ist in Kundenliste und Kundenmaske sichtbar und wird bei der Kundensuche berücksichtigt.

Zusätzliche lokale Statusfelder:
- `dolibarr_system_customer`
- `dolibarr_eucasoft`
- `dolibarr_caspos`
- `dolibarr_category_synced_at`

Der Dolibarr-API-Benutzer benötigt für diesen Abgleich zusätzlich **Leserechte auf Kategorien**.


## V65 – QUAD-Lieferantenrechnung automatisch zum Dolibarr-Kunden

Für QUAD-Lieferantenrechnungen ist die **Lieferanschrift** die führende Endkundenquelle. Die auf der QUAD-Rechnung ausgewiesene QUAD-Kundennummer ist die Lieferanten-Kundennummer von FALKE-KASSEN und wird ausdrücklich **nicht** als Endkunden-Kundennummer verwendet.

Workflow:
1. QUAD anhand Absender/Rechnungstext erkennen.
2. Bei ZUGFeRD/Factur-X/XRechnung zuerst die strukturierten XML-Daten auswerten; bei normalem PDF die Lieferanschrift layoutbezogen auslesen.
3. Endkunde im lokalen Lizenzmanager erkennen oder automatisch anlegen.
4. Über feste Dolibarr-ID/Kundennummer bzw. exakten Namen und Adressdaten in Dolibarr suchen.
5. Wenn kein eindeutiger Dolibarr-Kunde existiert, automatisch einen Dolibarr-Geschäftspartner mit `client=1` und der Lieferanschrift anlegen.
6. Die von Dolibarr vergebene Kundennummer `code_client` zurück in `customers.customer_number` / `dolibarr_customer_code` übernehmen.
7. Original-Lieferanten-PDF direkt im Dokumentenordner des Dolibarr-Kunden ablegen.
8. Vorhandenes E-Rechnungs-XML zusätzlich beim Kunden ablegen.
9. Übertragung pro Lieferantenbeleg/Kunde protokollieren und bei Fehlern manuell erneut anstoßen können.

Die Tabelle `supplier_invoice_dolibarr_links` protokolliert Dolibarr-Kunde, automatische Neuanlage, PDF-/XML-Uploadstatus, Fehler und Synczeitpunkt. Eine Eingangsrechnung kann weiterhin mehrere Endkunden enthalten; in diesem Fall wird der Beleg bei jedem eindeutig zugeordneten Kunden abgelegt.

### QUAD Beispielregel
Bei einer QUAD-Rechnung wie `R2625916` ist nicht der Rechnungsempfänger FALKE-KASSEN der Endkunde, sondern der unter **Lieferanschrift** genannte Kunde. Name, Ansprechpartner, Straße, PLZ und Ort werden übernommen.

### E-Mail-Import
Der E-Mail-Importer archiviert neben PDFs auch vorhandene Rechnungs-XML-Anhänge. Beim Öffnen/Verarbeiten des Arbeitskorbs werden eindeutige QUAD-Rechnungen automatisch verarbeitet. Eingebettetes ZUGFeRD-/Factur-X-XML hat Vorrang vor der PDF-Texterkennung.

### Rechte
Für V65 braucht der technische Dolibarr-API-Benutzer zusätzlich zu den Leserechten:
- Geschäftspartner erstellen/bearbeiten
- Dokumente beim Geschäftspartner hochladen


## V67 – Dokumenten-Massenimport und automatische Belegerkennung

Neuer zentraler Bereich **Einkauf → Dokumentenimport** für große Mengen an Belegen.

Unterstützte Belegarten:
- Eingangsrechnung
- Ausgangsrechnung
- Lieferschein
- Auftrag / Auftragsbestätigung

Ablauf:
1. Mehrere PDF/XML-Dateien oder ein kompletter Ordner werden ausgewählt bzw. per Drag&Drop übergeben.
2. Die Dateien werden browserseitig einzeln analysiert und anschließend einzeln hochgeladen, damit PHP-Limits wie `max_file_uploads` und `post_max_size` bei großen Mengen nicht zum Flaschenhals werden.
3. Belegart, Richtung (eingehend/ausgehend), Belegnummer, Datum, Liefer-/Kundenanschrift und vorhandenes E-Rechnungs-XML werden erkannt.
4. Vor jeder Kunden-Neuanlage läuft der V66-Dublettenschutz.
5. Eindeutige Belege werden automatisch dem vorhandenen bzw. eindeutig neu anzulegenden Kunden zugeordnet und als Dokument bei Dolibarr hinterlegt.
6. FALKE-Ausgangsrechnungen können weiterhin als Dolibarr-Rechnungsentwurf angelegt werden; das Original-PDF bleibt zusätzlich als Anhang erhalten.
7. Unsichere Fälle werden niemals automatisch einem neuen Kunden zugeordnet.

### Sicherheitsregel: eigene Lieferadresse
Die eigene Lieferadresse ist auf der Importseite konfigurierbar (Standard: **FALKE-KASSEN GmbH, Mozartstr. 3, 26215 Wiefelstede**).

Wird diese Anschrift bei einem **eingehenden Lieferantenbeleg als Lieferanschrift** erkannt, wird der Beleg bewusst beiseitegelegt:
- Status **Manuell zuordnen**
- kein automatischer Kunde
- keine automatische Dolibarr-Neuanlage
- bestehender Kunde wird anschließend über eine Auswahlliste zugeordnet
- danach wird das Original-PDF/XML beim ausgewählten Dolibarr-Kunden gespeichert

Damit verhält sich dieser Fall ähnlich wie die manuelle TSE-Zuordnung.

Weitere Fälle für die manuelle Prüfliste:
- mögliche Kundendublette
- kein eindeutiger Kunde aus dem Beleg erkennbar
- Belegart nicht sicher klassifizierbar
- technischer Übertragungsfehler

### E-Rechnungen
ZUGFeRD/Factur-X/XRechnung wird bevorzugt aus strukturiertem XML gelesen. Bei eingebettetem XML werden Liefer-/Käuferdaten und Belegnummer zuerst daraus übernommen. Standalone-XML-Dateien werden ebenfalls akzeptiert.


## V68 – Stabiler Massenimport / Upload und Dolibarr-Verarbeitung getrennt

V67 führte Upload, Klassifikation, Kundensuche/-anlage und Dolibarr-Übertragung innerhalb desselben HTTP-Requests aus. Bei größeren Mengen konnte ein langsamer oder abgebrochener API-/PHP-CGI-Aufruf im Browser als `NetworkError when attempting to fetch resource` erscheinen.

V68 trennt den Ablauf in zwei Phasen:
1. PDF/XML und erkannte Metadaten werden zuerst sicher in `document_intake_items` gespeichert; der Server antwortet sofort mit Status `queued`.
2. Erst danach wird jede gespeicherte Import-ID in einem eigenen Request nach Dolibarr verarbeitet.

Folgen:
- ein Dolibarr-/Netzwerkfehler vernichtet keinen Datei-Upload mehr;
- bereits gespeicherte Belege können über **Warteschlange verarbeiten** erneut abgearbeitet werden;
- Uploadfehler und Verarbeitungsfehler werden getrennt angezeigt;
- Netzwerkfehler werden einmal automatisch wiederholt;
- der SHA-256-Dublettenschutz verhindert beim Wiederholen eine zweite Ablage derselben Datei;
- bei großen Stapeln werden zuerst alle Dateien sicher hochgeladen und erst danach verarbeitet.


## V69 – Statusmodell und Namensbereinigung im Dokumentenimport

Korrekturen nach dem ersten größeren V68-Import:
- `queued` wird in der Importliste nicht mehr gleichzeitig als erledigt dargestellt.
- Nur `assigned` erhält den sichtbaren Abschlussstatus **✓ In Dolibarr gespeichert / erledigt**.
- `queued` wird als **⏳ Warteschlange** dargestellt und kann einzeln über **jetzt verarbeiten** gestartet werden.
- Manuelle Zuordnung, mögliche Dublette und Fehler erhalten eigene verständliche Statusdarstellungen.
- Exakt doppelt erkannte Firmennamen wie `Deutsche Post AG Deutsche Post AG` werden auf `Deutsche Post AG` normalisiert.
- Die Namensnormalisierung erfolgt sowohl im Browser-Parser als auch serverseitig vor der Speicherung.
- Bereits vorhandene Importzeilen werden beim Öffnen des Dokumentenimports bereinigt, wenn der Firmenname ausschließlich aus zwei exakt identischen Hälften besteht.

Die Bereinigung führt **keine automatische Kunden-Zusammenführung** durch. Bestehende Kundenzuordnungen und Dolibarr-IDs bleiben unverändert.


## V70 – Alias-/Betriebsnamen beim Kundenabgleich

Feldbeispiele haben gezeigt, dass verschiedene Namen nicht automatisch verschiedene Kunden bedeuten:
- **Cuk Qenja GbR** kann in Dolibarr den Alias/Betriebsnamen **Restaurant Slavija** führen.
- **Manco GmbH** kann einen zusätzlichen/alten Betriebsnamen wie **Restaurant Perla (ehemals Wedelstaedt)** besitzen.

V70-Regeln:
- Dolibarr-Feld `name_alias` wird beim Abgleich berücksichtigt.
- Lokale `customer_aliases` werden beim Kundenmatching berücksichtigt.
- Name und Alias eines Kunden werden als **ein Kandidat** gewertet.
- Dolibarr-Ergebnisse werden vor der Bewertung nach eindeutiger Thirdparty-ID zusammengeführt.
- Wenn lokal kein Treffer existiert, wird **vor jeder Neuanlage zuerst Dolibarr** nach offiziellem Namen, Alias und Anschrift geprüft.
- Ein gefundener Dolibarr-Kunde wird möglichst mit einem vorhandenen lokalen Kunden verknüpft, bevor lokal ein neuer Datensatz entsteht.
- Der im Dokument erkannte Betriebsname wird nach erfolgreicher Zuordnung als Alias gelernt.
- Nur wenn tatsächlich mehrere **verschiedene Dolibarr-IDs** plausibel passen, bleibt der Beleg bei manueller Dublettenprüfung.
- Bestehende Fehlerbelege können mit **erneut prüfen** ohne erneuten Upload neu bewertet werden.


## V71 – Warteschlangen-Autoworker

Die Dokumentenwarteschlange verarbeitet jetzt immer genau **einen** gespeicherten Beleg pro Serveraufruf und holt danach den nächsten frisch aus der Datenbank. Dadurch stoppt ein einzelner problematischer Beleg nicht mehr den gesamten Stapel.

Neu:
- Endpoint `process_next` reserviert atomar den nächsten `queued`-Beleg und setzt ihn zunächst auf `processing`.
- Erfolgreiche Zuordnung endet in `assigned`; unklare Fälle bleiben als manueller Status liegen; technische Fehler werden als `error` markiert.
- Ein fehlerhafter Beleg liefert dem Queue-Worker trotzdem eine verwertbare Antwort, sodass unmittelbar der nächste Beleg folgt.
- Vorhandene Warteschlange startet beim Öffnen der Dokumentenimport-Seite automatisch.
- Nach einem Massen-Upload startet derselbe Worker automatisch und verarbeitet auch ältere wartende Belege.
- **Warteschlange verarbeiten** setzt den Worker jederzeit fort.
- `processing` ist als eigener sichtbarer Status vorhanden.
- Nach einem abgebrochenen Request werden mehr als 10 Minuten alte `processing`-Einträge wieder auf `queued` gesetzt.
- Netzwerkfehler werden mehrfach wiederholt; gespeicherte Dateien bleiben erhalten.


## V72 – Ausgangsrechnungen: Stammdatenschutz, Kundennummern und TSE

Ausgangsrechnungen werden jetzt robuster gegen den bestehenden Dolibarr-Stamm abgeglichen.

Wesentliche Regeln:
- Empfängeradresse wird positionsbezogen aus dem linken Adressblock gelesen; Ansprechpartner bleibt getrennt von der Straße.
- FALKE-Telefon/Fax/E-Mail/Webseite dürfen nicht in Kundenadressen übernommen werden.
- Numerische Kundennummern werden beim Matching ohne führende Nullen verglichen, z. B. `6340 == 06340`; gespeichert bleibt der originale Dolibarr-Code.
- Belegdaten überschreiben keinen vorhandenen Kundenstamm mehr. Wird ein Dolibarr-Kunde gefunden, ist dessen Stammdatensatz führend und wird in den Lizenzmanager zurückgespiegelt.
- Doppelte Firmennamen werden zusätzlich in `outgoing_invoice_imports` bereinigt.
- FALKE-Ausgangsrechnungen erkennen Hardware-ID, technische TSE-Seriennummer und Zertifikatsablaufdatum.
- Erkannte TSEs werden nach erfolgreicher Kundenzuordnung dem zentralen `tse_devices`-Bestand zugeordnet. Bereits einem anderen Kunden zugeordnete TSEs werden nicht automatisch umgehängt.
- Identische bereits importierte PDFs können erneut hochgeladen werden: Metadaten werden aktualisiert und der Beleg erneut eingereiht, ohne Rechnung/Dokument doppelt anzulegen.


## V72 – Ausgangsrechnungen: Adresse und TSE übernehmen

FALKE-Ausgangsrechnungen liefern zusätzliche Stammdaten, die beim Kundenabgleich genutzt werden:
- Empfängerblock wird getrennt in Firma, Ansprechpartner, Straße, PLZ und Ort.
- FALKE-Absender-/Telefon-/Faxdaten dürfen nicht als Kundenstraße übernommen werden.
- Kundenstamm erhält ein eigenes Feld `contact_name`.
- Offensichtlich falsch importierte Straßen (Telefon/Fax/Webdaten im Straßenfeld) dürfen bei erneutem Import durch die sauber erkannte Rechnungsanschrift repariert werden.
- Ausgangsrechnungen werden auf TSE-Daten geprüft: Hardware-ID, technische TSE-Seriennummer, Zertifikatsablauf und Hersteller.
- erkannte TSE wird im zentralen `tse_devices`-Bestand dem Rechnungskunden zugeordnet.
- bestehende TSE-Einträge werden ergänzt und nicht dupliziert.
- eine bereits einem anderen Kunden zugeordnete TSE wird niemals automatisch umgehängt.
- direkter Ausgangsrechnungsimport und Dokumenten-Massenimport verwenden dieselbe TSE-Logik.

Beispiel A008-G26478: Pizzeria Asado / Ghassan Hasso / Hauptstraße 35 / 26215 Wiefelstede, Hardware-ID `602869721347-1926-5`, Zertifikat bis 07.08.2031 und technische TSE-Seriennummer aus dem Rechnungsfuß.


## V73 – Dolibarr-500 / Dubletten / Rechnungsdatum

- Dolibarr-HTML-Fehlerseiten bei HTTP 5xx werden nicht mehr vollständig im UI ausgegeben.
- Temporäre Dolibarr-Fehler (500/502/503/504, Timeout, Verbindungsfehler) werden bis zu drei Mal automatisch erneut versucht und blockieren die Queue nicht.
- Alte V72-HTML-500-Fehler werden automatisch wieder in die Warteschlange gestellt.
- Wiederkehrende Kunden werden zusätzlich über bereits bestätigte Dokumentzuordnungen erkannt.
- Historisch bestätigte FALKE-Kundennummern aus Ausgangsrechnungen werden als starker Match verwendet.
- Bei gleich guten lokalen Dublettenkandidaten wird ein eindeutig mit Dolibarr verknüpfter kanonischer Kunde bevorzugt.
- Das Rechnungsdatum wird nur noch gezielt aus `Rechnungsdatum:` bzw. dem FALKE-Kopf `Wiefelstede, den` gelesen; TSE-Zertifikatsdaten können nicht mehr als Rechnungsdatum übernommen werden.
- Bereits vorhandene PDFs können erneut hochgeladen werden: der SHA-256-Dublettenschutz bleibt aktiv, aber Erkennungsmetadaten werden mit dem aktuellen Parser aktualisiert und nicht abgeschlossene Belege erneut eingereiht.


## V75 – Einheitliche Dokumenten-Pipeline / Lieferantenrouting

Ab V75 entscheidet **nicht mehr der Eingangskanal** über die Fachverarbeitung. E-Mail und Datei-/Massen-Upload verwenden dieselben Lieferantenparser und dieselben Zielprozesse.

### CASPOS / EUCASOFT
- E-Mail und Upload verwenden den gemeinsamen Parser `supplier_invoice_parser_unified.js`.
- Beide Wege laufen anschließend über dieselbe Lizenz-/Rechnungsprüfung in `supplier_invoices.php`.
- Bestehende Fachlogik für Anwender/Kunden, Dongle, Lizenz-Code, TSE, BusinessCard, Kontingente, Auftragsreferenzen und Weiterberechnung bleibt erhalten.
- Erfolgreiche CASPOS/EUCASOFT-Belege werden zusätzlich in `document_intake_items` gespiegelt, damit eine zentrale Dokumenthistorie besteht.

### QUAD
- QUAD ist **Dolibarr-only**.
- Es wird **kein neuer lokaler Lizenzmanager-Kunde** erzeugt.
- Es wird **keine Lizenzmanager-Eingangsrechnung / Rechnungsprüfung** angelegt.
- Lieferanschrift/Endkunde wird ausschließlich gegen Dolibarr geprüft; ein fehlender Geschäftspartner darf dort neu angelegt werden.
- PDF und vorhandenes E-Rechnungs-XML werden beim Dolibarr-Geschäftspartner archiviert.
- Neue QUAD-Importe über `supplier_invoices.php` werden serverseitig abgefangen.
- Bei unklarer Zuordnung kann eine Dolibarr-Thirdparty-ID manuell gesetzt werden, ohne lokalen Lizenzkunden anzulegen.

### Andere eingehende Lieferantendokumente
- Standardroute ist ebenfalls Dolibarr-only.
- Ohne eindeutigen Endkunden bleibt der Beleg zur manuellen Prüfung liegen.
- Automatische lokale Lizenzkunden-Neuanlage erfolgt nur in den ausdrücklich dafür vorgesehenen Fachwegen.

### FALKE-Ausgangsrechnungen
Der V72/V73-Weg bleibt bestehen: lokaler Kundenabgleich, Dolibarr-Ausgangsrechnung/Dokument, TSE-Erkennung und Stammdatenanreicherung.

Bestehende historische QUAD-Datensätze werden aus Sicherheitsgründen nicht automatisch gelöscht oder zusammengeführt; V75 verhindert die unerwünschte Neuanlage künftig.


## V76 – Manuelle Kundenwahl und Belegdatenkorrektur

Problembelege können jetzt vor der Verarbeitung vollständig korrigiert und eindeutig einem Kunden zugeordnet werden.

- Kundenwahl öffnet eine gemeinsame Suche über **Lizenzmanager** und **Dolibarr**.
- Beide Quellen werden getrennt gekennzeichnet; bestehende Verknüpfungen werden sichtbar angezeigt.
- Suche nach Name, Alias, Kundennummer, PLZ und Ort.
- Der gewählte Datensatz wird über **Übernehmen** sofort gespeichert und verarbeitet; `erneut prüfen` ist dafür nicht erforderlich.
- Wird bei einer FALKE-Ausgangsrechnung ein bisher nicht lokal verknüpfter Dolibarr-Kunde gewählt, wird er sicher lokal gespiegelt bzw. mit einem eindeutigen vorhandenen Kunden verknüpft.
- Bei mehreren lokalen Kandidaten wird keine neue Dublette angelegt.
- **Neuen Kunden aus Belegdaten anlegen** ist möglich; der Dublettenschutz bleibt aktiv.
- Bei QUAD/Dolibarr-only erzeugt eine Neuanlage ausschließlich einen Dolibarr-Kunden, niemals einen Lizenzmanager-Kunden.
- Erkannte Belegdaten können über **Belegdaten bearbeiten** korrigiert werden: Belegart, Richtung, Lieferant, Belegnummer/-datum, Kunde/Ansprechpartner, Kundennummer, Anschrift, USt-ID und Beträge.
- Bereits zugeordnete oder gerade verarbeitete Belege werden nicht still verändert.
