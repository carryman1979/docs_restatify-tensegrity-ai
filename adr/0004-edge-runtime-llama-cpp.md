# ADR-0004: Edge-Runtime llama.cpp/GGUF mit LoRA

- **Status:** angenommen (Training: abhängig von Spike S2)
- **Datum:** 2026-10-01

## Kontext

Mobilgeräte mit 6–8 GB RAM, sechs Plattformen, Offline-Betrieb, LoRA-Hot-Swap für Experten.

## Entscheidung

Inferenz über **llama.cpp** (GGUF, Q4, Vulkan/Metal/CPU) mit Rust-Bindings. Basismodell Apache/MIT, ≤ ca. 2 B
Parameter (Kandidaten Qwen3-1.7B/0.6B, SmolLM3, Phi-mini; Auswahl in Phase 2 per Eval). On-Device-Training wird in
Spike S2 zwischen candle, llama.cpp finetune und ONNX Runtime Training entschieden; Fallback ist Training auf einem
Desktop im LAN.

## Alternativen

ONNX Runtime GenAI (gute Mobil-Unterstützung, LoRA-Wechsel aufwendiger), MLC-LLM (TVM-Toolchain schwer), tch-rs
(libtorch zu groß für Mobil).

## Konsequenzen

Hive muss Adapter nach GGUF exportieren (Spike S5).
