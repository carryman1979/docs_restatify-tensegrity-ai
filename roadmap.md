# Roadmap und Phasen

Das Projekt folgt dem **V-Modell**. Jede Phase endet mit einem Review gegen die Kriterien unten.

| Phase | Inhalt | Ergebnis / Abnahmekriterium |
|---|---|---|
| 0 Fundament | Repos, Lizenz, Community-Dateien, Agenten und Skills, Whitepaper V1.0.26, Ökosystem 2.0, Backlog | Alle Repos lokal angelegt, Agenten nutzbar, Freigabe zum Push |
| 1 Anforderungen und Architektur | Lastenheft (REQ-STK), Pflichtenheft (REQ-SYS), arc42, ADRs, Bedrohungsmodell, Contracts v1, Testplan | Jede REQ-SYS hat Architekturbezug und Teststufe |
| 2 Spikes | S1–S8 (siehe unten) | Messwerte und ADR je Spike |
| 3 Core | Crates `tg-*`, FFI | Unit- und Komponententests grün auf allen Zielplattformen |
| 4 Hive | Annahme, Weben, Registry, Eval-Gate, Release, Rollen | Integrationstests mit Testcontainers |
| 5 App | Uno-App nativ + WASM | UI-Tests (Playwright für WASM, Appium/Uno.UITest mobil) |
| 6 Cloud | Gateway, Router, vLLM, Helm | Lasttest, OIDC-Ende-zu-Ende |
| 7 Integration, System, Release | Ende-zu-Ende über alle Repos, Sicherheitstests | Systemtests bestanden, Release 0.1.0 |

Phasen 3, 4 und 6 können parallel laufen, sobald die Contracts aus Phase 1 stehen.

## Spikes (Phase 2)

| ID | Frage | Erfolgskriterium |
|---|---|---|
| S1 | llama.cpp mit LoRA-Hot-Swap über Rust auf allen Plattformen | Adapterwechsel < 200 ms auf Mittelklasse-Android |
| S2 | On-Device-LoRA-Training (candle / llama.cpp finetune / ONNX Runtime Training) | 1 Trainingsschritt Rang 8 auf 1,7B-Q4 in akzeptabler Zeit und Temperatur; sonst LAN-Desktop-Fallback |
| S3 | FFI Rust ↔ Uno auf 6 Plattformen (uniffi-bindgen-cs / csbindgen) | Aufruf + Streaming-Callback auf allen nativen Zielen |
| S4 | libp2p-Mesh mit Relay und Paketaustausch | Zwei Geräte hinter NAT tauschen ein signiertes Paket |
| S5 | Hive-Pipeline PEFT → Weben → GGUF-Export | Adapter vom Hive läuft im Core |
| S6 | vLLM Multi-LoRA + ASP.NET gRPC-Web aus Uno-WASM | Streaming-Antwort im Browser |
| S7 | Strukturmeldung und Slot-Matching für Streng geheim | Null-Slot entsteht ab k_min, lokales Prüf-Gate mit Rollback |
| S8 | Hive → Klon per Datenträger mit Neusignierung | Gerät am Klon akzeptiert nur Anker des Klons |

## Systemtest-Pflichtfälle (Phase 7)

- „Nur lokal“ erzeugt **0 Byte** Netzwerkverkehr (Mitschnitt).
- Streng geheim sendet nie Gewichte; manipulierte Einreichung wird vom Hive verworfen.
- Vergiftungsversuch wird durch Clipping/Trust/Eval-Gate abgefangen.
- Blacklist (f_betreiber = 0) wirkt sofort.
- Rollback eines fehlerhaften Releases auf Geräten.
- Klon-Import per Datenträger ohne Netzverbindung.
- Downgrade- und Replay-Angriffe auf Releases scheitern.
