---
name: signiertes-release
description: "Use when building, signing, verifying or distributing Tensegrity AI model/adapter releases and delta packages (Hive to device, P2P, USB, Hive clone re-signing) following The Update Framework (TUF). Keywords: release signieren, tuf, update, delta-paket, usb import, klon, neu signieren, downgrade, rollback."
---
# Signiertes Release / Update

## Paket
- Inhalt: geänderte Slots (Adapter-Dateien), Slot-Registry-Auszug, Lösch-/Archiv-Einträge, Manifest.
- Inhaltsadressiert (SHA-256) und TUF-Metadaten: `root.json`, `targets.json`, `snapshot.json`, `timestamp.json`.

## Erstellen (Hive)
1. Kandidat hat das Eval-Gate bestanden (Skill `eval-gate`).
2. Delta zur Vorversion berechnen; Versionsnummer monoton erhöhen.
3. Targets signieren (Online-Schlüssel), Snapshot und Timestamp aktualisieren; Root bleibt offline.

## Prüfen (Gerät/Peer/Klon)
1. Root-Kette vom konfigurierten Vertrauensanker prüfen.
2. Timestamp nicht abgelaufen, Snapshot/Targets-Version ≥ bekannt (kein Downgrade, kein Freeze).
3. Hash und Größe jeder Datei prüfen, bevor sie geparst wird.
4. Anwenden atomar; vorherige Version für Rollback behalten.
5. Streng-geheim-Slots: nur über das lokale Prüf-Gate.

## Klon
Upstream vollständig prüfen → Inhalte übernehmen → mit Klon-Root neu signieren. Geräte am Klon vertrauen nur dem Klon-Anker.

## Tests
Falsche Signatur, Downgrade, Replay, abgelaufener Timestamp, manipulierte Datei, Rollback, Datenträger-Import offline.
