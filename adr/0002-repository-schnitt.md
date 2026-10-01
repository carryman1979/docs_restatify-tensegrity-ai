# ADR-0002: Repository-Schnitt

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Vier Sprachen (Rust, Python, C#, Protobuf), unterschiedliche Release-Zyklen und Zielgruppen (App-Nutzer, Hive-Betreiber,
Cloud-Betreiber).

## Entscheidung

Sechs Repositories unter `github.com/carryman1979`:

| Repo | Inhalt |
|---|---|
| `docs_restatify-tensegrity-ai` | Dokumentation, Querschnitts-Agenten und -Skills, Workspace-Datei |
| `proto_restatify-tensegrity-ai` | Protobuf `tensegrity.v1` (Single Source of Truth für Schnittstellen) |
| `core_restatify-tensegrity-ai-slm` | Rust-Core inkl. FFI und Relay |
| `api_restatify-tensegrity-ai-hive` | Hive (Python) |
| `api_restatify-tensegrity-ai-cloud` | Cloud-Backend (C# Gateway, vLLM, Helm) |
| `app_restatify-tensegrity-ai` | Eine Uno-App für alle Plattformen inkl. WASM |

## Alternativen

Monorepo: einfachere übergreifende Änderungen, aber gemischte Toolchains, große CI und unklare Release-Einheiten.

## Konsequenzen

Contracts werden versioniert konsumiert (Git-Tag bzw. generierte Pakete). Querschnittsänderungen laufen über den
Orchestrator-Agenten und die Multi-Root-Workspace-Datei.
