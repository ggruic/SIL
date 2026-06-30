# 16 - Flow DSL

> **STATIS Intelligence Layer (SIL)**
> **Flow Definition Model**

Version: 0.1 (Draft)

---

# Purpose

The Flow DSL defines how SIL describes executable orchestration.

A Flow is a declarative model that tells the Flow Engine how to fulfill a Request using registered Capabilities.

The Flow DSL does not describe application logic.

It describes orchestration.

---

# Important Clarification

Flow DSL is not YAML.

YAML is only one possible representation.

A Flow may later be stored as:

* YAML
* JSON
* database record
* generated object
* API resource

The important concept is the **Flow Definition Model**.

---

# What Is a Flow?

A Flow describes how a Request is fulfilled.

Example:

```text
Request:
"Run FA validation and explain the result."

Flow:
1. Find Job
2. Run Job
3. Wait For Completion
4. Read Logs
5. Summarize Result
```

A Flow always operates through Capabilities.

It never calls APIs directly.

---

# Design Principles

## Flows Are Declarative

A Flow describes what should happen.

It does not contain executable code.

---

## Flows Are Business-Readable

A Flow should be understandable to a technical business user.

---

## Flows Are Small

A Flow should remain focused.

If a Flow becomes too large, it probably contains logic that belongs in an application.

---

## Flows Never Skip Layers

A Flow may call a Capability.

It may not call a Tool, REST endpoint, MCP method, SQL query or script directly.

Correct:

```text
Flow → Capability → Tool → Application
```

Incorrect:

```text
Flow → REST API
Flow → MCP Method
Flow → SQL Query
```

---

# Flow Structure

A Flow definition contains the following logical sections:

```yaml
id:
name:
description:
version:

matches:

requires:

parameters:

steps:

output:

audit:
```

---

# Flow Metadata

Metadata identifies the Flow.

```yaml
id: pipeline.run_job_and_summarize
name: Run Job and Summarize
description: Runs a Pipeline job and summarizes the result.
version: 0.1
```

The Flow ID should be stable and globally unique.

Recommended naming:

```text
<plugin>.<domain>.<action>
```

Examples:

```text
pipeline.job.explain
pipeline.job.run_and_summarize
pipeline.run.diagnose_failed
sudreg.company.explain
```

---

# Matching

The `matches` section describes which Requests this Flow may satisfy.

```yaml
matches:
  intents:
    - run_job
  entities:
    - job
  applications:
    - pipeline
```

Matching is used by the Planning Engine.

It is not executed by the Flow Engine.

---

# Requirements

The `requires` section describes required Capabilities.

```yaml
requires:
  capabilities:
    - pipeline.job.find
    - pipeline.job.run
    - pipeline.run.read
    - pipeline.run.logs.read
```

A Flow cannot be selected unless all required Capabilities are available.

---

# Parameters

Parameters define information required by the Flow.

```yaml
parameters:
  job:
    type: job_ref
    required: true

  environment:
    type: enum
    values: [DEV, TEST, PROD]
    default: DEV

  period:
    type: period
    required: false
```

Parameters may be resolved from:

* user request
* execution context
* defaults
* previous step outputs

---

# Steps

Steps define the execution sequence.

MVP supports four Step types:

```text
capability
condition
wait
agent
```

No other Step types are required for the MVP.

---

# Capability Step

A `capability` Step invokes a registered business Capability.

```yaml
- id: find_job
  type: capability
  capability: pipeline.job.find
  input:
    query: "{{request.entities.job}}"
    environment: "{{request.parameters.environment}}"
```

The Flow Engine resolves the Capability to a Tool.

The Flow never knows which Tool implementation is used.

---

# Wait Step

A `wait` Step waits until a condition becomes true.

Typical use:

* wait for job completion
* wait for external process
* wait for asynchronous result

```yaml
- id: wait_run
  type: wait
  capability: pipeline.run.status
  input:
    run_id: "{{steps.start_run.output.run_id}}"
  until: "{{result.status in ['SUCCESS', 'FAILED', 'CANCELLED']}}"
  timeout_seconds: 3600
```

For MVP, wait should remain simple.

No complex loops or custom retry logic.

---

# Condition Step

A `condition` Step enables simple branching.

```yaml
- id: check_status
  type: condition
  if: "{{steps.wait_run.output.status == 'FAILED'}}"
  then: diagnose_failure
  else: summarize_success
```

For MVP, conditions should be limited to simple expressions over Request, Context and previous Step outputs.

---

# Agent Step

An `agent` Step invokes an AI persona for reasoning, explanation or summarization.

```yaml
- id: summarize
  type: agent
  agent: business_reporter
  input:
    prompt: |
      Summarize this Pipeline run for a business user.

      Run:
      {{steps.wait_run.output}}

      Logs:
      {{steps.read_logs.output}}
```

Agents do not execute application operations.

They reason over already retrieved information.

---

# Output

The `output` section defines what the Flow returns.

```yaml
output:
  type: markdown
  source: "{{steps.summarize.output}}"
```

The output should be suitable for the original Request channel.

---

# Audit

The `audit` section defines audit behaviour.

