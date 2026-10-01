# CLAUDE.md – docs_restatify-tensegrity-ai

Zentrale Dokumentation von Tensegrity AI (Apache-2.0). Gilt auch als Einstieg für alle KI-Agenten.

## Wichtigste Quellen
- `whitepaper/tensegrity-ai-whitepaper-v1.0.26.md` – fachliche Referenz; bei Widerspruch gewinnt das Whitepaper.
- `architecture/oekosystem.md`, `architecture/*.mmd` – Komponenten und Flüsse.
- `adr/` – getroffene Entscheidungen; nicht ohne neues ADR umwerfen.
- `roadmap.md` – Phasen, Spikes, Systemtest-Pflichtfälle.
- `backlog/backlog.md` – Zukunftsideen (BL-xxx), nicht im MVP.

## Regeln
- Deutsch mit echten Umlauten; Dateinamen ASCII.
- Keine Inhalte aus `Restatify AI/_extracted` oder Firmen-/Bank-/Steuerdaten übernehmen.
- Kein `git push` ohne Freigabe des Maintainers.
- Agenten: `.github/agents/`, Skills: `.github/skills/`. Einstieg: Tensegrity Orchestrator.
- Multi-Repo-Arbeit über `tensegrity-ai.code-workspace`.
