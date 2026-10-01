# ADR-0005: Uno ↔ Core per FFI im selben Prozess

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Version 1.0 der Ökosystem-Beschreibung sah gRPC über TCP/Unix-Socket zu einem separaten Rust-Prozess vor. iOS erlaubt
keine Hintergrund-Daemons; Android beschränkt sie stark.

## Entscheidung

Der Core wird als native Bibliothek in die App geladen; Bindings über **uniffi-bindgen-cs** oder **csbindgen**
(Entscheidung in Spike S3). Streaming über Callbacks.

## Konsequenzen

Eine Schnittstellendefinition (`tg-ffi`) mit generierten C#-Bindings; Absturz im Core betrifft die App → Panics an der
FFI-Grenze abfangen.