```yaml
audit:
  enabled: true
  include:
    - request
    - flow
    - steps
    - capabilities
    - tool_results
    - final_output
```

For MVP, audit should be enabled for every Flow.

---

# Example: Explain Job

```yaml
id: pipeline.job.explain
name: Explain Job
description: Explains what a Pipeline job does.
version: 0.1

matches:
  intents:
    - explain_job
  entities:
    - job
  applications:
    - pipeline

requires:
  capabilities:
    - pipeline.job.find
    - pipeline.job.read
    - pipeline.job.files.read

parameters:
  job:
    type: job_ref
    required: true

steps:
  - id: find_job
    type: capability
    capability: pipeline.job.find
    input:
      query: "{{request.entities.job}}"

  - id: read_job
    type: capability
    capability: pipeline.job.read
    input:
      job_id: "{{steps.find_job.output.job_id}}"

  - id: explain
    type: agent
    agent: technical_explainer
    input:
      prompt: |
        Explain what this job does.
        Focus on purpose, inputs, outputs and risks.

        Job:
        {{steps.read_job.output}}

output:
  type: markdown
  source: "{{steps.explain.output}}"

audit:
  enabled: true
```

---

# Example: Run Job and Summarize

```yaml
id: pipeline.job.run_and_summarize
name: Run Job and Summarize
description: Runs a Pipeline job and summarizes the execution result.
version: 0.1

matches:
  intents:
    - run_job
  entities:
    - job
  applications:
    - pipeline

requires:
  capabilities:
    - pipeline.job.find
    - pipeline.job.run
    - pipeline.run.status
    - pipeline.run.logs.read

parameters:
  job:
    type: job_ref
    required: true

  environment:
    type: enum
    values: [DEV, TEST, PROD]
    default: DEV

steps:
  - id: find_job
    type: capability
    capability: pipeline.job.find
    input:
      query: "{{request.entities.job}}"
      environment: "{{request.parameters.environment}}"

  - id: start_run
    type: capability
    capability: pipeline.job.run
    input:
      job_id: "{{steps.find_job.output.job_id}}"

  - id: wait_run
    type: wait
    capability: pipeline.run.status
    input:
      run_id: "{{steps.start_run.output.run_id}}"
    until: "{{result.status in ['SUCCESS', 'FAILED', 'CANCELLED']}}"
    timeout_seconds: 3600

  - id: read_logs
    type: capability
    capability: pipeline.run.logs.read
    input:
      run_id: "{{steps.start_run.output.run_id}}"

  - id: summarize
    type: agent
    agent: run_reporter
    input:
      prompt: |
        Summarize the result of this Pipeline run.

        Status:
        {{steps.wait_run.output}}

        Logs:
        {{steps.read_logs.output}}

output:
  type: markdown
  source: "{{steps.summarize.output}}"

audit:
  enabled: true
```

---

# Example: Explain Company

This Flow demonstrates that SIL is not Pipeline-specific.

```yaml
id: sudreg.company.explain
name: Explain Company
description: Finds a company in Sudreg and explains its basic registry information.
version: 0.1

matches:
  intents:
    - explain_company
  entities:
    - company
  applications:
    - sudreg

requires:
  capabilities:
    - sudreg.company.search
    - sudreg.company.read
    - sudreg.company.owners.read

parameters:
  company:
    type: company_ref
    required: true

steps:
  - id: search_company
    type: capability
    capability: sudreg.company.search
    input:
      query: "{{request.entities.company}}"

  - id: read_company
    type: capability
    capability: sudreg.company.read
    input:
      company_id: "{{steps.search_company.output.company_id}}"

  - id: read_owners
    type: capability
    capability: sudreg.company.owners.read
    input:
      company_id: "{{steps.search_company.output.company_id}}"

  - id: explain
    type: agent
    agent: business_analyst
    input:
      prompt: |
        Explain this company in clear business language.

        Company:
        {{steps.read_company.output}}

        Owners:
        {{steps.read_owners.output}}

output:
  type: markdown
  source: "{{steps.explain.output}}"

audit:
  enabled: true
```

---

# MVP Restrictions

For MVP, the Flow DSL intentionally does not support:

* arbitrary code
* shell commands
* direct REST calls
* direct MCP calls
* SQL queries
* loops
* parallel execution
* dynamic tool selection by AI
* hidden side effects

These restrictions are intentional.

They keep the MVP safe, explainable and implementable.

---

# Architecture Decisions

### AD-16-001

Flow DSL represents a Flow Definition Model, not YAML specifically.

---

### AD-16-002

Flows are declarative and contain no executable code.

---

### AD-16-003

Flows invoke Capabilities, never Tools or APIs directly.

---

### AD-16-004

MVP supports only four Step types: capability, wait, condition and agent.

---

### AD-16-005

All MVP Flows must be auditable.

---

# Open Questions / Future Evolution

The following topics are intentionally deferred:

* Flow versioning strategy
* Flow inheritance
* reusable subflows
* retries
* parallel execution
* advanced branching
* Flow validation schema
* Flow editor UI
* AI-assisted Flow generation

These are not required for the MVP.
