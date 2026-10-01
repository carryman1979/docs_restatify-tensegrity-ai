---
name: adr
description: "Use when recording a Tensegrity AI architecture decision as an ADR (MADR short form) and updating the ADR index. Keywords: adr, architekturentscheidung, entscheidung dokumentieren, madr."
---
# ADR anlegen

1. Nächste freie Nummer in `adr/README.md` bestimmen (4-stellig).
2. `adr/0000-vorlage.md` kopieren nach `adr/NNNN-kurztitel.md` (Kleinbuchstaben, Bindestriche, Umlaute als ae/oe/ue im Dateinamen).
3. Kontext, Entscheidung, Alternativen (Tabelle), Konsequenzen ausfüllen; betroffene Repos und REQ-IDs nennen.
4. Status `vorgeschlagen`; erst nach Freigabe durch den Maintainer `angenommen`.
5. Index in `adr/README.md` ergänzen.
6. Ersetzt die Entscheidung eine ältere: alte ADR auf `ersetzt durch ADR-NNNN` setzen, nicht löschen.
7. Widerspricht die Entscheidung dem Whitepaper: Änderungstabelle im Whitepaper ergänzen.
