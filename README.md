# AirSDLC: AI-Responsible Software Development Lifecycle

**A Practical Implementation of the AWS AI-DLC Framework**

📚 **[View Documentation](https://airsstack.github.io/airsdlc/)**

AirSDLC is an open-source framework specification that implements and extends the [AI-Driven Development Lifecycle (AI-DLC)](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/) methodology developed by Amazon Web Services. It provides a structured, AI-driven approach to software development, formalizing the principles, artifacts, and workflows that bridge the gap between business requirements and production code.

## 🎯 What is AirSDLC?

AirSDLC (AI-Responsible Software Development Lifecycle) is **not a tool**—it is a **framework specification** and **manifesto** that provides:

- **Core Principles**: AI-driven execution with human oversight
- **Artifact Definitions**: Structured specifications for DAA, ADR, TIP, RFC, and more
- **Lifecycle Phases**: From Inception through Design to Construction & Operations
- **Workflow Patterns**: Repeatable processes for feature development
- **Extension Guidelines**: How to adapt and extend the framework

This repository serves as the **canonical reference** for the AirSDLC framework. Implementation tools can use this specification to provide concrete development workflows.

## 🏗️ Core Philosophy

**AI-Driven Execution with Human Oversight**

In AirSDLC, AI is not merely an assistant—it is a central collaborator that generates plans, models domains, and proposes solutions. The human expert's role is elevated from artifact generator to **validator, curator, and strategic decision-maker**.

The framework is built on sequential knowledge handoff across three phases:
1. **Inception** - The "WHAT": Business intent → Domain model
2. **Design** - The "HOW": Domain model → Architecture decisions  
3. **Construction & Operations** - The "BUILD & RUN": Decisions → Production code

## 📚 Documentation Structure

**[📖 Read the full documentation site](https://airsstack.github.io/airsdlc/)**

The framework is documented in modular, focused chapters:

### Core Documentation
- **[Overview](docs/overview.md)** - Introduction and key concepts
- **[Philosophy](docs/philosophy.md)** - Core principles and AI-DLC foundation
- **[Lifecycle](docs/lifecycle.md)** - The three phases explained
- **[Artifacts](docs/artifacts.md)** - DAA, ADR, TIP, RFC specifications
- **[Workflow](docs/workflow.md)** - Step-by-step process and rituals
- **[Operations](docs/operations.md)** - Post-deployment and incident handling
- **[Extensibility](docs/extensibility.md)** - How to adapt and extend

### Additional Resources
- **[Complete Reference](docs/core.md)** - Comprehensive single-document guide
- **[Examples](docs/examples/)** - Sample artifacts and use cases

## 🔧 For Tool Implementers

If you're building a tool that implements the AirSDLC framework:

1. Read the **[Overview](docs/overview.md)** to understand the big picture
2. Study the **[Artifacts](docs/artifacts.md)** to understand data structures
3. Review the **[Workflow](docs/workflow.md)** to implement the process
4. Consult **[Extensibility](docs/extensibility.md)** for extension points

**Conformance**: A tool is considered "AirSDLC-compliant" if it implements the core artifact types (DAA, ADR) and respects the phase-based workflow with human validation gates.

## 🌱 For Teams and Engineers

If you want to adopt AirSDLC practices:

1. Start with **[Philosophy](docs/philosophy.md)** to understand the "why"
2. Read **[Workflow](docs/workflow.md)** for practical step-by-step guidance
3. Explore **[Examples](docs/examples/)** to see what good artifacts look like
4. Choose or build a tool that implements this framework

## 🤝 Contributing

AirSDLC is an evolving framework. Contributions to improve clarity, add examples, or refine concepts are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

## 📄 License

[Add your license here]

## 🔗 References

- **AWS AI-DLC**: [Official Blog Post](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/) | [White Paper](https://prod.d13rzhkk8cj2z0.amplifyapp.com/)
- **AI-DLC Attribution**: See [AI-DLC-ATTRIBUTION.md](docs/AI-DLC-ATTRIBUTION.md) for detailed information about the original AWS framework

---

**Status**: This framework is actively being documented and refined. Feedback and contributions are encouraged.
