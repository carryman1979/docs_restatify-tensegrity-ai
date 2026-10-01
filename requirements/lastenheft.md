# Lastenheft – Tensegrity AI

## 1. Ziel und Produktumfeld

Tensegrity AI ist ein föderiertes KI-Ökosystem für lokale Small Language Models (SLMs), das auf Edge-Geräten lernt, Lern-Deltas je nach Vertraulichkeitsstufe faltet und sie an einen Hive weitergibt. Der Hive webt die Deltas vertrauensgewichtet zusammen, unterteilt sie in Fachspezialisten und verteilt signierte Updates über Hive, P2P und Datenträger zurück.

Das System ist für Benutzer mit Datenschutz- und Compliance-Anforderungen gedacht, insbesondere für Organisationen, die zunächst nur lokale oder vertrauliche Wissensdifferenzen auf Geräten halten wollen, aber trotzdem von gemeinschaftlichen Qualitätsverbesserungen profitieren möchten. Die Open-Source-Umsetzung erfolgt unter Apache-2.0.

## 2. Produktziele

- Lokales Lernen ohne Rohdatenauslagerung.
- Vertraulichkeitsstufen und Berechtigungslogik mit klaren Regeln für „Offen“, „Vertraulich“, „Streng geheim“ und „Nur lokal“.
- Vertrauensgewichtetes Weben im Hive mit Fachbereichs-Experten.
- Signierte Updates und sichere Rollback-Mechanik.
- Mobil- und Desktop-fähige Nutzung mit Performance- und Akku-Budget.
- Einfache, überprüfbare Protokolle für Feedback, Korrekturen und rechtfertigende Belege.

## 3. Stakeholder und Nutzergruppen

- Endnutzer auf mobilen und Desktop-Geräten.
- Organisationen mit Intranet-Hive oder eigenen Hub-Instanzen.
- Entwickler und Maintainer der Projekte `core_restatify-tensegrity-ai-slm`, `api_restatify-tensegrity-ai-hive`, `api_restatify-tensegrity-ai-cloud` und `app_restatify-tensegrity-ai`.
- Betreiber eines Hives mit Trust-, Slot- und Policy-Management.

## 4. Anforderungen an das Gesamtsystem

### REQ-STK-001 – Lokales Lernen ohne Rohdatenfreigabe
- **Priorität:** MUSS
- **Beschreibung:** Die zentrale Lernfunktion muss auf dem Gerät erfolgen; keine Rohdaten oder Klartext-Quellen müssen den Hive verlassen.
- **Begründung:** Whitepaper Abschnitt 1.2, 3.1, 5.1; Vertrauensmodell des Projekts.
- **Abnahmekriterium:** Ein lokales Training schafft ein Delta, das nur nach Faltung und Metadatenübertragung den Hive erreicht; Rohdaten verbleiben auf dem Gerät.
- **Teststufe:** Systemtest / Abnahme

### REQ-STK-002 – Vertraulichkeitsmodell mit vier Stufen
- **Priorität:** MUSS
- **Beschreibung:** Die Anwendung muss die Stufen „Offen“, „Vertraulich“, „Streng geheim“ und „Nur lokal“ unterstützen. Das System muss die strengste Stufe bei Kombinationen übernehmen.
- **Begründung:** Whitepaper Definition 1 und Taint-Regel.
- **Abnahmekriterium:** Für eine gemischte Eingabe gilt die strengste Stufe; Herabstufungen sind nur nach expliziter Nutzeraktion und Protokollierung erlaubt.
- **Teststufe:** Systemtest / Abnahme

### REQ-STK-003 – Faltung und Datenschutzmechanik
- **Priorität:** MUSS
- **Beschreibung:** Für „Vertraulich“ muss eine lokale Faltungsfunktion Clipping, Kompression und Gauß-Rauschen anwenden; „Streng geheim“ darf nur eine Strukturmeldung übermitteln; „Nur lokal“ darf keine Nachricht senden.
- **Begründung:** Whitepaper Abschnitt 2.2 und 2.3.
- **Abnahmekriterium:** Für eine Vertraulichkeitsstufe „Vertraulich“ wird ein DP-Mechanismus mit Privacy Accountant und Clipping validiert; für „Streng geheim“ ist die Nutzlast gleich Null und nur die Strukturmeldung bleibt erhalten; für „Nur lokal“ ist der Netzwerkverkehr 0 Byte.
- **Teststufe:** Integrationstest / Systemtest

### REQ-STK-004 – Vertrauensgewichtetes Weben im Hive
- **Priorität:** MUSS
- **Beschreibung:** Der Hive muss eingehende Deltas nach App-ID, Trust-Level, Fachgebiet und Slot-Kontext bewerten und mit einem Vertrauensgewicht zusammenführen.
- **Begründung:** Whitepaper Abschnitt 2.4, 3.3, 5.4.
- **Abnahmekriterium:** Ein Delta mit Trust-Level 0 wird verworfen; ein gültiges Delta nimmt an Aggregation teil und trägt mit Gewicht T(a) bei.
- **Teststufe:** Systemtest

