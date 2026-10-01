# ADR-0008: Identität über OIDC, Keycloak als Standard

- **Status:** angenommen
- **Datum:** 2026-10-01

## Kontext

Cloud-Backend und Hive-Admin brauchen Anmeldung; Social Login (Google, Microsoft, Apple, Facebook) ist gewünscht.
Intranet-Hives sind air-gapped.

## Entscheidung

Abstraktion über **Standard-OIDC**. Standard-Broker **Keycloak** (selbst gehostet, Social Login), **Auth0** per
Konfiguration möglich, Intranet nutzt den eigenen IdP (AD/Entra/LDAP über Keycloak oder direkt).

## Alternativen

Nur Auth0: bequem, aber US-SaaS und im Air-Gap nicht erreichbar.

## Konsequenzen

Keine IdP-spezifischen Bibliotheken im Anwendungscode; Integrationstest mit Keycloak-Container.
