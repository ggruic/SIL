# Plugin Model

> **STATIS Intelligence Layer (SIL)**  
> **Plugin Model**

**Document:** `30_Plugin_Model.md`  
**Version:** 0.1 (Draft)  
**Status:** Core Architecture  
**Owner:** SIL Core  
**Audience:** Software architects, backend developers, plugin developers, AI engineers, future contributors

## Purpose

The **Plugin Model** defines how third-party or custom extensions integrate with SIL. A *Plugin* is a bundled component that contributes definitions and integrations, such as new Flows, Capabilities, Tools, Agents (Personas), Context Providers, Policies, and Approval rules. Plugins allow the platform to remain flexible and extensible while maintaining deterministic control. 

In SIL, Plugins do **not** execute business logic directly. Instead, each Plugin provides static metadata and integration code that the SIL core uses to extend its functionality. For example, a Plugin for a CI/CD system might declare a new Capability to start a pipeline job, a Tool that knows how to call the pipeline application, and a Flow that orchestrates those steps. By packaging this as a Plugin, all new operations can be added without modifying SIL’s core. 

The Plugin Model exists to support SIL’s commitment to *Plugin-based extensibility* and *layered determinism*. Plugins are first-class extension units that register their contributions with SIL. This explicit registration ensures that all additions are visible to the SIL registry components (Flows, Capabilities, Tools, Agents, etc.) and are subject to the same governance (Policy and Approval) as core platform actions. In this way, the SIL core remains agnostic to the specifics of each Plugin while preserving auditability and explainability of every decision and execution step.

## Responsibilities and Boundaries

The Plugin Manager component of SIL is responsible for loading and registering Plugin contributions. Each Plugin declares its identity (a unique `id`, name, version, etc.) and explicitly lists any provided Flows, Capabilities, Tools, Agents, Context Providers, Policies, or Approval rules. Upon installation or startup, SIL validates the Plugin’s manifest and registers all declared definitions in the appropriate registries (Flow Registry, Capability Registry, Tool Registry, etc.). SIL ensures that each resource (flow, capability, tool, etc.) receives a unique identifier, and that any conflicts (duplicate IDs or incompatible versions) are detected and reported.

Plugins are *declarative and integrative*, not autonomous executors. They declare *what* resources they provide and may supply adapter or integration code, but they do not decide *when* or *how* to use those resources. The Planning Engine and Flow Engine retain control over scheduling and invocation according to the user’s Request and system policies.

Plugins must not bypass SIL’s governance layers. They cannot invoke an application directly without using a registered Tool, nor can they skip any required Policy or Approval checks. Any action provided by a Plugin is subject to the same policy evaluation and approval workflows as core capabilities.

In terms of boundaries, the Plugin Model defines only how extensions are declared and registered. It does not prescribe execution semantics or user interface behavior. Plugins do not handle authentication or direct user input; they simply augment the SIL model of available operations. Error handling for Plugins is managed by the SIL core: if a Plugin fails validation or execution, the platform should handle it gracefully (for example, by aborting the Request or rolling back) to keep the system stable and predictable.

## Processing Model

The Plugin loading process follows two high-level phases: registration and consumption. In the **registration** phase, SIL processes the Plugin manifest and registers each declared resource in the appropriate registry. For example, new Flows are added to the Flow Registry, new Capabilities to the Capability Registry, and new Tools to the Tool Registry. Each contributed resource is stored with the Plugin’s ID, making explicit which Plugin provided it. SIL also validates all identifiers and dependencies, catching any conflicts (such as duplicate IDs or incompatible versions) before runtime.

The **consumption** phase refers to normal request processing after Plugins are loaded. Once a Plugin’s contributions are registered, they become part of the execution environment. When the Planning Engine constructs an Execution Plan, it can select Flows and Capabilities that originated from any Plugin. At runtime, the Flow Engine executes these Flows and invokes the corresponding Tools, treating plugin-provided components exactly like core components. Because all Plugin additions were recorded in advance, request processing proceeds without any further Plugin-specific steps.

The following diagram illustrates the Plugin processing model:

```mermaid
graph LR
    PL[Plugin Package]
    PL -->|Manifest + Contributions| VD[Validate & Load]
    VD --> CR[Capability Registry]
    VD --> TR[Tool Registry]
    VD --> FR[Flow Registry]
    VD --> AR[Agent Registry]
    VD --> PE[Policy Engine]
    VD --> AE[Approval Engine]
    VD --> CE[Context Engine]
    CR --> FlowEngine[Flow Engine (execution)]
    FR --> FlowEngine
    TR --> FlowEngine
    AR --> FlowEngine
    PE --> FlowEngine
    AE --> FlowEngine
    CE --> FlowEngine
```

