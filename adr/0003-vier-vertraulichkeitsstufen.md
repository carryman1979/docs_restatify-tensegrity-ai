# ADR-0003: Vier Vertraulichkeitsstufen mit Berechtigungsmaske

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

V1.0.25 kannte nur ein Bitflag (Streng vertraulich ja/nein); „Offen“ und „Vertraulich“ teilten sich B = 0, eine Stufe
ohne jede Übertragung fehlte.

## Entscheidung

Stufen **Offen < Vertraulich < Streng geheim < Nur lokal**, abgebildet auf eine 6-Bit-Berechtigungsmaske (Whitepaper
2.1). Strengste Stufe gewinnt (Taint). Für Offen/Vertraulich gilt Hive-Vertrauen > Gerät (direkte Updates); nur
gezielte Updates von Streng-geheim-Slots laufen über ein lokales Prüf-Gate mit Rollback. Pseudonymisierung, Bündelung
und k-Mindestanzahl gelten nur für Streng-geheim-Strukturmeldungen; Offen lernt sofort.

## Konsequenzen

`tg-policy` ist zentral und wird property-basiert getestet. Systemtest: „Nur lokal“ = 0 Byte. Bit 5 bleibt für
BL-001 reserviert.