### REQ-STK-005 – Experten- und Slot-Teilung
- **Priorität:** MUSS
- **Beschreibung:** Das System muss Wissensdeltas in Fachgebiets-Experten und Slots aufteilen. Neue oder gekennzeichnete Slots müssen im Hive verwaltet und an Geräte verteilt werden.
- **Begründung:** Whitepaper Abschnitt 2.5, 4.2 und Architektur-Dokument.
- **Abnahmekriterium:** Ein neues Fachgebiet bekommt einen Slot, der über die Slot-Registry verteilt wird; der Router wählt passende Experten pro Anfrage aus.
- **Teststufe:** Systemtest

### REQ-STK-006 – Rückmeldung mit Belegen und Reputationsdaten
- **Priorität:** MUSS
- **Beschreibung:** Feedback wie Daumen hoch/runter, Korrektur mit Beleg und positive Rückmeldung muss protokollierbar und überprüfbar sein.
- **Begründung:** Whitepaper Abschnitt 3.1, 5.3; Architektur- und Produktanforderungen zur Qualitätssicherung.
- **Abnahmekriterium:** Jede Korrektur enthält eine Referenz auf Beleg, Quelle, Zeitpunkt und Benutzerkontext; falsche oder unvollständige Belege werden nicht akzeptiert.
- **Teststufe:** Integrationstest / Abnahme

### REQ-STK-007 – Signierte Updates und Rollback-Sicherheit
- **Priorität:** MUSS
- **Beschreibung:** Der Release-Prozess muss signierte Adapter-, Delta- und Meta-Pakete erzeugen, die Geräte sicher verwalten und nur mit gültigen Signaturen annehmen.
- **Begründung:** Whitepaper Abschnitt 7.1 sowie Architektur- und Release-Entscheidungen.
- **Abnahmekriterium:** Ein nicht signiertes oder heruntergestuftes Paket wird abgelehnt; die Aktualisierung ist nur mit gültiger Versions- und Hash-Prüfung zulässig.
- **Teststufe:** Systemtest

### REQ-STK-008 – Intranet und P2P-Verteilung
- **Priorität:** SOLL
- **Beschreibung:** Die Verteilung muss über Internet-Hive, Intranet-Hive, Peer-to-Peer und Datenträger erfolgen können, sofern die Richtlinien und die Trust-Policy dies erlauben.
- **Begründung:** Architektur-Dokument und Release-Design.
- **Abnahmekriterium:** Ein lokales Hive-Klon-Kontingent kann signierte Updates importieren; P2P- und Datenträger-Pakete werden nur bei gültiger Signatur akzeptiert.
- **Teststufe:** Integrationstest

### REQ-STK-009 – WASM-/Cloud-Einschränkungen und mobile Performance
- **Priorität:** MUSS
- **Beschreibung:** In WebAssembly muss die lokale Spezialfunktionalität für „Streng geheim“ und „Nur lokal“ gesperrt sein; die App muss auf Mobilgeräten mit begrenzter Leistung und Akkuressourcen zuverlässig laufen.
- **Begründung:** Whitepaper Abschnitt 2.1 und Plattformstrategie; Uno-Anspruch für mobile Nutzung.
- **Abnahmekriterium:** Ein Browser-Client darf keine Streng-geheim- oder Nur-lokal-Operationen starten; mobile Messungen bleiben innerhalb des definierten Performance-Budgets.
- **Teststufe:** Systemtest / Abnahme

### REQ-STK-010 – Auditierbarkeit und Sicherheit
- **Priorität:** MUSS
- **Beschreibung:** Das System muss Sicherheits-, Trust-, Ein-/Ausgabeverarbeitung und Audit-Protokolle nachvollziehbar halten, um Geheimhaltung, Signaturprüfung und Abweichungen nachverfolgen zu können.
- **Begründung:** Sicherheitsziel, Whitepaper, Risiko- und Trust-Modell.
- **Abnahmekriterium:** Jeder kritische Vorgang erzeugt einen verknüpfbaren Eintrag (Ziel, Zeit, App-ID, Entscheidung, Grund); kritische Verstöße werden geloggt und blockiert.
- **Teststufe:** Systemtest

## 5. Nicht-Ziele

- Eine vollständige, proprietäre Closed-Source-Ökonomie statt Open Source.
- Das Trainieren auf Rohdaten im Hive.
- Das Zulassen von „Streng geheim“ bzw. „Nur lokal“ im Browser ohne Cloud-Fallback.
- Die automatische Herabstufung von Inhalten ohne Nutzeraktion oder Protokollierung.

## 6. Abnahmekriterien für die erste Version 1

Version 1 gilt als freigegeben, wenn:

1. Die vier Stufen sind im Systemmodell und in den Contracts definiert.
2. Die Faltungslogik und die Null-Sendungsregel für „Nur lokal“ sind durch Tests belegt.
3. Der Hive akzeptiert, verwirft und aggregiert Deltas nach Trust-Policy.
4. Das Release-System prüft Signatur, Version und Hash.
5. Die Proto-Contracts und die wichtigsten Systemtests sind lokal grün.

---

Quelle: Whitepaper V1.0.26, Architektur-Ökosystem, ADR 0001 bis 0010, Roadmap Phase 1.
