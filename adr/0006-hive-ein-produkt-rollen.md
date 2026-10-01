# ADR-0006: Ein Hive-Produkt mit Rollen per Konfiguration

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Benötigt werden Internet-Hive, Unternehmens-/Intranet-Hives und Länder-Hives (Whitepaper 9.2.6).

## Entscheidung

Ein Produkt, Rolle per Konfiguration: **Root/Upstream** oder **Klon**. Keine fest verdrahteten Endpunkte;
konfigurierbare Vertrauensanker (Upstream-Signatur prüfen, lokal neu signieren); Import/Export-Modul (online oder
Datenträger) mit Richtlinie; lokale Daten mit hohem Betreiberfaktor; Kaskaden möglich. Intranet-Hives strikt getrennt,
Export optional, nie Streng geheim.

## Konsequenzen

Rollen- und Richtlinien-Tests in der Hive-Integrationsstufe; Systemtest „Klon-Import per Datenträger“.
