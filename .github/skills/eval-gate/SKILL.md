---
name: eval-gate
description: "Use when defining, running or extending the Tensegrity AI evaluation gate that every candidate release (weaved adapters, new base model, router change) must pass: domain knowledge, safety, regressions, memorization canaries, mobile performance. Keywords: eval, evaluation, eval-gate, benchmark, regression, kanarie, memorierung, qualität."
---
# Eval-Gate

## Bestandteile (versioniert)
| Suite | Zweck | Kriterium (Startwert, Richtlinie) |
|---|---|---|
| Fachwissen je Experte | Qualität | keine Verschlechterung > 1 % gegenüber Vorversion |
| Allgemein | Generalist | keine Verschlechterung > 1 % |
| Sicherheit | schädliche Ausgaben | keine neuen Verstöße |
| Kanarien | Memorierung eingeschleuster Geheimnisse | kein Kanarie-String reproduzierbar |
| Performance | Tokens/s, RAM auf Referenzgerät | innerhalb Budget |

## Ablauf
1. Kandidat und Vorversion auf identischer Suite und Hardware messen.
2. Ergebnisse als JSON neben dem Kandidaten ablegen.
3. Bestanden → Release (Skill `signiertes-release`). Nicht bestanden → verwerfen, Beitragende mit negativer Bewertung in R(a).
4. Neue Fehlerklassen aus Feedback als Testfälle in die Suite aufnehmen.

## Regeln
Suiten enthalten keine personenbezogenen Daten; nur Daten mit kompatibler Lizenz.
