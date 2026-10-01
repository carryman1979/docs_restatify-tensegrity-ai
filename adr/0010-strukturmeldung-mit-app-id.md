# ADR-0010: Strukturmeldung mit App-ID

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Streng-geheim-Strukturmeldungen könnten anonym gesendet werden; dann fehlen Blacklist und Trust-Historie zum Schutz vor
Vergiftung der Slot-Registry.

## Entscheidung

Strukturmeldungen enthalten die **App-ID**, nur grobe Kategorien aus der Hive-Liste, werden gebündelt und verzögert
gesendet; Slots entstehen erst ab k_min verschiedenen Meldern. Das Restrisiko (Themen-/Aktivitätsprofil) wird in den
Nutzungsbedingungen und in der UI benannt. Alternativen für Betroffene: eigener Hive-Klon oder „Nur lokal“.

## Konsequenzen

BL-004 (anonyme Berechtigungsnachweise) bleibt optionale Härtung. AGB-Baustein und UI-Hinweis sind Pflicht vor Release.
