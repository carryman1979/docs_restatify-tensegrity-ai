# Teststrategie (rechte Seite des V-Modells)

| Teststufe | prüft gegen | Werkzeuge | Ort |
|---|---|---|---|
| **Unittest** | Einheiten/Funktionen | Rust: `cargo nextest`, `proptest`, `insta`; Python: `pytest`, `hypothesis`; C#: MSTest | je Repo, bei jedem Commit |
| **Komponententest** | `CMP-…` | wie oben, Komponente isoliert mit Fakes (z. B. `tg-fold` mit echtem Accountant) | je Repo, CI |
| **Integrationstest** | `ARC-…`, Contracts | Testcontainers (Postgres, MinIO, Keycloak), `buf breaking`/`buf lint`, Core ↔ Hive über echte gRPC-Verbindung | je Repo + Docs-Workflow |
| **Systemtest** | `REQ-SYS-…` | Ende-zu-Ende über alle Repos; Playwright (WASM), Uno.UITest/Appium (mobil), Netzwerkmitschnitt | Phase 7, vor jedem Release |
| **Abnahmetest** | `REQ-STK-…` | Szenarien aus dem Lastenheft | vor Release |

## Querschnittliche Prüfungen

- **Datenschutz-Leck-Tests:** „Nur lokal“ = 0 Byte; Streng geheim nie Gewichte; DP-Kalibrierung gegen Referenzwerte
  (z. B. ε = 0,03125, δ = 10⁻⁵, Δ₂ = 1 → σ ≈ 155).
- **Sicherheit:** Signatur-, Downgrade-, Replay-Tests; Vergiftung; Fuzzing der Paket-Parser (`cargo fuzz`).
- **Performance (Mobil):** Kaltstart, Tokens/s, RAM-Spitze, Temperatur/Akku beim Leerlauftraining – Referenzgeräte in
  Phase 2 festlegen.
- **Lizenzen:** `cargo deny`, `pip-licenses`, `dotnet-project-licenses`.

## Namenskonvention

Testnamen verweisen auf die Anforderung, z. B. `req_sys_012_audit_log_for_blocked_submission`. Die Traceability-Matrix wird
daraus generiert.
