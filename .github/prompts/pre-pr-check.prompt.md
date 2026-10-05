---
description: "Verbindlicher Qualitätscheck vor jedem Tensegrity-AI-Pull-Request: Clean Code Review, Dokumentations-Sync und Test-/Coverage-Gate für die geänderten Repos."
name: Pre-PR Check
agent: "agent"
argument-hint: "Nenne die geänderten Repos oder die PR-Beschreibung."
---
Führe den verbindlichen Pre-PR-Check für Tensegrity AI durch. Die Reihenfolge ist einzuhalten:

1. **Tensegrity Clean Code Reviewer**: Diff der geänderten Repos prüfen; Blocker-Befunde zuerst beheben lassen.
2. **Tensegrity Docs Keeper**: Öffentliche APIs, Coverage-Ausnahmen und Traceability der Änderung synchronisieren.
3. **Tensegrity Quality Gate**: Alle betroffenen Stack-Gates ausführen (Rust, Python, .NET, Uno); Tests und 85-%-Coverage müssen bestehen.

Erst wenn alle drei Schritte GRÜN melden, darf ein Pull Request erstellt oder ein Merge empfohlen werden.
Bei ROT: Befunde je Schritt mit Datei und konkretem Fix auflisten; kein Commit- oder PR-Vorschlag vor durchgehend Grün.
