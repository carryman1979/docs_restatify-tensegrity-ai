# Tensegrity AI – Dokumentation

Zentrale Dokumentation des Open-Source-Projekts **Tensegrity AI**: föderiertes Lernen mit lokalen Small Language
Models, vier Vertraulichkeitsstufen und einem Hive, der Lern-Deltas vertrauensgewichtet zusammenwebt.

## Inhalt

| Bereich | Pfad |
|---|---|
| Whitepaper V1.0.26 | [whitepaper/](whitepaper/tensegrity-ai-whitepaper-v1.0.26.md) |
| Ökosystem und Diagramme | [architecture/](architecture/oekosystem.md) |
| Komponenten- und Architektur-Design | [architecture/komponentendesign.md](architecture/komponentendesign.md) |
| MVP-Blueprint Phase 2 | [architecture/mvp-blueprint.md](architecture/mvp-blueprint.md) |
| Architekturentscheidungen | [adr/](adr/README.md) |
| Anforderungen (V-Modell links) | [requirements/](requirements/README.md) |
| Teststrategie (V-Modell rechts) | [testing/](testing/teststrategie.md) |
| Traceability | [traceability/](traceability/README.md) |
| Roadmap und Phasen | [roadmap.md](roadmap.md) |
| Zukunfts-Backlog | [backlog/](backlog/backlog.md) |
| Glossar | [glossar.md](glossar.md) |
| Qualitätsagenten und Pre-PR-Check | [.github/agents/](.github/agents/) und [.github/prompts/pre-pr-check.prompt.md](.github/prompts/pre-pr-check.prompt.md) |
| Cloud-Routing mit Slot-Registry | [api_restatify-tensegrity-ai-cloud](https://github.com/carryman1979/api_restatify-tensegrity-ai-cloud) |

## Repositories

| Repository | Inhalt |
|---|---|
| [docs_restatify-tensegrity-ai](https://github.com/carryman1979/docs_restatify-tensegrity-ai) | dieses Repo |
| [proto_restatify-tensegrity-ai](https://github.com/carryman1979/proto_restatify-tensegrity-ai) | Protobuf-Contracts `tensegrity.v1` |
| [core_restatify-tensegrity-ai-slm](https://github.com/carryman1979/core_restatify-tensegrity-ai-slm) | Edge-Kern in Rust |
| [api_restatify-tensegrity-ai-hive](https://github.com/carryman1979/api_restatify-tensegrity-ai-hive) | Hive in Python |
| [api_restatify-tensegrity-ai-cloud](https://github.com/carryman1979/api_restatify-tensegrity-ai-cloud) | Cloud-Backend für WebAssembly |
| [app_restatify-tensegrity-ai](https://github.com/carryman1979/app_restatify-tensegrity-ai) | Uno-App „Restatify – Tensegrity AI“ |

Für die Arbeit an allen Repos gleichzeitig: [tensegrity-ai.code-workspace](tensegrity-ai.code-workspace) öffnen
(alle Repos als Geschwisterordner auschecken).

## KI-Agenten und Skills

Das Projekt wird mit spezialisierten Agenten entwickelt. Projektweite Agenten liegen in
[.github/agents/](.github/agents/), Skills in [.github/skills/](.github/skills/). Einstieg ist der
**Tensegrity Orchestrator**.

## Mitmachen

Siehe [CONTRIBUTING.md](CONTRIBUTING.md), [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) und [SECURITY.md](SECURITY.md).

## Lizenz

Apache License 2.0 – siehe [LICENSE](LICENSE) und [NOTICE](NOTICE).
