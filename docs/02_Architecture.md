# 02 - Architecture

> **STATIS Intelligence Layer (SIL)**  
> **Architecture**

**Version:** 0.1 (Draft)  
**Status:** Foundational Architecture

---

# Purpose

This document describes the high-level architecture of the STATIS Intelligence Layer (SIL).

It explains how the major architectural components interact, how responsibilities are separated, and how Requests move through the platform.

Implementation details are intentionally omitted. They are described in the corresponding component documents.

---

# Table of Contents

1. Architectural Goals
2. High-Level Architecture
3. Core Processing Pipeline
4. Layered Business Execution Model
5. Platform Components
6. Plugin Architecture
7. Architectural Boundaries
8. Architecture Decisions
9. Related Documents

---

# Architectural Goals

SIL is designed to provide:

- Natural language interaction
- Deterministic execution
- Enterprise governance
- Security by design
- Complete auditability
- Extensibility through Plugins
- Independence from individual applications and AI models

---

# High-Level Architecture

```
                 User
                  │
                  ▼
          Request Engine
                  │
                  ▼
          Context Engine
                  │
                  ▼
         Planning Engine
                  │
                  ▼
          Policy Engine
                  │
                  ▼
         Approval Engine
                  │
                  ▼
           Flow Engine
                  │
                  ▼
           Audit Engine
                  │
                  ▼
              Response
```

Each engine has a single responsibility.

No engine bypasses another.

---

# Core Processing Pipeline

A Request travels through the platform in a predictable lifecycle.

```
Request
    ↓
Understand
    ↓
Enrich
    ↓
Plan
    ↓
Authorize
    ↓
Execute
    ↓
Audit
    ↓
Respond
```

Each stage enriches the same Request object.

The Request remains the single source of truth throughout execution.

---

# Layered Business Execution Model

SIL separates business intent from technical implementation.

```
User
    │
    ▼
Request
    │
    ▼
Flow
    │
    ▼
Capability
    │
    ▼
Tool
    │
    ▼
Application
```

Each layer answers a different question.

| Layer | Responsibility |
|--------|----------------|
| Request | What does the user want? |
| Flow | How should the Request be orchestrated? |
| Capability | Which business operation is required? |
| Tool | How is that Capability technically executed? |
| Application | Where does the business logic live? |

No layer may bypass another.

---

# Platform Components

## Request Engine

Transforms user input into a structured Request.

## Context Engine

Enriches the Request with execution context.

## Planning Engine

Produces a deterministic Execution Plan.

## Policy Engine

Evaluates governance and authorization rules.

## Approval Engine

Obtains human authorization when required.

## Flow Engine

Executes the selected Flow by orchestrating Capabilities.

## Audit Engine

Records the complete execution history.

Each component is documented separately.

---

# Plugin Architecture

Applications integrate with SIL through Plugins.

A Plugin may contribute:

- Capabilities
- Tools
- Flows
- Context Providers
- Policy Extensions
- Approval Rules
- Agent Hints

Example:

```
SIL Core
│
├── Pipeline Plugin
├── Sudreg Plugin
├── Data Catalogue Plugin
└── Future Plugin
```

SIL Core remains independent of application-specific logic.

---

# Architectural Boundaries

Responsibilities are intentionally separated.

| Component | Owns |
|----------|------|
| AI | Understanding and reasoning |
| SIL | Planning, orchestration, governance |
| Applications | Business logic and data |

This separation allows each layer to evolve independently.

---

# Request-Centric Design

The Request is the central business object.

All engines enrich the same Request rather than creating independent execution objects.

The Request preserves:

- identity
- original input
- context
- execution history
- audit information
- final response

This model provides explainability and reproducibility.

---

# Design Principles

The architecture follows these key principles:

- Request-first architecture
- Single responsibility
- Layered execution
- Deterministic planning
- Declarative orchestration
- Plugin-based extensibility
- Application autonomy
- Complete auditability

---

# Architecture Decisions

**AD-0201** — SIL is organized as a processing pipeline.

**AD-0202** — Every Request follows the same lifecycle.

**AD-0203** — Business logic remains inside applications.

**AD-0204** — Plugins are the only application integration mechanism.

**AD-0205** — Layers never skip adjacent layers.

---

# Related Documents

- `README.md`
- `00_Principles.md`
- `01_Vision.md`
- `03_Core_Concepts.md`
- `10_Request_Engine.md`
- `11_Context_Engine.md`
- `12_Planning_Engine.md`
- `13_Policy_Engine.md`
- `14_Approval_Engine.md`
- `15_Flow_Engine.md`

---

> **The architecture is designed to ensure that intelligence remains flexible while execution remains deterministic.**
