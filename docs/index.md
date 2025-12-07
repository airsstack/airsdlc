---
hide:
  - navigation
  - toc
---

# AirSDLC

**The AI-Responsible Software Development Lifecycle**

A comprehensive framework specification for AI-driven software development that bridges the gap between business requirements and production code.

---

## Quick Navigation

<div class="grid cards" markdown>

-   📖 **Get Started**

    ---

    New to AirSDLC? Start here to understand the framework.

    [→ Read Overview](overview.md)

-   🧠 **Core Philosophy**

    ---

    Learn the foundational AI-DLC principles behind AirSDLC.

    [→ Explore Philosophy](philosophy.md)

-   🔄 **Three Phases**

    ---

    Understand Inception, Design, and Construction phases.

    [→ View Lifecycle](lifecycle.md)

-   📄 **Artifacts**

    ---

    Complete specifications for DAA, ADR, TIP, RFC, and more.

    [→ Browse Artifacts](artifacts.md)

-   ✅ **Workflow Guide**

    ---

    Step-by-step process from PRD to production.

    [→ Follow Workflow](workflow.md)

-   ⚡ **Operations**

    ---

    Context-aware incident response and monitoring.

    [→ Learn Operations](operations.md)

-   🧩 **Extensibility**

    ---

    Adapt the framework to your team's unique needs.

    [→ Extend Framework](extensibility.md)

-   💻 **Examples**

    ---

    See real-world artifacts in action.

    [→ View Examples](examples/README.md)

</div>

---

## What is AirSDLC?

AirSDLC (AI-Responsible Software Development Lifecycle) is **not a tool**—it is a **framework specification** that provides:

- **Core Principles**: AI-driven execution with human oversight
- **Artifact Definitions**: Structured specifications for all lifecycle artifacts
- **Lifecycle Phases**: Sequential knowledge handoff from business to code
- **Workflow Patterns**: Repeatable, validated processes
- **Extension Guidelines**: Adapt to your team's needs

---

## The Three Phases

```mermaid
graph LR
    A[Business Intent] --> B(Inception)
    B --> C[AI-DAA]
    C --> D(Design)
    D --> E[AI-ADR]
    E --> F(Construction)
    F --> G[Production Code]
```

1. **Inception** (The "WHAT") - Business intent → Domain model
2. **Design** (The "HOW") - Domain model → Architecture decisions
3. **Construction** (The "BUILD & RUN") - Decisions → Production code

---

## Framework vs. Implementation

!!! info "Important Distinction"
    - **AirSDLC** (this repository): The framework specification
    - **Implementation Tools**: Concrete software that implements the framework

This separation ensures multiple tools can implement the same standard.

!!! note "Framework Origins"
    AirSDLC is an open-source implementation of the [AI-Driven Development Lifecycle (AI-DLC)](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/) framework developed by Amazon Web Services. See [AI-DLC Attribution](AI-DLC-ATTRIBUTION.md) for details.

---

## For Different Audiences

=== "Teams Adopting AirSDLC"

    **Getting Started**:
    
    1. Read [Overview](overview.md) for the big picture
    2. Study [Philosophy](philosophy.md) for core principles
    3. Follow [Workflow](workflow.md) for practical guidance
    4. Explore [Examples](examples/README.md) to see artifacts

    **Best Practices**:
    
    - Start with Lightweight Workflow for simple features
    - Use Full Workflow for complex/high-risk features
    - Maintain Knowledge Repository as single source of truth
    - Validate AI outputs at every phase

=== "Tool Implementers"

    **Implementation Guide**:
    
    1. Review [Artifacts](artifacts.md) for data structures
    2. Study [Lifecycle](lifecycle.md) for phase requirements
    3. Check [Extensibility](extensibility.md) for conformance criteria
    4. Build tools that implement this specification

    **Conformance Requirements**:
    
    - ✅ Implement core artifacts (PRD, DAA/TIP, ADR)
    - ✅ Support sequential phases with validation gates
    - ✅ Maintain traceability chain
    - ✅ Store artifacts in Knowledge Repository

=== "Architects & Leads"

    **Strategic Adoption**:
    
    - Evaluate [Philosophy](philosophy.md) against team values
    - Customize [Extensibility](extensibility.md) for your context
    - Build [Architectural Playbook](artifacts.md#6-architectural-playbook)
    - Define workflow paths for your complexity matrix

    **Key Decisions**:
    
    - Full vs Lightweight workflow thresholds
    - Custom artifact types for compliance
    - Validation ceremony cadence
    - Tool selection criteria

---

## Ready to Start?

<div class="grid" markdown>

<a href="overview/" class="md-button md-button--primary" style="margin: 0.5rem;">
  🚀 Get Started
</a>

<a href="https://github.com/airsstack/airsdlc" class="md-button" style="margin: 0.5rem;">
  📦 View on GitHub
</a>

</div>

---

<div style="text-align: center; margin-top: 2rem; color: #666;">
  Built with ❤️ by the AirsStack community | 
  <a href="https://github.com/airsstack">GitHub</a>
</div>
