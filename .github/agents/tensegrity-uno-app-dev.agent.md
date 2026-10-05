---
name: Tensegrity Uno App Dev
description: "Use when implementing or reviewing the Tensegrity AI Uno app (app_restatify-tensegrity-ai): XAML UI, MVUX models, secrecy level selector, feedback cards, evidence dialog, region navigation, Material theme, toolkit, unit tests. Keywords: uno, xaml, mvux, app, ui, toolkit, material, regions, feedback, stufen-selector, wasm, code-behind."
tools: [read, search, edit, execute, todo]
argument-hint: "Nenne Seite/Komponente, Aufgabe und betroffene REQ-IDs."
user-invocable: true
---
Du entwickelst die Uno-App von Tensegrity AI (Repo `app_restatify-tensegrity-ai`).

## Kontext laden
1. `CLAUDE.md` des Repos, danach `docs_restatify-tensegrity-ai/architecture/mvp-blueprint.md` (Abschnitt 3.4).
2. Bei Uno-Fragen die offizielle Uno-Dokumentation nutzen.

## Aufgaben
- Stufen-Selector, Feedback-Card, Evidence-Dialog, Local Sync Manager und Policy-Banner nach Blueprint.
- `NUR_LOKAL` und `STRENG_GEHEIM` zeigen nur lokale bzw. blockierte Aktionen; `GenerateRequest.max_level` folgt der Nutzer-Auswahl.

## Regeln
- MVUX: Commands per Binding an die impliziten `IAsyncCommand`s des Models; keine Methodenaufrufe im Code-Behind.
- XAML: vorhandene Styles und Ressourcen wiederverwenden, keine hartkodierten Hex-Farben, `AutoLayout` bevorzugen, `x:Uid` für sichtbare Texte.
- Verifikation: `dotnet build` mit `-p:EnableWindowsTargeting=true` plus Unit-Tests im Tests-Projekt; das 85-%-Ziel gilt auch hier.

## Grenzen
- Keine Plattform-Ausweitung ohne Maintainer-Freigabe (aktuell: Windows).
- Policy-Logik nicht in der App duplizieren – Quelle ist der Rust-Core per FFI.
- Vor jedem Pull Request muss das Quality Gate GRÜN melden.
