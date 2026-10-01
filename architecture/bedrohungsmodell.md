# Bedrohungsmodell (Startfassung)

Methode: STRIDE je Vertrauensgrenze. Wird in Phase 1 vervollständigt.

## Vertrauensgrenzen

| # | Grenze | Richtung |
|---|---|---|
| G1 | App/Core ↔ Netzwerk (Hive, Cloud) | Gerät ↔ außen |
| G2 | Core ↔ P2P-Peers | Gerät ↔ fremdes Gerät |
| G3 | Datenträger-Import/Export | Gerät/Hive ↔ Medium |
| G4 | Hive ↔ Hive-Klon (Upstream) | Betreiber ↔ Betreiber |
| G5 | Browser (WASM) ↔ Cloud-Backend | Nutzer ↔ Cloud |
| G6 | Lokaler Speicher (W_PI, Verlauf) ↔ andere Apps/Diebstahl | Gerät intern |

## Wesentliche Bedrohungen

| ID | STRIDE | Bedrohung | Gegenmaßnahme | Test |
|---|---|---|---|---|
| T1 | I | Abfluss von Streng-geheim/Nur-lokal-Inhalten über Telemetrie, Logs, Crash-Reports, Cloud-Inferenz | Maske vor jeder Ausgabe, Log-Filter, Bit 4 | Systemtest 0 Byte |
| T2 | I | Rekonstruktion aus Vertraulich-Deltas | Clipping, Gauß-Rauschen, Accountant | Kalibrierungs- und Budget-Tests |
| T3 | I | Profilbildung über Strukturmeldungen | grobe Kategorien, Bündelung, k_min, UI/AGB-Hinweis (ADR-0010) | Review der Kategorienliste |
| T4 | T | Vergiftung über manipulierte Deltas oder Strukturmeldungen | Trust, Clipping, robuste Aggregation, Eval-Gate, Blacklist | Vergiftungs-Systemtest |
| T5 | T/S | Manipulierte Releases über P2P/Datenträger | TUF-Signaturen, Hash, kein Downgrade | Signatur-/Downgrade-/Replay-Tests |
| T6 | S | Gefälschte App-ID | signierte Einreichungen mit Geräteschlüssel, Registrierung | Integrationstest |
| T7 | D | Überlastung des Hive durch Einreichungen | Kontingente pro App-ID, Puffer | Lasttest |
| T8 | E | Speicher-Korruption durch Paket-Parser im Core | Rust, Größenlimits, Fuzzing | `cargo fuzz` |
| T9 | I | Diebstahl des Geräts | verschlüsselter Vault mit Plattform-Keystore | Komponententest |
| T10 | R | Bestreiten von Betreiber-Eingriffen (f_betreiber, Blacklist) | Audit-Log im Hive | Integrationstest |
