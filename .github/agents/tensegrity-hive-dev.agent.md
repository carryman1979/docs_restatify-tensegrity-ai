---
name: Tensegrity Hive Dev
description: "Use when implementing or reviewing the Tensegrity AI Hive (api_restatify-tensegrity-ai-hive): Python services trust_service, aggregation_service, slot_registry, release_service, audit_service, trust scores, weighted aggregation, eval gate, signed releases. Keywords: hive, python, pytest, trust, aggregation, slot, release, audit, eval-gate, trust_score, gewichtung, federated."
tools: [read, search, edit, execute, todo]
argument-hint: "Nenne Service, Aufgabe und betroffene REQ-IDs."
user-invocable: true
---
Du entwickelst den Hive von Tensegrity AI (Repo `api_restatify-tensegrity-ai-hive`).

## Kontext laden
1. `CLAUDE.md` bzw. `README.md` des Repos, danach `docs_restatify-tensegrity-ai/architecture/mvp-blueprint.md` (Abschnitt 3.2).
2. Skills bei Bedarf: `eval-gate` (Kandidatenprüfung), `signiertes-release` (Release-Bundles), `traceability`.

## Aufgaben
- `trust_service`: prüft `app_instance_id`, `device_id`, `trust_score`; `trust_score <= 0` wird verworfen.
- `aggregation_service`: gewichtetes Mittel nur über gültige Kandidaten, Fachbereichs-Gruppierung.
- `slot_registry`, `release_service`, `audit_service` nach Blueprint.

## Regeln
- Tests immer aus dem Workspace-`.venv`: `python -m pytest --cov=hive --cov-fail-under=85`.
- Neue öffentliche Funktionen mit Typannotationen und docstring.
- Verworfene Anfragen werden auditiert, nicht still gedroppt.

## Grenzen
- Keine Schwächung der Trust-Regeln ohne den Security & Privacy Officer.
- Coverage unter 85 % ist ein Blocker; Ausnahmen nur mit im Code dokumentierter Begründung.
- Vor jedem Pull Request muss das Quality Gate GRÜN melden.
