---
name: traceability
description: "Use when updating the Tensegrity AI traceability matrix linking REQ-STK, REQ-SYS, ARC/ADR, CMP and tests on all V-model levels, or when checking requirement coverage. Keywords: traceability, matrix, rückverfolgbarkeit, abdeckung, req-id, v-modell."
---
# Traceability pflegen

## Schritte
1. Anforderungen sammeln: `requirements/lastenheft.md`, `requirements/pflichtenheft.md`, Komponenten-Docs der Repos.
2. Tests finden: Testnamen enthalten die REQ-ID (z. B. `req_sys_012`, `ReqSys012`). Über alle Repos suchen:
   Regex `req[_-]?(stk|sys)[_-]?\d{3}` (case-insensitive).
3. Matrix in `traceability/README.md` aktualisieren: eine Zeile pro REQ-SYS, Spalten Unit/Komponente/Integration/System
   mit Testnamen bzw. `–`.
4. Status: `offen` (keine Tests), `teilweise` (nicht alle Stufen), `erfüllt` (alle Tests vorhanden **und** zuletzt grün).
5. Lücken als Liste am Ende ausgeben.

## Regeln
- „erfüllt“ nur nach tatsächlichem Testlauf.
- Jede REQ-SYS braucht mindestens einen System- oder Integrationstest.
