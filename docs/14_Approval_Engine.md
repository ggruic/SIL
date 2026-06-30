# 14 - Approval Engine

> **STATIS Intelligence Layer (SIL)**
> **Approval Engine**

Version: 0.1 (Draft)

---

# Purpose

The Approval Engine is responsible for managing human authorization when required by the Policy Engine.

It ensures that high-impact operations cannot continue until explicit approval has been obtained from an authorized user.

The Approval Engine never decides whether approval is required.

That decision belongs exclusively to the Policy Engine.

Likewise, the Approval Engine never executes business functionality.

Its sole responsibility is managing the approval lifecycle.

---

# Responsibilities

The Approval Engine is responsible for:

* receiving approval requests
* presenting approval information
* collecting user decisions
* validating approver identity
* validating approver permissions
* recording approval outcomes
* enriching the Request with approval information

The Approval Engine never modifies the Execution Plan.

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

The Approval Engine operates only when the Policy Engine determines that approval is required.

---

# Why Approval Exists

Some operations have business consequences that require explicit human authorization.

Examples include:

* running production jobs
* promoting releases
* modifying production configuration
* executing destructive operations
* performing organization-defined sensitive actions

The purpose of the Approval Engine is not to make these operations safer through AI.

Its purpose is to ensure that a responsible human explicitly authorizes them.

---

# Approval Lifecycle

Every approval follows the same lifecycle.

```text
Approval Required
        │
        ▼
Approval Request Created
        │
        ▼
Waiting For Decision
        │
        ├───────────────┐
        ▼               ▼
Approved            Rejected
        │               │
        ▼               ▼
Continue        Request Completed
Execution         (Rejected)
```

The lifecycle is deterministic and fully auditable.

---

# Approval Request

An Approval Request contains all information required for an informed decision.

Typical information includes:

* Request ID
* user initiating the Request
* selected Flow
* target application
* requested operation
* execution environment
* reason approval is required
* expected impact

The approver should never need to inspect internal implementation details.

Approval information should be presented in business language.

---

# Approval Decision

An approver may choose one of the following actions.

## Approve

Execution continues.

---

## Reject

Execution terminates.

The Request remains fully auditable.

---

## Cancel

The Request is voluntarily abandoned.

No further processing occurs.

---

# Approver Validation

Before accepting an approval, the Approval Engine validates:

* approver identity
* approver authentication
* approver authorization

Unauthorized approvals are rejected.

---

# Request Enrichment

The Approval Engine enriches the Request.

Example:

```yaml
approval:

    status:
        approved

    approved_by:
        john.doe

    approved_at:
        2026-07-01T09:15:42

    reason:
        production_execution
```

The Request now contains a complete approval record.

---

# Timeouts

Approval Requests may expire.

Typical reasons include:

* organizational policy
* security policy
* execution relevance
* scheduling constraints

Expired approvals terminate the Request.

---

# Multiple Approvals

Some organizations may require more than one approval.

Examples:

* four-eyes principle
* technical approval
* business approval

The Approval Engine supports multiple approval stages.

The exact approval model is defined by organizational policy rather than by the Approval Engine itself.

---

# Separation of Responsibilities

The Approval Engine does not:

* determine whether approval is required
* execute business operations
* evaluate organizational policies
* modify Execution Plans

Its responsibility begins after Policy evaluation and ends before Flow execution.

---

# User Experience

Approval requests should be presented in clear business language.

Example:

```text
Approve execution?

Operation:
Run Production Pipeline Job

Application:
Pipeline

Environment:
PROD

Requested by:
Goran Gruic

Reason:
Production execution requires approval.

Impact:
A production statistical job will be executed.

Approve?
```

The approver should understand the business consequence without requiring technical knowledge.

---

# Design Principles

## Human Authority

Humans authorize.

AI never authorizes.

---

## Explicit Decisions

Approval is always explicit.

Silence is never interpreted as approval.

---

## Auditability

Every approval decision is permanently recorded.

---

## Simplicity

Approval should be as simple as possible.

Complex approval workflows belong to organizational policy rather than to the Approval Engine.

---

# Architecture Decisions

### AD-14-001

The Approval Engine manages approvals but never decides when they are required.

---

### AD-14-002

Approval decisions are always performed by authenticated and authorized users.

---

### AD-14-003

Approval outcomes become part of the Request.

---

### AD-14-004

Rejected and expired approvals terminate the Request.

---

### AD-14-005

The Approval Engine never executes business functionality.

---

# Open Questions / Future Evolution

The following topics will be specified later:

* approval notification channels
* delegated approvals
* approval reminders
* approval expiration policies
* approval user interface
* integration with organizational workflow systems

These implementation details do not change the architectural responsibility of the Approval Engine.