In this diagram, a *Plugin Package* provides a manifest and component definitions, which the SIL core validates and uses to update its registries. Later, the Flow Engine consumes these registered definitions as needed to execute an Execution Plan.

## Plugin Definition Model

A Plugin is described by a structured manifest (typically a YAML or JSON file) that includes its metadata and contributions. A basic Plugin manifest might look like:

```yaml
plugin:
  id: pipeline
  name: "CI/CD Pipeline Plugin"
  version: 1.0
  description: Provides pipeline job management flows and tools.
  dependencies:
    - core>=1.0
  contributions:
    flows:
      - pipeline.job.run_and_summarize
    capabilities:
      - pipeline.job.run
    tools:
      - pipeline.job.run.default
    agents:
      - pipeline.reporter
    context_enrichers:
      - pipeline_user_context
    policies:
      - name: "Pipeline Execution Policy"
        file: pipeline-policy.yaml
    approvals:
      - name: "PipelineApproval"
        file: pipeline-approval.yaml
```

In this example:
- `id`, `name`, and `version` identify the Plugin and allow SIL to manage different versions.
- `dependencies` may list required minimum versions of SIL or other plugins.
- Under `contributions`, the Plugin lists the IDs of each resource it provides: flows, capabilities, tools, agents (personas), context enrichers, policies, and approval rules. The actual definitions of these contributions (for example, the YAML for the flow or the policy rules) would be included in separate files (like `pipeline-policy.yaml`).
- Each contributed Capability or Tool would also include its own definition that references this plugin, for example:

```yaml
capability:
  id: pipeline.job.run
  name: "Run Pipeline Job"
  description: "Starts a pipeline job in a selected environment."
  plugin: pipeline
  application: pipeline-service
  version: 1.0
```

```yaml
tool:
  id: pipeline.job.run.default
  name: "Pipeline Job Runner"
  description: "Invokes the Pipeline service to start a job."
  plugin: pipeline
  application: pipeline-service
  version: 1.0
  implements:
    - pipeline.job.run
  invocation:
    protocol: rest
    operation: startJob
```

By listing the Plugin ID in each resource (`plugin: pipeline`), SIL knows which Plugin contributed which component. All identifiers (like `pipeline.job.run`) should use a consistent naming scheme.

## Behavioural Rules

- Which Plugins are installed and available for a given session or environment?  
- Which Plugin (by `id` and `version`) contributed a particular Flow, Capability, or Tool?  
- How are naming conflicts resolved if two Plugins define the same resource identifier?  
- When a new Plugin is added or an existing Plugin is updated, how does SIL ensure its contributions are correctly registered before any Request is planned?  
- What happens if a Request refers to a resource from a Plugin that is not currently loaded or has been removed?  

- All Plugins must explicitly declare every extension they provide; implicit or hidden contributions are not allowed.  
- The SIL core treats plugin-provided components as first-class citizens. Plugin identities should be recorded in all plan and audit logs for traceability.  
- Plugins should not introduce non-determinism. Any plugin logic must execute through deterministic Tools or Agents given the same inputs.  
- Removing or disabling a Plugin should invalidate its contributions. The platform should fail fast if an Execution Plan references a now-missing Plugin resource.  
- Plugins are loaded between Requests. The system does not allow dynamic plugin loading during the execution of an already-planned Request.  

## Examples

Imagine a user request to run a pipeline job: *“Start the CI pipeline for PR #123.”* The Processing Engine turns this into a Capability request (`pipeline.job.run`) with parameters. The Planning Engine might select a Flow (defined by the pipeline Plugin) to accomplish this task. The Execution Plan could look like:

```yaml
request:
  intent: pipeline.job.run
  parameters:
    pr_number: 123

execution_plan:
  flow:
    id: pipeline.job.run_and_summarize
    version: 1.0
  plugins:
    - pipeline
  parameters:
    environment: "prod"
    pr: 123
```

This plan indicates that the Flow `pipeline.job.run_and_summarize` (defined by the `pipeline` Plugin) will be executed. During execution, the Flow Engine loads the flow definition and invokes each step’s Tool. Each step corresponds to a Capability (e.g. `pipeline.job.run`) implemented by a Tool contributed by the Plugin. Because the plugin’s contributions were registered in advance, the execution is fully deterministic and auditable, with the Plugin ID noted in logs.

