# Traceability

Die Matrix verbindet jede Anforderung mit Architektur, Komponente und Tests aller Teststufen.

| REQ-STK | REQ-SYS | ARC / ADR | CMP | Unit | Komponente | Integration | System | Status |
|---|---|---|---|---|---|---|---|---|---
| REQ-STK-002 | REQ-SYS-001 | ARC-003, ADR-0003 | Core `tg-policy`, Proto `common.proto` | `req_sys_001_confidentiality_level_enum` | `req_sys_001_confidentiality_level_enum` | `proto_contract_common_level_enforced` | `req_sys_001_confidentiality_level_enum` | erfüllt |
| REQ-STK-002 | REQ-SYS-002 | ARC-003, ADR-0003 | Core `tg-policy` | `req_sys_002_taint_rule_max_level` | `req_sys_002_taint_rule_max_level` | `proto_contract_common_level_mapping` | `req_sys_002_taint_rule_max_level` | erfüllt |
| REQ-STK-001 | REQ-SYS-003 | ARC-001, ADR-0004 | Core `tg-learn` | `req_sys_003_masked_training_keeps_base_frozen` | `req_sys_003_masked_training_keeps_base_frozen` | `integration_train_strict_local_delta` | `req_sys_003_masked_training_keeps_base_frozen` | teilweise |
| REQ-STK-003 | REQ-SYS-004 | ARC-002, ADR-0007 | Core `tg-fold`, Proto `federation.proto` | `req_sys_004_fold_clips_and_dp_noise` | `req_sys_004_fold_clips_and_dp_noise` | `proto_contract_federation_folded_payload` | `req_sys_004_fold_clips_and_dp_noise` | teilweise |
| REQ-STK-001 | REQ-SYS-005 | ARC-002, ADR-0003 | Core `tg-sync` | `req_sys_005_nur_lokal_sends_zero_bytes` | `req_sys_005_nur_lokal_sends_zero_bytes` | `integration_nur_lokal_zero_bytes` | `req_sys_005_nur_lokal_sends_zero_bytes` | teilweise |
| REQ-STK-004 | REQ-SYS-006 | ARC-004, ADR-0006 | Hive `trust` + `weave` | `req_sys_006_trust_zero_rejected` | `req_sys_006_trust_zero_rejected` | `integration_hive_rejects_zero_trust` | `req_sys_006_trust_zero_rejected` | teilweise |
| REQ-STK-005 | REQ-SYS-007 | ARC-005, ADR-0010 | Hive `slot-registry` | `req_sys_007_slot_registry_registration` | `req_sys_007_slot_registry_registration` | `integration_slot_registry_router_selection` | `req_sys_007_slot_registry_registration` | teilweise |
| REQ-STK-006 | REQ-SYS-008 | ARC-006, ADR-0001 | Proto `feedback.proto` | `req_sys_008_feedback_requires_evidence` | `req_sys_008_feedback_requires_evidence` | `proto_contract_feedback_submission` | `req_sys_008_feedback_requires_evidence` | teilweise |
| REQ-STK-007 | REQ-SYS-009 | ARC-007, ADR-0007 | Release/Proto `release.proto` | `req_sys_009_release_signature_and_hash_validation` | `req_sys_009_release_signature_and_hash_validation` | `proto_contract_release_bundle_validation` | `req_sys_009_release_signature_and_hash_validation` | teilweise |
| REQ-STK-008 | REQ-SYS-010 | ARC-008, ADR-0008 | Proto `exchange.proto` | `req_sys_010_exchange_requires_valid_signature` | `req_sys_010_exchange_requires_valid_signature` | `integration_exchange_import_export` | `req_sys_010_exchange_requires_valid_signature` | teilweise |
| REQ-STK-009 | REQ-SYS-011 | ARC-009, ADR-0009 | App + Cloud policy | `req_sys_011_wasm_blocks_strict_levels` | `req_sys_011_wasm_blocks_strict_levels` | `integration_wasm_policy_restriction` | `req_sys_011_wasm_blocks_strict_levels` | teilweise |
| REQ-STK-010 | REQ-SYS-012 | ARC-010, ADR-0010 | Hive `audit`, Proto `admin.proto` | `req_sys_012_audit_log_for_blocked_submission` | `req_sys_012_audit_log_for_blocked_submission` | `integration_admin_blocked_submission_is_logged` | `req_sys_012_audit_log_for_blocked_submission` | teilweise |

## Qualitäts-Gates und Pre-PR-Check

Vor jedem Pull Request gilt der Prompt [.github/prompts/pre-pr-check.prompt.md](../.github/prompts/pre-pr-check.prompt.md):

- **Clean Code Reviewer** prüft Duplikate, Naming und Idiome.
- **Docs Keeper** hält API-Doku, Traceability und Coverage-Begründungen synchron.
- **Quality Gate** führt die Stack-Tests aus und erzwingt mindestens 85 % Coverage in den Produktassemblies.

Diese Matrix bleibt Pflichtenheft- und Blueprint-konform; jede neue Implementierung muss die zugehörige REQ-STK/REQ-SYS-Zeile aktualisieren, sobald die zugehörigen Tests grün sind.

## Cloud-Routing und Slot-Registry

- `SlotRegistry` in `Restatify.Tensegrity.Cloud.Core` definiert aktive Experten-Slots (z. B. `legal`, `finance`, `engineering`).
- `GatewayRouter` löst angeforderte Slot-IDs deterministisch auf und lehnt unbekannte oder inaktive Slots ab.
- Die Route bleibt datenschutzkonform: `Offen` → Generalist, `Vertraulich` → Redaktion, beides mit optionalen Experten.
- Die Tests belegen die Ablehnung unbekannter Slots und die stabile Reihenfolge der ausgewählten Experten.
- Integrationstests prüfen die Kombination aus Policy, Slot-Registry und Routing gegen den HTTP-Endpunkt.

## Contract-Alignment und Proto-Drift

- Die Protobuf-Contracts sind die Single Source of Truth für alle Stacks.
- Der Test `test_contract_alignment.py` prüft, dass die Proto-Feldnamen mit den tatsächlichen JSON-Keys der Konsumenten übereinstimmen.
- Änderungen an Contracts erfordern sofortige Aktualisierung der Konsumenten und der Traceability.

Pflege mit dem Skill `traceability`. Eine Anforderung gilt erst als erfüllt, wenn alle zugeordneten Tests grün sind.
