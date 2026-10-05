# MVP-Blueprint Phase 2 – Core, Hive und Cloud

## 1. Zielsetzung

Die Phase 2 stellt die erste realisierbare MVP-Basis dar: ein lokales Gerät, ein Hive, ein Cloud-Routing-Service und eine Uno-App mit sauberer Vertrauenslogik. Die Architektur bleibt kompatibel zur Protobuf-Schnittstelle `tensegrity.v1` und zu den Anforderungen aus dem Lasten- und Pflichtenheft.

## 2. Scope des MVP

### In Scope

- `ConfidentialityLevel`-Enum und Taint-Logik
- lokale LoRA-Training- und Faltungslogik
- lokale `NUR_LOKAL`-Blockade
- Hive-Aggregation mit Trust-Policy
- Release-Validation mit Hash + Signatur
- Routing mit Generalist + Experten in der Cloud
- Uno-App mit Stufen-Auswahl und Feedback

### Out of Scope

- vollständige sichere Aggregation (Phase 3/4)
- komplexe P2P-Topologien mit breiter Multi-Hive-Topologie
- vollständiger vLLM-Produktivbetrieb mit GPU-Autoscaling
- mobile Performance-Optimierung auf Referenzgeräten vor Release

## 3. MVP-Architektur je Komponente

### 3.1 Core (Rust)

#### Module

- `tg-policy`
  - `enum ConfidentialityLevel`
  - `PermissionsMask`
  - `taint_max(levels)`
  - `is_browser_blocked(level)`
- `tg-fold`
  - `fold(delta, level)`
  - `clip(delta, max_norm)`
  - `compress(delta)`
  - `apply_dp_noise(delta, epsilon, delta2)`
  - `derive_structure_message(level)`
- `tg-learn`
  - `mask_gradients(example_level, base_weights, loRA_weights)`
  - `train_local(adapter, examples)`
- `tg-sync`
  - `build_submission(delta, level, app_id)`
  - `build_update_request(version, slots)`
- `tg-ffi`
  - vereinfachte Bridge für Uno-App 

#### Erste Verifikation

- `tg-policy` hat Unit-Tests für:
  - `OFFEN < VERTRAULICH < STRENG_GEHEIM < NUR_LOKAL`
  - `max(levels)` bei gemischten Beispielen
  - `NUR_LOKAL` ist immer ein hard stop für Transport
- `tg-fold` hat Unit-Tests für:
  - `NUR_LOKAL` → keine Nutzlast
  - `STRENG_GEHEIM` → nur Strukturmeldung
  - `VERTRAULICH` → DP-Noise aktiv

### 3.2 Hive (Python)

#### Module

- `trust_service.py`
  - prüft `app_instance_id`, `device_id`, `trust_score`
  - verwirft `trust_score <= 0`
- `aggregation_service.py`
  - `weighted_average(deltas, trust)`
  - "+ Fachbereichs-Gruppierung"
- `slot_registry.py`
  - Registry für `slot_id`, `category_id`, `owner`, `allow_distribution`
  - deterministische Auflösung aktiver Slots für Router und Aggregation
- `release_service.py`
  - signierte Releses mit `ReleaseBundle`
  - Validierung von `version`, `hash`, `manifest`
- `audit_service.py`
  - Logdatenstruktur für blockierte und verworfene Anfragen

#### Erste Verifikation

- `trust_service` prüft Signatur und Trust-Score
- `aggregation_service` verwirft `T(a)=0`
- `release_service` lehnt unvollständige oder falsche `ReleaseBundle` ab

### 3.3 Cloud-Backend (C#)

#### Module

- `Gateway` mit ASP.NET Core gRPC-Web
- `PolicyGuard`
  - Schützt `STRENG_GEHEIM` und `NUR_LOKAL` im WASM-Pfad
- `Router`
  - Generalist-Modell zuerst
  - dann Experten-Auswahl nach Slot-/Kategorie-Kontext
- `InferenceService`
  - übernimmt `GenerateRequest` / `GenerateResponse`

#### Erste Verifikation

- Browser/ WASM darf `STRENG_GEHEIM` nicht anfragen
- `GenerateRequest` muss `max_level` validieren und per Policy akzeptieren
- `Expert routing` funktioniert mit einem bekannten Slot-Set

### 3.4 App (Uno)

#### UI-Komponenten

- Stufen-Selector je Kontext
- Feedback-Card mit Daumen hoch/runter
- Evidence-Dialog mit Citation / Quelle
- Local Sync Manager
- Cloud status and policy banner

#### Erste Verifikation

- `NUR_LOKAL` und `STRENG_GEHEIM` zeigen nur lokale oder blockierte Aktionen an
- Feedback mit unvollständigem Beleg wird verworfen
- `GenerateRequest.max_level` folgt der Auswahl des Nutzers

## 4. Schnittstellen in der MVP-Phase

### `common.proto`

- `ConfidentialityLevel`
- `DeviceInfo`
- `TraceContext`
- `EvidenceRef`

### `federation.proto`

- `DeltaSubmission`
- `SubmissionAck`

### `release.proto`

- `UpdateRequest`
- `ReleaseBundle`
- `UpdateAck`

### `feedback.proto`

- `FeedbackEvent`
- `FeedbackAck`

## 5. Reihenfolge der Implementierung

1. `tg-policy` + `common.proto` + erste Tests
2. `tg-fold` + `NUR_LOKAL` / `STRENG_GEHEIM`-Verhalten
3. `federation.proto` im Hive und Core validieren
4. `release.proto` mit Signatur- und Hash-Prüfung
5. Cloud-Router mit PolicyGuard
6. Uno-App mit Stufen-UI und Feedback
7. Lokale Infrastruktur für Integrationstests (Cloud + Hive) aufsetzen

## 6. Definition of Done für MVP

Die MVP-Phase gilt als abgeschlossen, wenn:

- alle vier Vertraulichkeitsstufen in den Contracts und Policy-Regeln valid sind
- `NUR_LOKAL` keine Netzwerk-Nutzlast erzeugt
- der Hive `Trust=0` und ungültige Pakete verwirft
- signierte Releases validiert und abgelehnt werden können
- die App die Cloud-Policy korrekt umsetzt
- die lokalen Tests für die Core/Hive/Contract-Basis grün sind

## 7. Nächster konkreter Schritt

Als unmittelbarer nächster Schritt wird die erste minimale Core-/Hive-Implementierung angelegt: `tg-policy` für Rust plus eine kleine Python-API-Struktur im Hive mit gültigen Testfixtures. Danach folgen die ersten `dotnet`- und Uno-Scaffolds.
