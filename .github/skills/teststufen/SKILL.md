---
name: teststufen
description: "Use when deciding which V-model test level (unit, component, integration, system, acceptance) and which tool fits a Tensegrity AI change, and how to name the tests. Keywords: teststufe, unittest, komponententest, integrationstest, systemtest, welches testframework, v-modell."
---
# Teststufe wählen

| Frage | Stufe | Werkzeug |
|---|---|---|
| Reine Funktion/Logik ohne I/O? | Unit | nextest+proptest / pytest+hypothesis / MSTest |
| Eine Komponente (Crate, Python-Paket, Service) mit Fakes für Nachbarn? | Komponente | dieselben + Fakes |
| Zwei echte Komponenten über Contract, DB, Container? | Integration | Testcontainers, buf, echte gRPC |
| Nutzerfall Ende-zu-Ende über Repos? | System | Playwright (WASM), Appium/Uno.UITest, Netzwerkmitschnitt |
| Stakeholder-Szenario aus dem Lastenheft? | Abnahme | Szenario-Skript + Protokoll |

## Regeln
- Testname enthält die REQ-/CMP-ID.
- Datenschutz-Invarianten (Taint, Maske, Falten) zusätzlich property-basiert testen.
- Mobil-Performance: Messung auf Referenzgerät, Ergebnis mit Gerät/OS/Build protokollieren.
- Snapshot-Tests (`insta`) nur für stabile Formate (Pakete, Protokolle).
