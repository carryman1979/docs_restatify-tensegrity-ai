# ADR-0007: Signierte Updates nach TUF

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Updates fließen Hive → Gerät, Gerät → Gerät (P2P) und per Datenträger. Jeder Peer ist potenziell bösartig.

## Entscheidung

Releases und Delta-Pakete folgen dem Rollenmodell von **The Update Framework** (Root, Targets, Snapshot, Timestamp),
inhaltsadressiert per Hash. Geräte prüfen Signatur, Version (kein Downgrade), Ablauf und Hash vor jeder Nutzung.
Root-Schlüssel offline. Klone signieren mit eigenem Root neu.

## Konsequenzen

Gemeinsames Paketformat für Online, P2P und Datenträger. Tests: Downgrade, Replay, Freeze, falsche Signatur.
