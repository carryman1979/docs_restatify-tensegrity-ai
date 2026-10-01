---
name: vertraulichkeits-maske
description: "Use when implementing or reviewing code that depends on Tensegrity AI confidentiality levels (Offen, Vertraulich, Streng geheim, Nur lokal), the 6-bit permission mask, taint propagation, gradient masking or folding rules. Keywords: vertraulichkeit, stufe, bitmaske, berechtigungsmaske, taint, streng geheim, nur lokal, falten, maskierte gradienten."
---
# Vertraulichkeits-Maske

## Referenz (Whitepaper 2.1)
| Stufe | Bits 0..5 (Gewichte, DP, Struktur, gezielte Updates, Cloud, Vertrauensraum) |
|---|---|
| Offen | 1 0 1 1 1 0 |
| Vertraulich | 1 1 1 1 1 0 |
| Streng geheim | 0 – 1 1 0 0 |
| Nur lokal | 0 – 0 0 0 0 |

## Implementierungsregeln
- Eine einzige Quelle der Wahrheit (`tg-policy` im Core; Spiegel nur generiert, nicht abgeschrieben).
- Stufe ist ein geordneter Enum; Kombination = `max`. Herabstufung nur per expliziter, protokollierter Nutzeraktion.
- Jede Funktion, die Daten nach außen gibt (Netz, P2P, Datei-Export, Cloud, Logs, Telemetrie), fragt die Maske ab – kein Default „erlaubt“.
- Training: pro Beispiel maskieren; Streng geheim/Nur lokal → nur W_PI.
- WASM-Build: Streng geheim und Nur lokal sind nicht wählbar.

## Pflicht-Tests
- Property: Taint ist kommutativ, assoziativ, idempotent, monoton.
- Property: für jede Stufe s und jede Ausgabefunktion f gilt `f sendet ⇒ Maske(s) erlaubt`.
- System: Nur lokal = 0 Byte, Streng geheim ohne Gewichts-Nutzlast.
