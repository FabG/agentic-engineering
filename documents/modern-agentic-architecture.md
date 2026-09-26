# Modern Agentic Architecture

## The Factory Model

Treat an agent as a runtime container: it provides the execution loop and hosts a model, context, skills, tools, policies, and evaluation hooks. "Container" describes the architectural role; it does not require packaging the agent as an operating-system container.

In this model, the durable intellectual property is usually not the agent wrapper itself. It is the domain-specific skills and instructions, curated context and knowledge, reliable tool interfaces, policy constraints, and evaluation data that make the system useful. A shared runtime or factory can assemble these capabilities into agents for different roles, environments, or risk levels. Agent configurations and their constituent assets can be versioned, tested, approved, and deployed independently where their contracts allow it.

This favors reusable capabilities and replaceable runtimes. It also asks the platform team to build and operate more of the common infrastructure: capability contracts, lifecycle management, telemetry, policy enforcement, and compatibility testing.

## What Actually Makes the Factory Model Work

The container analogy only holds when agents have the equivalent of an image specification and a runtime interface. Without shared contracts, a registry of loosely related prompts and tools is not a portable factory; each agent remains a bespoke application.

- **Agent manifest:** A versioned, machine-readable declaration of an agent's identity, runtime requirements, model constraints, inputs and outputs, referenced skills and tools, permissions, policies, resource limits, and compatibility requirements. It should identify immutable versions or digests of its dependencies so a deployed agent can be reproduced and reviewed.
- **Tool and skill protocol:** A common way to discover and invoke capabilities, with typed input and output schemas, identity and authorization propagation, error semantics, timeouts, and explicit declarations of side effects and idempotency. Protocol compatibility should be testable, not inferred from matching names.
- **Context contract:** A defined interface for supplying and retrieving context, including source identity and provenance, sensitivity labels, access boundaries, freshness, and relevant metadata. The contract should make clear what the runtime may retain, log, or pass to models and tools.
- **Control plane and registry:** A trusted place to publish, discover, approve, version, and deprecate manifests and capabilities. It should support promotion between environments, compatibility checks, rollback, ownership, and policy enforcement; the runtime should resolve only authorized versions.
- **Portable evaluations:** Versioned evaluation cases and expected criteria that can run against different runtimes through a common harness or adapter. Results must record the agent manifest and dependency versions, model configuration, evaluator version, and thresholds, so portability does not erase the evidence needed to compare behavior.

The runtime interface also needs lifecycle semantics: how an agent is started, provided identity and secrets, given state, observed, cancelled, and shut down. The factory becomes real when independent runtimes can consume these contracts and produce sufficiently comparable, auditable behavior. Standards and adapters reduce coupling, but they do not make model outputs identical across runtimes.

## Framework-Led Development

Frameworks such as Google ADK, LangGraph, and CrewAI provide abstractions and components for building agent workflows. Depending on the framework and how it is used, these can accelerate development with ready-made orchestration patterns, integrations, state handling, and developer tooling. The trade-off is that application logic can become coupled to framework APIs, execution semantics, or deployment assumptions. That coupling may be a good choice when the framework's model fits the problem and its productivity benefits outweigh the cost of migration or abstraction.

These approaches are not mutually exclusive. A team can use a framework to implement an agent runtime while keeping domain skills, tools, policy interfaces, and evaluation suites behind stable contracts. Framework choice is then an implementation decision within the broader architecture, rather than the architecture's only organizing principle.

