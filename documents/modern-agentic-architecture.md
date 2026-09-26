# Modern Agentic Architecture

## The Factory Model

Treat an agent as a runtime container: it provides the execution loop and hosts a model, context, skills, tools, policies, and evaluation hooks. "Container" describes the architectural role; it does not require packaging the agent as an operating-system container.

In this model, the durable intellectual property is usually not the agent wrapper itself. It is the domain-specific skills and instructions, curated context and knowledge, reliable tool interfaces, policy constraints, and evaluation data that make the system useful. A shared runtime or factory can assemble these capabilities into agents for different roles, environments, or risk levels. Agent configurations and their constituent assets can be versioned, tested, approved, and deployed independently where their contracts allow it.

This favors reusable capabilities and replaceable runtimes. It also asks the platform team to build and operate more of the common infrastructure: capability contracts, lifecycle management, telemetry, policy enforcement, and compatibility testing.

## Framework-Led Development

Frameworks such as Google ADK, LangGraph, and CrewAI provide abstractions and components for building agent workflows. Depending on the framework and how it is used, these can accelerate development with ready-made orchestration patterns, integrations, state handling, and developer tooling. The trade-off is that application logic can become coupled to framework APIs, execution semantics, or deployment assumptions. That coupling may be a good choice when the framework's model fits the problem and its productivity benefits outweigh the cost of migration or abstraction.

These approaches are not mutually exclusive. A team can use a framework to implement an agent runtime while keeping domain skills, tools, policy interfaces, and evaluation suites behind stable contracts. Framework choice is then an implementation decision within the broader architecture, rather than the architecture's only organizing principle.

| Dimension | Modular factory | Framework-led application |
| --- | --- | --- |
| Primary organizing idea | Assemble agents from versioned capabilities and shared platform services | Express workflows using the framework's abstractions and runtime |
| Initial investment | Higher platform and interface-design cost | Often faster to get a working workflow |
| Portability | Better when skills, tools, and state use framework-neutral contracts | May depend on framework APIs, state models, or execution behavior |
| Consistency at scale | Shared lifecycle, policy, and telemetry can standardize many agents | Depends on framework support and the conventions each team adopts |
| Best fit | Multiple agents, varied runtimes, strong reuse or governance needs | A focused workflow where built-in abstractions match the use case |

The table describes tendencies, not guarantees. A poorly designed internal platform can become its own source of lock-in, and a framework-based system can preserve portability through deliberate boundaries.

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

Compare approaches against the workflow's risk, expected number of agents, reuse needs, required audit evidence, deployment constraints, and the team's ability to operate a platform. Prototype with a framework when it materially shortens feedback cycles; invest in factory-level interfaces and controls when repeated use, portability, or centralized governance justifies the cost. Reassess the boundary as the system moves from prototype to production.