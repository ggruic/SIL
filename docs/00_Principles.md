# 00 - Principles

> **STATIS Intelligence Layer (SIL)**  
> **Architectural Principles & Constitution**

**Version:** 0.1 (Draft)  
**Status:** Foundational Architecture

---

## Purpose

This document defines the non-negotiable architectural principles of the STATIS Intelligence Layer (SIL).

Unlike implementation documents, these principles are intended to remain stable throughout the lifetime of the platform. Every architectural decision, plugin, Flow, Tool and future extension should be evaluated against these principles.

When implementation details evolve, these principles should continue to hold.

---

## Table of Contents

1. Purpose
2. Core Principles
3. Architectural Checklist
4. Architecture Decisions
5. Future Evolution
6. Related Documents

---

# Core Principles

## Principle 1 — Everything Begins with a Request

The **Request** is the central business object of SIL.

Every execution starts with a Request.
Every decision belongs to a Request.
Every audit record references a Request.

Everything else exists to enrich, validate or execute a Request.

---

## Principle 2 — AI Understands. SIL Controls. Applications Execute.

Responsibilities are intentionally separated.

### AI

- Understands
- Reasons
- Explains
- Summarizes

### SIL

- Plans
- Orchestrates
- Applies policies
- Requests approvals
- Audits execution

### Applications

- Own business logic
- Own data
- Own persistence
- Enforce domain rules

These responsibilities must never overlap.

---

## Principle 3 — Applications Remain Autonomous

Applications never orchestrate one another.

Cross-application workflows are always orchestrated by SIL.

---

## Principle 4 — Business Before Technology

SIL speaks the language of business.

Prefer:

- Run Job
- Search Company
- Read Dataset Metadata

Avoid:

- REST endpoint
- SQL query
- MCP method
- SDK function

Technology changes.

Business concepts remain stable.

---

## Principle 5 — Capabilities Describe. Tools Execute.

A Capability represents **what** an application can do.

A Tool defines **how** that Capability is executed.

Flows depend on Capabilities.

Flows never depend on Tool implementations.

---

## Principle 6 — Flows Orchestrate. They Do Not Contain Business Logic.

Flows describe orchestration only.

Business rules always remain inside applications.

---

## Principle 7 — Security Is an Architectural Concern

Security is enforced by SIL.

Never by AI.

Never by prompts.

Every Request passes through Policy evaluation.

---

## Principle 8 — Human Authority Is Preserved

AI may recommend.

AI may explain.

AI must never silently perform high-impact operations.

Critical operations require explicit approval.

---

## Principle 9 — Every Decision Must Be Explainable

SIL should always be able to answer:

- Why this Flow?
- Why this Capability?
- Why approval?
- Why denial?

Opaque behaviour is unacceptable.

---

## Principle 10 — Everything Important Is Auditable

Every Request produces a complete execution history.

Audit is a core platform responsibility.

---

## Principle 11 — Prefer Extension Over Modification

New functionality should be introduced through:

- Plugins
- Capabilities
- Tools
- Flows
- Policies

The SIL Core should remain stable.

---

## Principle 12 — Plugins Are First-Class Citizens

Every application integrates through a Plugin.

Plugins may contribute:

- Capabilities
- Tools
- Flows
- Context Providers
- Policy Extensions
- Approval Rules
- Agent Hints

---

## Principle 13 — Deterministic Execution Is Mandatory

Understanding language is probabilistic.

Execution is deterministic.

The same Request under the same conditions should produce the same execution behaviour.

---

## Principle 14 — Context Is Explicit

Every execution is based on an explicit Execution Context.

Hidden assumptions should be avoided.

---

## Principle 15 — The Request Evolves Through Events

A Request is enriched throughout its lifecycle.

Typical events:

- Request Created
- Request Normalized
- Context Enriched
- Plan Generated
- Policy Evaluated
- Approval Granted
- Flow Executed
- Response Generated
- Request Completed

The Request identity remains stable while its knowledge grows.

---

## Principle 16 — Registries Are the Source of Truth

Discovery is metadata-driven.

SIL uses dedicated registries for:

- Plugins
- Flows
- Capabilities
- Tools
- Agents

---

## Principle 17 — Design for the Unknown

The architecture assumes that:

- AI models will change
- Protocols will change
- Applications will change
- User interfaces will change

The architecture should evolve without redesign.

---

## Principle 18 — SIL Is a Platform

SIL is not:

- a chatbot
- an MCP server
- a Pipeline extension

SIL is a platform for secure enterprise orchestration.

---

## Principle 19 — Layers Never Skip Layers

Each architectural layer communicates only with its adjacent layer.

```
Request
    ↓
Flow
    ↓
Capability
    ↓
Tool
    ↓
Application
```

Forbidden examples:

- Flow → REST API
- Flow → SQL
- Planning → Application

This preserves architectural integrity.

---

# Architectural Checklist

Before introducing a new component ask:

1. Does it respect Request-first architecture?
2. Does it separate understanding from execution?
3. Does it expose business concepts?
4. Can it be reused?
5. Does it preserve application autonomy?
6. Is it deterministic?
7. Is it secure by design?
8. Is it auditable?
9. Can it be extended via Plugins?
10. Would it survive changing the AI model?

---

# Architecture Decisions

**AD-0001** — Request is the central business object.

**AD-0002** — AI understands; SIL orchestrates; Applications execute.

**AD-0003** — Flows orchestrate Capabilities, never implementations.

**AD-0004** — Plugins extend SIL without modifying SIL Core.

**AD-0005** — Layers never bypass adjacent layers.

---

# Future Evolution

These principles are expected to remain stable.

Future documents may extend the implementation but should not contradict this constitution.

---

# Related Documents

- `README.md`
- `01_Vision.md`
- `02_Architecture.md`
- `03_Core_Concepts.md`

---

> **Natural language should make enterprise systems easier to use — never harder to govern.**