| Dimension | Modular factory | Framework-led application |
| --- | --- | --- |
| Where IP lives | Primarily in domain skills, context, tools, policies, and evaluations; runtime is replaceable infrastructure | In workflow code and framework-specific configuration, alongside domain assets |
| Portability | Higher when capabilities use stable, framework-neutral contracts; adapters still have a cost | Varies by framework; APIs, state models, and execution semantics can make migration harder |
| Time-to-first-agent | Usually slower because shared interfaces and platform foundations come first | Usually faster when built-in abstractions and integrations fit the use case |
| Ecosystem leverage | Must select and integrate components; can combine providers behind chosen contracts | Can use framework integrations, examples, and community packages directly |
| Composability | Designed around reusable capabilities that can be assembled across agents and runtimes | Strong within the framework's composition model; cross-framework reuse may need adapters |
| Lock-in risk | Lower framework dependence, but risk shifts to internal platform APIs and operational know-how | Higher when core behavior relies on framework-specific features; mitigated by boundaries and exportable assets |
| Governance | Central controls and lifecycle can be built into the factory and applied consistently | Can leverage framework features, but governance often needs additional platform-level controls |
| Talent | Needs platform, interface, and integration skills in addition to agent engineering | Benefits from developers already familiar with the framework and its ecosystem |
| Best fit | Multiple agents, substantial reuse, varied runtimes, or centralized governance needs | A focused workflow where framework conventions accelerate delivery |

These are tendencies, not guarantees. A poorly designed internal platform can become its own source of lock-in, and a framework-based system can preserve portability and governance through deliberate boundaries and controls.

## Honest Trade-offs

### Factory Approach Wins When

- Multiple agents share skills, tools, context, or controls, and the cost of repeated integration is already visible.
- Agents need to run across different models, frameworks, teams, or deployment environments, and stable contracts can keep that choice open.
- Centralized versioning, approval, policy enforcement, audit evidence, and consistent evaluation are platform requirements rather than future possibilities.
- The organization can fund and operate the control plane, runtime interfaces, adapters, and compatibility tests that make the factory useful.

### Framework-Coupled Wins When

- A workflow is new or narrowly scoped, and framework primitives closely match its orchestration and state needs.
- Time-to-first-agent and fast iteration matter more than cross-runtime reuse, while the expected cost of later migration is acceptable.
- The framework provides valuable integrations, observability, or operational behavior that would be expensive to reproduce.
- The team knows the framework well, and it can meet the required security, governance, and audit controls without a separate factory platform.

Framework coupling is not automatically a problem, and a factory is not automatically portable. Choose the smallest architecture that meets present requirements; preserve escape routes at the boundaries that are costly to change, and revisit them as reuse, risk, or scale becomes concrete.

## Regulated and High-Assurance Use

In finance, healthcare, and other regulated settings, choose architecture based on the evidence and controls the organization must demonstrate, not only on how quickly a prototype can be assembled. A framework can be suitable if its behavior is observable and the surrounding system supplies the required controls; a modular platform does not provide compliance automatically.

Design for these capabilities:

- **Governance:** Maintain approved, versioned inventories of models, skills, context sources, and tools. Assign owners, risk classifications, access rules, and approval paths; separate development, approval, and production access where required.
- **Evaluation:** Run documented, risk-based evaluations before release and after changes to models, prompts, skills, context, or tools. Include representative and edge-case scenarios, task success, groundedness, unsafe actions, and abstention behavior. Keep results tied to the exact versions evaluated, and monitor for regressions and drift in production.
- **Guardrails:** Enforce authorization and business rules at the tool or service boundary, not only in prompts. Add input and output validation, least-privilege access, transaction limits, escalation paths, and human approval for high-impact actions. Define fail-closed behavior where an unsafe or uncertain action could cause harm.
- **Explainability and audit:** Record a traceable account of the versions used, relevant context and source references, tool requests and outcomes, policy checks, approvals, and final actions. Protect sensitive data in logs and apply retention controls. Provide evidence and provenance for decisions; a generated explanation is not, by itself, proof of how a decision was reached.
- **Data controls:** Minimize sensitive data shared with models and tools. Apply access, residency, consent, retention, and deletion requirements to both live context and evaluation datasets.

Prefer deterministic workflows or explicit human review for decisions and actions whose consequences require stronger assurance than a probabilistic agent can provide. Keep the agent's scope and tool permissions narrow, and make its limits visible to operators.

## Choosing an Approach

Make the decision against the workflow's risk, expected reuse, required evidence, deployment constraints, and the team's ability to operate a platform. Start with a framework when it materially shortens feedback cycles; invest in factory contracts when shared capabilities and controls justify their cost. Reassess as the system moves from prototype to production.