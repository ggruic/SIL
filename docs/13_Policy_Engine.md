# 13 - Policy Engine

> **STATIS Intelligence Layer (SIL)**
> **Policy Engine**

Version: 0.1 (Draft)

---

# Purpose

The Policy Engine is responsible for determining whether a Request may continue toward execution.

It evaluates the Execution Plan against organizational rules, permissions and governance policies.

The Policy Engine never executes business functionality.

It never modifies business data.

Its only responsibility is making deterministic authorization decisions.

---

# Responsibilities

The Policy Engine is responsible for:

* evaluating execution permissions
* validating organizational policies
* checking plugin availability
* determining approval requirements
* denying prohibited operations
* enriching the Request with policy decisions

The Policy Engine never performs execution.

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
```

Policy evaluation always occurs before execution.

---

# Why Policy Exists

Understanding a Request does not mean the Request should be executed.

Example:

User:

> Promote Job to Production.

The Request may be perfectly understood.

The Execution Plan may be completely valid.

Execution may still be forbidden.

Policy exists to separate:

**Can we execute?**

from

**How would we execute?**

---

# Policy Evaluation

The Policy Engine evaluates every Execution Plan.

Typical inputs include:

* authenticated user
* user roles
* application
* Flow
* requested Capability
* environment
* workspace
* organizational rules

The result is always deterministic.

---

# Policy Outcomes

Every evaluation produces one of the following outcomes.

## Allow

Execution may continue.

---

## Approval Required

Execution may continue only after explicit human approval.

---

## Deny

Execution stops.

The reason should be recorded inside the Request.

---

# Typical Policy Examples

Examples include:

Allow

* Read Job
* Read Logs
* Explain Company

Approval Required

* Run Production Job
* Promote Job
* Git Commit

Deny

* Delete Workspace
* Read Secrets
* Execute Unknown Tool

Policy definitions remain independent from AI.

---

# Policy Sources

Policies may originate from multiple sources.

Examples:

* SIL Core
* Plugin Policies
* Organization Policies
* Environment Policies

The Policy Engine evaluates them as a unified policy model.

---

# Policy Decisions

Every decision should be explainable.

Example:

```text
Decision

Approval Required

Reason

Production execution

Policy

Production Operations Policy

Source

Pipeline Plugin
```

No decision should ever appear arbitrary.

---

# Request Enrichment

The Policy Engine enriches the Request.

Example:

```yaml
policy:

    decision:
        approval_required

    reason:
        production_execution

    policy:
        production_policy
```

The Execution Plan itself remains unchanged.

---

# Application Independence

Applications do not enforce SIL policies.

Applications continue enforcing their own business permissions.

SIL enforces orchestration policies.

This separation keeps responsibilities clear.

---

# Deterministic Behaviour

Policy evaluation must always be deterministic.

The same Request under the same conditions must produce the same decision.

AI must never determine authorization.

---

# Relationship with Approval

The Policy Engine never requests approval.

It only determines whether approval is required.

Approval is handled by the Approval Engine.

---

# Design Principles

## Policies Are Explicit

Hidden authorization logic should be avoided.

---

## Policies Are Explainable

Every decision must include its reason.

---

## Policies Are Independent

Policy definitions should remain separate from Flows.

---

## Policies Are Reusable

The same policy may protect multiple Flows.

---

# Architecture Decisions

### AD-13-001

Every Execution Plan must pass through Policy evaluation.

---

### AD-13-002

Authorization decisions are deterministic.

---

### AD-13-003

The Policy Engine never performs execution.

---

### AD-13-004

Approval requirements are determined by Policy.

---

### AD-13-005

Applications enforce business permissions.

SIL enforces orchestration policies.

---

# Open Questions / Future Evolution

The following topics will be specified later:

* Policy definition format
* Policy inheritance
* Policy versioning
* Conflict resolution
* External policy providers
* Policy caching

These implementation details do not affect the architectural role of the Policy Engine.
