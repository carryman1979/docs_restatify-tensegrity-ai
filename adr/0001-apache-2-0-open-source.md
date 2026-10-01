# ADR-0001: Apache 2.0 für alle Repositories

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Die Restatify UG stellt die Finanzierung ein und gibt das Konzept als Open Source frei. Das Projekt wird von einer
Person getragen und soll Beitragende und Betreiber gewinnen. Whitepaper V1.0.25 sprach von einem proprietären Protokoll.

## Entscheidung

Alle sechs Repositories stehen unter **Apache License 2.0** mit `NOTICE`. Beiträge über DCO (`git commit -s`).

## Alternativen

| Option | Vorteile | Nachteile |
|---|---|---|
| Apache 2.0 | Patentlizenz, unternehmensfreundlich | Forks können geschlossen werden |
| AGPL-3.0 | Copyleft auch für SaaS | schreckt Unternehmen/Hive-Betreiber ab |
| Open Core | Einnahmen möglich | kein Budget für Pflege zweier Linien |

## Konsequenzen

Nur Apache-kompatible Abhängigkeiten und Modelle (Skill `lizenz-check`). Firmen- und Bankdaten aus den
Ursprungsdokumenten werden nicht veröffentlicht.
