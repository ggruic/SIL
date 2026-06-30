# 15 - Flow Engine

> **STATIS Intelligence Layer (SIL)**
> **Flow Engine**

Version: 0.1 (Draft)

---

# Purpose

The Flow Engine is responsible for executing the Execution Plan produced by the Planning Engine.

It orchestrates business Capabilities according to the selected Flow.

The Flow Engine never contains business logic.

Business logic remains inside applications.

The Flow Engine coordinates execution.

---

# Responsibilities

The Flow Engine is responsible for:

* loading the selected Flow
* executing Flow steps
* resolving required Capabilities
* invoking Tools
* collecting execution results
* handling execution errors
* enriching the Request
* producing execution output

The Flow Engine never bypasses SIL architecture.

Every operation is performed through registered Capabilities.

---

# Position Within SIL

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
Audit Engine
```

The Flow Engine begins only after the Request has successfully completed all previous stages.

---

# Why Flow Exists

A Request describes **what** the user wants.

The Execution Plan selects **which Flow** should be executed.

The Flow defines **how** the Request is fulfilled.

The Flow Engine executes that definition.

---

# Execution Model

Every execution follows the same pattern.

```text
Execution Plan

↓

Load Flow

↓

Execute Step 1

↓

Execute Step 2

↓

Execute Step N

↓

Collect Results

↓

Complete Request
```

The execution order is deterministic.

---

# Flow Definition

A Flow is a declarative description of execution.

It contains:

* metadata
* required Capabilities
* execution steps
* conditions
* outputs

A Flow does not contain application logic.

---

# Flow Steps

A Flow is composed of ordered Steps.

Typical examples:

```text
Resolve Job

↓

Run Job

↓

Wait For Completion

↓

Read Logs

↓

Generate Summary
```

Each Step performs one responsibility.

---

# Capability Resolution

The Flow Engine never executes applications directly.

Instead it requests the required Capability.

Example

```text
Capability

Run Job

↓

Capability Registry

↓

Tool Registry

↓

Pipeline Tool

↓

Pipeline Application
```

The Flow Engine does not know how the Capability is implemented.

---

# Tool Invocation

After resolving a Capability, the corresponding Tool is executed.

Examples:

Capability

Search Company

↓

Sudreg Tool

Capability

Read Logs

↓

Pipeline Tool

Capability

Search Dataset

↓

Catalogue Tool

The Flow Engine communicates only through registered Tools.

---

# Request Enrichment

Every completed Step contributes additional information to the Request.

Example

```yaml
execution:

    current_step:
        Read Logs

results:

    logs:
        ...

status:
    running
```

Execution history becomes part of the Request.

---

# Error Handling

If a Step fails:

* execution stops
* the error is recorded
* the Request is updated
* downstream execution is skipped

Recovery strategies are defined by the Flow.

The Flow Engine itself remains deterministic.

---

# Flow Completion

A Flow completes when:

* all Steps finish successfully
* execution is rejected
* execution fails
* execution is cancelled

The final execution state is stored inside the Request.

---

# Application Independence

The Flow Engine never communicates directly with applications.

All communication occurs through:

Capabilities

↓

Tools

↓

Plugins

↓

Applications

Applications remain isolated from SIL internals.

---

# Design Principles

## Flows Describe Execution

They do not contain business rules.

---

## Capabilities Are Business Concepts

Flows depend on Capabilities.

Never on Tool implementations.

---

## One Step, One Responsibility

Each Step performs a single operation.

---

## Deterministic Execution

Execution order is explicitly defined.

No hidden behaviour exists.

---

## Request-Centric Execution

The Request remains the single source of truth.

Every execution result becomes part of the Request.

---

# Architecture Decisions

### AD-15-001

The Flow Engine executes Flows, not business logic.

---

### AD-15-002

Capabilities are resolved before Tool execution.

---

### AD-15-003

The Flow Engine never communicates directly with applications.

---

### AD-15-004

Every Step enriches the Request.

---

### AD-15-005

Execution is deterministic.

---

# Open Questions / Future Evolution

The following implementation topics are intentionally deferred:

* parallel Step execution
* retries
* timeout handling
* conditional branching
* asynchronous execution
* distributed execution
* execution monitoring

These implementation details do not change the architectural role of the Flow Engine.
