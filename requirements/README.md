# Anforderungen (linke Seite des V-Modells)

| Ebene | Dokument | ID-Schema | Prüfung durch |
|---|---|---|---|
| Stakeholder (Lastenheft) | `lastenheft.md` (Phase 1) | `REQ-STK-nnn` | Systemtest / Abnahme |
| System (Pflichtenheft) | `pflichtenheft.md` (Phase 1) | `REQ-SYS-nnn` | Systemtest |
| Architektur | `../architecture/` + ADRs | `ARC-nnn` | Integrationstest |
| Komponente | je Repo `docs/` | `CMP-<repo>-nnn` | Komponententest |
| Einheit | Code + Doku-Kommentare | – | Unittest |

## Regeln

- Jede Anforderung: ID, Titel, Beschreibung, Begründung/Quelle (Whitepaper-Abschnitt, ADR, Backlog), Priorität
  (MUSS/SOLL/KANN), Abnahmekriterium, Teststufe.
- IDs werden nie wiederverwendet; gestrichene Anforderungen erhalten Status `entfallen`.
- Jede `REQ-SYS` verweist auf mindestens eine `REQ-STK`.

## Vorlage

```markdown
### REQ-SYS-001 – Kurztitel
- **Quelle:** REQ-STK-…, Whitepaper 2.1
- **Priorität:** MUSS
- **Beschreibung:** …
- **Abnahmekriterium:** messbar, z. B. „0 Byte Netzwerkverkehr bei Stufe Nur lokal“
- **Teststufe:** System
```
