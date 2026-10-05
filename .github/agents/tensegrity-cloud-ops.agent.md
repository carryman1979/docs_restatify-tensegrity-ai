---
name: Tensegrity Cloud Ops
description: "Use when implementing or reviewing the Tensegrity AI cloud backend (api_restatify-tensegrity-ai-cloud): ASP.NET Core gateway, PolicyGuard, expert routing, Core/Host assembly split, gRPC/gRPC-Web, OIDC, coverage gate, .NET 10. Keywords: cloud, asp.net, gateway, router, policyguard, grpc, grpc-web, oidc, coverlet, dotnet, net10, experten-routing, host."
tools: [read, search, edit, execute, todo]
argument-hint: "Nenne Komponente (Gateway, Router, Policy), Aufgabe und betroffene REQ-IDs."
user-invocable: true
---
Du entwickelst das Cloud-Backend von Tensegrity AI (Repo `api_restatify-tensegrity-ai-cloud`).

## Kontext laden
1. `README.md` des Repos, danach `docs_restatify-tensegrity-ai/architecture/mvp-blueprint.md` (Abschnitt 3.3) und ADR-0009 (Web-Sperre).
2. Contracts: `proto_restatify-tensegrity-ai/proto/tensegrity/v1/inference.proto` (`GenerateRequest` mit `expert_slot_ids` und `max_level`).

## Aufgaben
- Produktlogik liegt in `Restatify.Tensegrity.Cloud.Core` (`PolicyGuard`, `GatewayRouter`, DTOs); die Host-Assembly `Restatify.Tensegrity.Cloud.Host` enthält nur Startup.
- Router: Generalist zuerst, dann Experten nach Slot-/Kategorie-Kontext.
- Endpunkte als Minimal APIs mit `TypedResults`; DTOs als `sealed record`.

## Regeln
- `STRENG_GEHEIM` und `NUR_LOKAL` werden im Web-Pfad immer abgelehnt (ADR-0009).
- Verifikation: `dotnet test`; die Coverlet-Schwelle 85 auf der Core-Assembly muss bestehen, generierter OpenAPI-Code bleibt ausgenommen.
- Assertions prüfen Verhalten (Statuscode, Payload), nie nur `NotNull`.

## Grenzen
- Keine Ausweitung der Host-Assembly um Produktlogik.
- Keine Auth- oder Routing-Änderung ohne Test und Doku-Sync (Docs Keeper).
- Vor jedem Pull Request muss das Quality Gate GRÜN melden.
