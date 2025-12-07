# AI-DLC Attribution and Sources

## Official Amazon AI-DLC Framework

The AI-Driven Development Lifecycle (AI-DLC) is an official framework developed by Amazon Web Services (AWS) and introduced by Raja SP, Principal Solutions Architect at AWS.

### Official Sources

1. **AWS Blog Post**: [AI-Driven Development Life Cycle: Reimagining Software Engineering](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)
   - Published: July 31, 2025
   - Author: Raja SP, Principal Solutions Architect, AWS

2. **AI-DLC White Paper**: https://prod.d13rzhkk8cj2z0.amplifyapp.com/
   - Comprehensive methodology documentation
   - Detailed terminology and rituals

### Key AI-DLC Concepts (from AWS)

**Core Philosophy:**
- **AI-Powered Execution with Human Oversight**: AI creates plans, seeks clarification, and defers critical decisions to humans
- **Dynamic Team Collaboration**: Teams unite in collaborative spaces for real-time problem-solving

**Three Phases:**
1. **Inception Phase**: Transform business intent into detailed requirements through "Mob Elaboration"
2. **Construction Phase**: AI proposes logical architecture, domain models, code, and tests through "Mob Construction"
3. **Operations Phase**: AI manages infrastructure as code and deployments with team oversight

**New Terminology:**
- **Bolts**: Replace "sprints" - shorter work cycles measured in hours/days instead of weeks
- **Units of Work (UoW)**: Replace "Epics" - primary containers for functionality
- **Mob Elaboration**: Cross-functional team validates AI's questions and proposals (Inception)
- **Mob Construction**: Technical team provides clarification on architectural choices (Construction)

**Core Mental Model:**
```
AI proposes → AI clarifies → Human validates → AI implements
(This cycle repeats rapidly for every SDLC activity)
```

### How AirSDLC Relates to AI-DLC

**AirSDLC** is a **practical, open-source implementation** of the AWS AI-DLC methodology, adapted and extended with:

1. **Domain-Driven Design (DDD)**: AirSDLC adopts DDD as the core modeling language for domain analysis
2. **Specific Artifact Definitions**: Concrete specifications for DAA, ADR, TIP, RFC, and Playbook
3. **Technology Agnosticism**: Emphasis on 100% technology-neutral domain modeling
4. **Extensibility Framework**: Well-defined extension points for customization
5. **Lightweight Workflow Option**: Pragmatic path for simple features (TIP-based workflow)

**Attribution:**
- AI-DLC: © Amazon Web Services, Inc.
- AirSDLC: Open-source framework implementing AI-DLC principles

### References

- AWS Blog: https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/
- AI-DLC White Paper: https://prod.d13rzhkk8cj2z0.amplifyapp.com/
- Amazon Q Developer: https://aws.amazon.com/q/developer/
- Kiro: https://kiro.dev/

---

**Last Updated**: December 7, 2025
