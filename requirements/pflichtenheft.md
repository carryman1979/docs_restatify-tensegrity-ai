# Pflichtenheft – Tensegrity AI

## 1. Zweck und Systemgrenzen

Das System umfasst die lokalen Lern- und Faltungsfunktionen auf Geräten, die Hive-Services für Aggregation, Trust und Release-Verteilung sowie die Cloud-/WASM-Integration für Inferenz und Update-Requests. Das Pflichtenheft präzisiert die technische Umsetzung der Anforderungen aus dem Lastenheft und bildet die Grundlage für die Protobuf-Contracts und die V-Model-Tests.

## 2. Systemarchitektur

### 2.1 Komponenten

- Edge-Core (Rust): Inferenz, LoRA-Training, Faltung, Privacy Accountant, Update-Request, P2P-Transport.
- Hive (Python): Schnittstellen-API, Trust, Aggregation, Slot-Registry, Release-Signatur, Import/Export.
- Cloud-Backend (C#): Router, Generalist-SLM, Experten-Adapter, gRPC-Web-Gateway.
- Uno-App: UI, Nutzer-Feedback, lokale Entscheidung zur Vertraulichkeitsstufe, Anzeige der Gerätegrundlage, Benachrichtigungen.

### 2.2 Vertrauensgrenzen

- Gerät: vertrauliche lokale Daten, W_PI, LoRA-Slots, lokale Bewertungslogik.
- Hive: aggregierte, gefaltete Deltas und Metadaten; keine Rohdaten.
- Cloud: nur für nicht streng geheime Inferenz, sofern freigegeben.
- Edge-Peer und Intranet-Hive: signierte Paket- und Release-Exchange, mit Policy-Checks.

## 3. Technische Anforderungen

### REQ-SYS-001 – Vertraulichkeitslogik mit vier Stufen
- **Quelle:** REQ-STK-002; Whitepaper 2.1
- **Priorität:** MUSS
- **Beschreibung:** Das System muss eine `ConfidentialityLevel`-Enumeration definieren: `OFFEN`, `VERTRAULICH`, `STRENG_GEHEIM`, `NUR_LOKAL`. Die strengste Stufe bestimmt das Verhalten bei Kombinationen.
- **Abnahmekriterium:** Die API akzeptiert nur definierte Stufen; Mischungen werden als Maximum der Stufen verarbeitet; Herabstufungen sind nicht implizit erlaubt.
- **Teststufe:** Einheit / Integration

### REQ-SYS-002 – Bitmaske und Taint-Regel
- **Quelle:** REQ-STK-002; Whitepaper 2.1
- **Priorität:** MUSS
- **Beschreibung:** Jede Stufe muss auf eine Berechtigungsmaske mit sechs Bit-Feldern abgebildet werden. Die Taint-Regel ermittelt `max(levels)`.
- **Abnahmekriterium:** Es gibt eine eindeutige Zuordnung von Stufe zu Bitmasken und eine deterministische Taint-Berechnung für gemischte Eingaben.
- **Teststufe:** Einheit

### REQ-SYS-003 – Lokales Training und maskierte Aktualisierung
- **Quelle:** REQ-STK-001; Whitepaper 3.1
- **Priorität:** MUSS
- **Beschreibung:** Training darf nur über LoRA-Adapter erfolgen; `W_basis` bleibt unverändert. Beispiele mit `STRENG_GEHEIM` oder `NUR_LOKAL` aktualisieren nur `W_PI`; alle anderen Aktualisierungen betreffen `W_logik`.
- **Abnahmekriterium:** Keine Änderung an Basisgewichten; maskierte Gradienten für streng geheime und lokale Daten sind validiert.
- **Teststufe:** Komponententest / Integrationstest

### REQ-SYS-004 – Faltungsfunktion mit Clipping und DP-Schema
- **Quelle:** REQ-STK-003; Whitepaper 2.2 und 2.3
- **Priorität:** MUSS
- **Beschreibung:** `fold(delta, level)` muss Clipping, Kompression und bei „Vertraulich“ Gauß-Rauschen mit einem Privacy Accountant anwenden. Beim Null-Delta für „Streng geheim“ wird eine Strukturmeldung erzeugt.
- **Abnahmekriterium:** `NUR_LOKAL` erzeugt keine Netzwerknutzlast; `STRENG_GEHEIM` erzeugt nur eine kleine Strukturmeldung; `VERTRAULICH` startet mit einem DP-Parameter und Budget-Protokoll.
- **Teststufe:** Komponententest / Systemtest

### REQ-SYS-005 – Null-Sendungs- und 0-Byte-Regel
- **Quelle:** REQ-STK-001, REQ-STK-003; Whitepaper 3.3
- **Priorität:** MUSS
- **Beschreibung:** Wenn die Stufe `NUR_LOKAL` ist, muss keine Einreichung an den Hive oder andere Peers erfolgen. Der Netzwerkverkehr muss 0 Byte betragen.
- **Abnahmekriterium:** Der Sender produziert keine Nachricht und keinen Transportauftrag; der Prüfpfad meldet den Status `skipped_local_only`.
- **Teststufe:** Systemtest

### REQ-SYS-006 – Hive-Akzeptanz und Trust-Score-Logik
- **Quelle:** REQ-STK-004; Whitepaper 2.4, 4.3
- **Priorität:** MUSS
- **Beschreibung:** Der Hive akzeptiert nur signierte Anfragen mit gültigem App- und Device-Kontext. Einträge mit Trust-Level 0 oder mit ungültiger Signatur werden verworfen. Aggregation erfolgt mit `T(a)` als Gewicht.
- **Abnahmekriterium:** Trust-Level-0-Einträge führen zu `REJECTED`; gültige Deltas werden als gewichtete Beiträge aggregiert.
- **Teststufe:** Integrationstest / Systemtest

### REQ-SYS-007 – Slot-Registry und Expertenauswahl
- **Quelle:** REQ-STK-005; Whitepaper 2.5
- **Priorität:** MUSS
- **Beschreibung:** Der Hive pflegt eine Slot-Registry mit Fachgebiet, Kategorie, Rang und Lebenszyklus. Jeder Router wählt die passende Expertenkombination pro Anfrage.
- **Abnahmekriterium:** Jede neue Slot-Registrierung ist mit Fachgebiet und Kategorie belegt; der Router kann eine passende Expertenliste deterministisch erzeugen.
- **Teststufe:** Komponententest / Integrationstest

### REQ-SYS-008 – Feedback-, Korrektur- und Beleg-Handling
- **Quelle:** REQ-STK-006; Whitepaper 3.1
- **Priorität:** MUSS
- **Beschreibung:** Feedback-Events, Korrekturen mit Belegen und positive Rückmeldungen werden in strukturierter Form über die Feedback-API weitergeleitet und protokolliert.
- **Abnahmekriterium:** Ein Feedback-Event enthält User-ID, Zeitstempel, Datenquelle und Beleg-Referenz; leere oder falsche Belege werden abgewiesen.
- **Teststufe:** Integrationstest

### REQ-SYS-009 – Update-Release und Signaturprüfung
- **Quelle:** REQ-STK-007; Whitepaper 7.1
- **Priorität:** MUSS
- **Beschreibung:** Das Release-System muss Signaturen, Versionsnummern und Hashes prüfen. Ein Downgrade oder eine unvollständige Paket-Liste wird abgelehnt.
- **Abnahmekriterium:** Ein Paket ohne gültige Signatur oder mit falschem Hash kann nicht installiert werden; der Rollback wird verhindert.
- **Teststufe:** Systemtest

### REQ-SYS-010 – P2P-, Intranet- und Datenträger-Exchange
- **Quelle:** REQ-STK-008; Whitepaper 7.1, 7.3
- **Priorität:** SOLL
- **Beschreibung:** Exchange-Pakete können über direkte Peer-Verbindungen, Intranet-Hives und Datenträger ausgetauscht werden. Die Validierung erfolgt unabhängig vom Kanal.
- **Abnahmekriterium:** Ein importiertes Paket wird nur akzeptiert, wenn Signatur, Trust und Meta-Hash gültig sind; der Kanal selbst ist nicht vertrauenswürdig.
- **Teststufe:** Integrationstest

### REQ-SYS-011 – WASM-/Cloud-Restriktionen und Gerät-Policy
- **Quelle:** REQ-STK-009; Whitepaper 2.1, 5.2
- **Priorität:** MUSS
- **Beschreibung:** Im Browser darf nichts für `STRENG_GEHEIM` oder `NUR_LOKAL` ausgeführt werden; Cloud-Inferenz ist nur mit user-granted Freigabe und gelegentlich eingeschränkter Berechtigung erlaubt.
- **Abnahmekriterium:** Browser-Clients können keine lokalen Streng-Geheim- oder Nur-lokal-Operationen starten; die Policy-Klasse liefert eine entsprechende Fehlermeldung.
- **Teststufe:** Systemtest

### REQ-SYS-012 – Audit, Security und Fehlerprotokoll
- **Quelle:** REQ-STK-010; Whitepaper 5.3, Architektur-Bedrohungsmodell
- **Priorität:** MUSS
- **Beschreibung:** Kritische Entscheidungen müssen mit Zeitstempel, App-ID, Ursache und Ergebnis protokolliert werden. Fehlermeldungen und Blockaden müssen an die zuständigen Absturz- und Audit-Mechanismen weitergereicht werden.
- **Abnahmekriterium:** Jede blockierte oder verworfene Einreichung produziert einen nachvollziehbaren Log-Eintrag; Signaturfehler werden systematisch getrackt.
- **Teststufe:** Systemtest

## 4. Qualitätsziele

- Datenschutz: Streng geheime Inhalte bleiben auf dem Gerät; nur Strukturmetadaten werden geteilt.
- Sicherheit: Signatur, Hash, Trust-Policy, Rollback-Blockierung.
- Performance: Leerlauftraining nur mit definierten Ressourcen-/Temperaturgrenzen; mobile Geräte laufen ohne unnötige Rechenlast.
- Zuverlässigkeit: User-Feedback und Updates sind reproduzierbar und auditable.

## 5. Versionierungs- und Release-Regeln für V1

- Protobuf-Contracts gelten als Single Source of Truth für die Schnittstellen.
- Jede Änderung an APIs erfolgt zuerst in `proto_restatify-tensegrity-ai`.
- Die erste Version `v1` ist nur freigegeben, wenn alle relevanten lokalen Tests in den betroffenen Repos grün sind.
- Ein Commit erfolgt erst nach lokaler Verifikation und Dokumentation der Ergebnisse.

---

Verweise: Lastenheft, Whitepaper V1.0.26, ADR 0001-0010, Architektur-Ökosystem, Roadmap Phase 1.
