---
name: lizenz-check
description: "Use when adding dependencies, base models, datasets or copied code to any Tensegrity AI repo, or before a release, to verify Apache-2.0 compatibility and update NOTICE. Keywords: lizenz, license, abhängigkeit, dependency, modell-lizenz, dataset, notice, cargo deny, pip-licenses."
---
# Lizenz-Check

## Erlaubt
Apache-2.0, MIT, BSD-2/3, ISC, Zlib, Unicode, CC0, MPL-2.0 (nur unverändert als Abhängigkeit).

## Nicht erlaubt ohne ADR
GPL/AGPL/LGPL (statisch gelinkt), SSPL, BUSL, „non-commercial“-Lizenzen, Modell-Lizenzen mit Nutzungsbeschränkungen
(z. B. Llama Community License), Daten aus Diensten, deren AGB das Training anderer Modelle verbieten.

## Werkzeuge
- Rust: `cargo deny check licenses`
- Python: `pip-licenses --fail-on "GPL;AGPL"`
- .NET: `dotnet list package --include-transitive` + Lizenzprüfung
- Modelle/Daten: Model Card bzw. Dataset Card lesen und in `THIRD_PARTY_NOTICES.md` eintragen.

## Abschluss
NOTICE/THIRD_PARTY_NOTICES aktualisieren; Fund mit Lizenz, Quelle und Version dokumentieren.
