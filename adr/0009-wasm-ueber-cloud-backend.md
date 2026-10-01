# ADR-0009: WebAssembly über Cloud-Backend, Stufen gesperrt

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Die Uno-App soll auch als WebAssembly laufen. Ein SLM im Browser ist für Mobilgeräte zu schwer; Inferenz läuft daher in
der Cloud.

## Entscheidung

Die WASM-Variante spricht per gRPC-Web mit `api_restatify-tensegrity-ai-cloud` (Generalist zuerst, Router zu
Experten, vLLM Multi-LoRA). „Streng geheim“ und „Nur lokal“ sind dort gesperrt und in der UI erklärt.

## Konsequenzen

Die App kapselt die Inferenz hinter einer Schnittstelle mit zwei Implementierungen (Core-FFI, Cloud). UI-Tests mit
Playwright prüfen die Sperre.