Another example is dynamic plugin discovery. Suppose a new Plugin `backups` is installed that adds a Capability `database.backup` with its Tool and Flow. After registration, this new capability can be used like any other. For instance, a request with intent `database.backup` will use the `backups` Plugin’s definitions without any core code changes.

```yaml
# plugins/backups/plugin.yaml
plugin:
  id: backups
  name: "Database Backup Plugin"
  version: 2.1
  description: Enables scheduling and running backups of database instances.
  contributions:
    capabilities:
      - database.backup
    tools:
      - database.backup.default
    flows:
      - database.backup.schedule
      - database.backup.run
    policies:
      - name: "BackupAuthorization"
        file: backup-policy.yaml
```

With this manifest, SIL will load the `backups` Plugin’s capabilities, tools, and flows. If a request is made for `database.backup`, the Planning Engine can pick the `database.backup.run` flow, and the Plugin’s Tool will execute the backup process. No modifications to core SIL code are needed to support this new operation.

## Architecture Decisions

### AD-3001

**Plugin extensibility is core to SIL.** SIL uses a Plugin model to allow extensions to provide new Flows, Capabilities, Tools, Agents, and policies. All plugin-provided definitions must be explicitly registered in the corresponding registries (Flow Registry, Capability Registry, etc.) at load time.

### AD-3002

**Plugin contributions must be declared, not implicit.** Plugins cannot add new resources through hidden code. Instead, every extension must appear in a Plugin manifest or definition file. This ensures the platform retains a complete, auditable picture of all available functionality.

### AD-3003

**Unique namespacing for plugin resources.** Each Plugin has a unique ID. All definitions contributed by a Plugin include the Plugin ID and use globally unique identifiers (for example, prefixed by plugin name). This prevents collisions between different plugins or between plugins and core components.

### AD-3004

**Plugin metadata and version control.** Plugins include version information. SIL must manage plugin compatibility, ensuring that plugins only load if their declared dependencies are satisfied (such as a minimum SIL version). Plugins with conflicting definitions or version requirements must be detected as errors before execution.

### AD-3005

**No runtime code injection into flows.** Plugins may include helper code for Tools or Agents, but Flow definitions remain declarative. Any computation must occur in Tools or external Applications. Flow steps only reference named operations, preserving explainability of the orchestration.

### AD-3006

**Fail-fast on plugin errors.** If a Plugin fails validation or a contributed resource cannot be registered, SIL should handle it immediately. The platform should not partially apply a plugin’s contributions; either all declared definitions from a plugin are accepted, or none are, to maintain consistency in the registries.

## Future Evolution and Related Documents

The Plugin Model defined in this document is intended to remain stable at the architectural level. Future work may explore refinements around plugin distribution and lifecycle. Areas for future evolution include:

- Plugin dependency resolution and cross-plugin references.  
- Official plugin repository or marketplace integration for distribution.  
- Fine-grained permission scopes for plugin-provided operations.  
- Enhanced sandboxing or security isolation for plugin code.  
- Versioning strategies for rolling upgrades of plugin definitions.  
- Plugin telemetry and usage analytics for governance.  

The following SIL architecture documents should be read in conjunction with the Plugin Model:

- [00_Principles](00_Principles.md)  
- [01_Vision](01_Vision.md)  
- [02_Architecture](02_Architecture.md)  
- [03_Core_Concepts](03_Core_Concepts.md)  
- [10_Request_Engine](10_Request_Engine.md)  
- [11_Context_Engine](11_Context_Engine.md)  
- [12_Planning_Engine](12_Planning_Engine.md)  
- [13_Policy_Engine](13_Policy_Engine.md)  
- [14_Approval_Engine](14_Approval_Engine.md)  
- [15_Flow_Engine](15_Flow_Engine.md)  
- [16_Flow_DSL](16_Flow_DSL.md)  
- [17_Capability_Registry](17_Capability_Registry.md)  
- [18_Tool_Registry](18_Tool_Registry.md)  
- [19_Agent_Registry](19_Agent_Registry.md)  

> **A strong Plugin Model makes extension safe, explicit and explainable.** By requiring all plugin contributions to be declared and registered, SIL preserves deterministic orchestration and governance even as new integrations are added.
