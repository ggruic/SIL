# Sudreg Plugin

> Example SIL Plugin package for integrating Sudreg with the STATIS Intelligence Layer.

**Plugin ID:** `sudreg`  
**Version:** `0.1.0`  
**Status:** Draft MVP Example

## Purpose

This plugin demonstrates how a non-Pipeline STATIS application integrates with SIL through the same
Plugin Model used by the Pipeline Plugin.

The MVP scenario is:

1. Explain Company

The plugin is intentionally declarative. It does not execute Requests directly. It contributes
Capabilities, Tools, a Flow, Context Providers, Policies, Personas and Agents that SIL Core can
validate, register and orchestrate.

## MVP Flow

| Flow | Purpose |
|---|---|
| `sudreg.company.explain` | Searches for a company, reads registry details and ownership information, then explains the company in business language. |

## Package Structure

```text
sudreg-plugin/
├── plugin.yaml
├── capabilities/
├── tools/
├── flows/
├── context/
├── policies/
├── approvals/
├── personas/
├── agents/
├── docs/
├── examples/
└── schemas/
```

## Architectural Boundary

The Sudreg Plugin owns the `sudreg.*` namespace.

SIL Core remains independent from Sudreg implementation details.

```text
Request
  ↓
Flow
  ↓
Capability
  ↓
Tool
  ↓
Sudreg Application
```

## Notes

This package is an architectural example. Tool invocation details such as REST paths are illustrative
and should be adapted to the actual Sudreg API.
