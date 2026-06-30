# STATIS Intelligence Layer (SIL)

> **A deterministic AI orchestration platform for enterprise applications.**

**Version:** 0.1 (Draft)
**Status:** Architecture Specification

---

## Overview

The **STATIS Intelligence Layer (SIL)** is an enterprise platform that enables users to interact with the **STATIS ecosystem** using natural language while preserving deterministic execution, governance, security and complete auditability.

SIL is **not** an AI assistant.

SIL is **not** an application.

SIL is an orchestration platform that sits between Large Language Models (LLMs) and enterprise applications, transforming conversational requests into controlled, explainable and secure business operations.

Instead of allowing AI models to invoke application APIs directly, SIL introduces a structured execution pipeline that separates language understanding from business execution.

This allows organizations to benefit from conversational AI without compromising architectural integrity or operational governance.

---

# Why SIL Exists

Enterprise applications traditionally expose functionality through:

* graphical user interfaces
* REST APIs
* command-line tools
* scheduled jobs
* SDKs
* database interfaces

These interfaces are effective for software, but not for people.

Large Language Models fundamentally change how users expect to interact with software.

Instead of learning application-specific commands, users simply describe what they want to achieve.

For example:

> *Run yesterday's FA validation and explain any failed rules.*

or

> *Show me the ownership structure of Ericsson Nikola Tesla.*

or

> *Compare the last two successful production runs.*

The user should not need to know:

* which application owns the information,
* which API performs the operation,
* which parameters are required,
* which sequence of business operations must be executed.

SIL is responsible for translating business intent into deterministic execution while preserving enterprise governance.

---

# Design Philosophy

SIL is built around one fundamental architectural principle:

> **AI provides intelligence. SIL provides control. Applications provide business logic.**

These responsibilities are intentionally separated.

| Layer        | Responsibility                                           |
| ------------ | -------------------------------------------------------- |
| AI           | Understands language, reasons, explains and communicates |
| SIL          | Plans, orchestrates, governs, authorizes and audits      |
| Applications | Execute business logic and manage domain data            |

This separation ensures that AI models can evolve independently from enterprise systems while applications remain deterministic, secure and maintainable.

---

# Architectural Principles

The architecture follows several non-negotiable principles.

* **Request-first architecture**
* **Deterministic execution**
* **Business-oriented abstractions**
* **Plugin-based extensibility**
* **Complete auditability**
* **Security by design**
* **Application autonomy**
* **Explainability**
* **Human approval for sensitive operations**
* **Technology independence**

These principles are described in detail in **00_Principles.md**.

---

# High-Level Architecture

The SIL runtime consists of a sequence of specialized engines.

```text
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
         Target Application
                  │
                  ▼
          Audit & Response
```

Each engine owns exactly one architectural responsibility.

No engine bypasses another.

---

# Core Architectural Concepts

SIL is built around a shared ubiquitous language.

Some of the most important concepts are:

* Request
* Intent
* Entity
* Parameters
* Execution Context
* Execution Plan
* Flow
* Capability
* Tool
* Plugin
* Policy
* Approval
* Audit

Together these concepts define a stable architectural model that remains independent of individual AI models, protocols and enterprise applications.

---

# Platform Architecture

SIL is organized into several architectural layers.

## Foundation

Defines the architectural philosophy of the platform.

* Principles
* Vision
* Architecture
* Core Concepts

---

## Runtime

Implements the deterministic request processing pipeline.

* Request Engine
* Context Engine
* Planning Engine
* Policy Engine
* Approval Engine
* Flow Engine
* Audit Engine

---

## Flow Layer

Defines declarative orchestration.

Flows describe:

* business intent
* orchestration logic
* execution order
* branching
* approvals
* expected outputs

Flows never contain business logic.

---

## Registry Layer

The platform is metadata-driven.

Dedicated registries describe:

* Plugins
* Capabilities
* Tools
* Agents
* Personas

This allows new applications to integrate without modifying SIL Core.

---

# Application Independence

SIL is intentionally application-agnostic.

Applications expose business capabilities through Plugins.

Examples include:

* STATIS Pipeline
* Sudreg
* Data Catalogue
* RIAD
* Future STATIS applications

Applications remain fully autonomous.

SIL orchestrates them through a common execution model.

---

# Current MVP

The first implementation intentionally focuses on validating the architectural model.

Initial Flows include:

## STATIS Pipeline

* Explain Job
* Run Job
* Diagnose Failed Run
* Compare Runs

## Sudreg

* Explain Company

The objective of the MVP is not feature completeness.

Its purpose is to validate the architectural principles that will support future platform growth.

---

# Repository Structure

```text
docs/
│
├── README.md
│
├── 00_Principles.md
├── 01_Vision.md
├── 02_Architecture.md
├── 03_Core_Concepts.md
│
├── 10_Request_Engine.md
├── 11_Context_Engine.md
├── 12_Planning_Engine.md
├── 13_Policy_Engine.md
├── 14_Approval_Engine.md
├── 15_Flow_Engine.md
│
├── 16_Flow_DSL.md
├── 17_Capability_Registry.md
├── 18_Tool_Registry.md
├── 19_Agent_Registry.md
├── 20_Audit_Engine.md
│
├── 40_MVP.md
├── 41_Roadmap.md
└── examples/
```

---

# Reading Guide

For first-time readers the recommended reading order is:

1. **00_Principles**
2. **01_Vision**
3. **02_Architecture**
4. **03_Core_Concepts**
5. **10_Request_Engine**
6. **11_Context_Engine**
7. **12_Planning_Engine**
8. **13_Policy_Engine**
9. **14_Approval_Engine**
10. **15_Flow_Engine**
11. **Flow DSL and Registries**

This sequence introduces the platform from architectural foundations through runtime behaviour to extension mechanisms.

---

# Roadmap

The architecture has been designed to support future capabilities including:

* cross-application orchestration
* reusable enterprise Flow libraries
* semantic capability discovery
* multi-agent collaboration
* organization-wide Plugin ecosystem
* AI-assisted development
* AI-assisted operations
* AI-assisted governance
* workflow recommendations
* future AI models and interaction channels

These capabilities should be achievable without fundamental architectural redesign.

---

# Current Status

The repository currently contains the official architecture specification of the **STATIS Intelligence Layer**.

Implementation is intentionally secondary to architecture.

The objective of this project is to establish a stable, deterministic and extensible foundation that can support AI-driven interaction across the entire STATIS ecosystem for many years.

---

# Guiding Statement

> **Natural language should become the primary interface to enterprise software.**
>
> **Enterprise architecture should remain the primary authority over execution.**
>
> **STATIS Intelligence Layer exists to combine both.**
