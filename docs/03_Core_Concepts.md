# 03 - Core Concepts

> **STATIS Intelligence Layer (SIL)**  
> **Core Concepts & Ubiquitous Language**

**Version:** 0.1 (Draft)  
**Status:** Foundational Architecture

---

## Purpose

This document defines the common vocabulary used throughout the STATIS Intelligence Layer (SIL).

Every architectural document, implementation, Plugin, Flow and AI Agent should use these terms consistently.

Each concept has exactly one meaning.

A shared language is essential for building a platform that remains understandable and extensible over time.

---

## Table of Contents

1. Ubiquitous Language
2. Execution Model
3. Core Concepts
4. Relationships
5. Design Rules
6. Architecture Decisions
7. Related Documents

---

# Ubiquitous Language

SIL follows the principles of Domain-Driven Design.

Whenever a concept is introduced:

- it should have one clear meaning;
- it should not overlap another concept;
- it should be reusable across all STATIS applications.

---

# Execution Model

Every Request moves through the same conceptual model.

```text
Request
    ↓
Execution Plan
    ↓
Flow
    ↓
Capability
    ↓
Tool
    ↓
Application
```

Each layer has a single responsibility.

---

# Core Concepts

## Request

The central business object of SIL.

A Request represents everything SIL knows about a user's objective.

A Request evolves during processing while preserving:

- identity
- original input
- execution history

---

## Intent

The business objective extracted from a Request.

Examples:

- run_job
- explain_job
- explain_company

Intent is part of a Request.

It is not the Request itself.

---

## Entity

A business object referenced by the user.

Examples:

- Job
- Workspace
- Company
- Dataset
- Run
- Schedule

Entities are resolved during Request processing.

---

## Parameters

Structured values required for execution.

Examples:

- environment
- period
- company
- workspace

Parameters may originate from:

- user input
- defaults
- execution context
- previous processing stages

---

## Execution Context

Information surrounding a Request.

Typical context includes:

- authenticated user
- active workspace
- environment
- user roles
- available plugins

Context influences planning but never changes the user's original Request.

---

## Execution Plan

The deterministic plan produced by the Planning Engine.

It specifies:

- selected Flow
- resolved parameters
- execution strategy

The Execution Plan is consumed by the Flow Engine.

---

## Flow

A declarative orchestration describing how a Request is fulfilled.

Flows:

- orchestrate Capabilities
- contain no business logic
- remain technology independent

---

## Capability

A business operation offered by the platform.

Examples:

- Run Job
- Search Company
- Read Logs

Capabilities describe *what* can be done.

---

## Tool

A technical implementation of a Capability.

Examples include:

- REST client
- MCP implementation
- SDK adapter

Tools describe *how* a Capability is executed.

---

## Plugin

The integration boundary between SIL and an application.

Plugins may register:

- Capabilities
- Tools
- Flows
- Context Providers
- Policy Extensions
- Approval Rules
- Agent Hints

---

## Application

A business system integrated with SIL.

Examples:

- Pipeline
- Sudreg
- Data Catalogue

Applications own business logic.

SIL orchestrates them.

---

## Agent

An AI component responsible for reasoning.

Typical responsibilities:

- explanation
- summarization
- comparison
- recommendations

Agents never execute business operations.

---

## Persona

A predefined behavioural profile for an Agent.

Examples:

- Technical Explainer
- Business Analyst
- QA Reviewer
- Release Manager

---

## Policy

A deterministic rule controlling execution.

Possible outcomes:

- Allow
- Approval Required
- Deny

---

## Approval

Explicit human authorization required before execution continues.

Approval requirements are determined by Policy.

---

## Audit

The permanent record of a Request.

Audit includes:

- planning decisions
- approvals
- executed Flow
- execution outcome

---

# Relationships

The concepts build upon one another.

```text
Request
    │
    ├── Intent
    ├── Entities
    ├── Parameters
    ├── Context
    │
    ▼
Execution Plan
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

---

# Design Rules

The following rules should always hold.

1. Request is the central business object.
2. Intent is part of a Request.
3. Flows orchestrate Capabilities.
4. Capabilities are implemented by Tools.
5. Business logic belongs to Applications.
6. Layers never skip adjacent layers.

---

# Architecture Decisions

**AD-0301** — Every architectural concept has a single responsibility.

**AD-0302** — Request is the central business object.

**AD-0303** — Capability abstracts business functionality from implementation.

**AD-0304** — Plugins isolate applications from SIL Core.

**AD-0305** — A shared vocabulary is mandatory across the platform.

---

# Related Documents

- [00_Principles](00_Principles.md)
- [01_Vision](01_Vision.md)
- [02_Architecture](02_Architecture.md)
- [10_Request_Engine](10_Request_Engine.md)

---

> **A shared language is one of the strongest architectural tools for building a long-lived platform.**
