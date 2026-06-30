# STATIS Pipeline Plugin

> Example SIL Plugin package for integrating STATIS Pipeline.

**Plugin ID:** `pipeline`  
**Version:** `0.1.0`  
**Status:** Draft MVP Example

## Purpose

This plugin demonstrates how a STATIS application integrates with the STATIS Intelligence Layer (SIL)
through a self-contained Plugin package.

It contributes all resources required for the four Pipeline MVP scenarios:

1. Explain Job
2. Run Job and Summarize
3. Diagnose Failed Run
4. Compare Last Two Runs

The plugin is intentionally declarative. It does not execute Requests directly. It contributes Flows,
Capabilities, Tools, Context Providers, Policies, Approvals, Personas and Agents that SIL Core can
validate, register and orchestrate.

## Package Structure

```text
pipeline-plugin/
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

The Pipeline Plugin owns the `pipeline.*` namespace.

SIL Core remains independent from Pipeline implementation details. Flows reference Capabilities,
Capabilities are implemented by Tools, and Tools call the Pipeline application.

```text
Request
  ↓
Flow
  ↓
Capability
  ↓
Tool
  ↓
Pipeline Application
```

## MVP Flows

| Flow | Purpose |
|---|---|
| `pipeline.job.explain` | Explains a Pipeline job using metadata and selected files. |
| `pipeline.job.run_and_summarize` | Runs a job, waits for completion, reads logs and summarizes the result. |
| `pipeline.run.diagnose_failed` | Reads a failed run, error files and logs, then proposes likely causes. |
| `pipeline.run.compare_last_two` | Compares the last two relevant runs and explains differences. |

## Notes

This package is an architectural example. Tool invocation details such as REST paths are illustrative and
should be adapted to the actual STATIS Pipeline API.
