# AirSDLC Examples

This directory contains practical examples of AirSDLC artifacts. These examples are based on a fictional "Booking Management" feature for a reservation application.

## Example Feature: Booking Cancellation

We'll walk through a complete feature implementation from PRD to deployment, demonstrating all core artifacts.

### Scenario

**Business Need**: Users need the ability to cancel their bookings for various reasons (e.g., change of plans, double booking, user-initiated cancellation).

### Artifacts in this Flow

1. **[example-prd.md](example-prd.md)** - Product Requirements Document for booking cancellation
2. **[example-daa.md](example-daa.md)** - Domain Architecture Analysis using DDD patterns (AI-generated)
3. **[example-tip.md](example-tip.md)** - Technical Implementation Proposal (lightweight workflow alternative)
4. **[example-rfc.md](example-rfc.md)** - Request for Comments with design discussion
5. **[example-adr.md](example-adr.md)** - Architectural Decision Record (finalized design)
6. **[example-playbook-entry.md](example-playbook-entry.md)** - Transactional Outbox Pattern (reusable)

### Traceability Chain (Full Workflow)

```
example-prd.md (Business Requirements)
     ↓
example-daa.md (Domain Model using DDD)
     ↓
example-rfc.md (Design Discussion)
     ↓
example-adr.md (Finalized Architecture)
     ↓
Bolts → Code → Deployment
```

### Alternative Path (Lightweight Workflow)

For simpler features, skip the DAA:

```
example-prd.md (Business Requirements)
     ↓
example-tip.md (Technical Proposal)
     ↓
example-rfc.md (Design Discussion)
     ↓
example-adr.md (Finalized Architecture)
     ↓
Bolts → Code → Deployment
```

## Understanding the Examples

These examples walk through a complete booking cancellation feature from start to finish.

### Full Workflow Path
1. **[example-prd.md](example-prd.md)**: Business stakeholder defines need for booking cancellation
2. **[example-daa.md](example-daa.md)**: AI analyzes domain, identifies `Booking` aggregate, `cancel()` operation, and invariants
3. **[example-rfc.md](example-rfc.md)**: Engineer proposes technical approach with outbox pattern for reliability
4. **[example-adr.md](example-adr.md)**: Team finalizes design after collaborative discussion with AI
5. **Bolts**: Feature broken into discrete tasks (API endpoint, worker, monitoring)

### Key Learning Points
- **Start with the PRD** to understand business requirements and success metrics
- **Notice how the DAA** remains technology-agnostic (no database, framework, or language mentioned)
- **See the RFC** capture open questions and initial technical thinking
- **The ADR becomes concrete** with PostgreSQL, Go, Kafka, specific tables and endpoints
- **Each artifact builds on and traces back to** the previous one
- **The Playbook** provides reusable patterns that inform multiple designs

### How to Use These Examples
- **For Learning**: Read the examples in order (PRD → DAA → RFC → ADR)
- **For Your Team**: Use these as templates and adapt to your domain
- **For Tool Builders**: Use as test fixtures for validation logic

## Additional Resources

For more context on each artifact type:

- **[artifacts.md](../artifacts.md)** - Complete specifications for all artifact types
- **[workflow.md](../workflow.md)** - Step-by-step process guide with additional examples
- **[lifecycle.md](../lifecycle.md)** - Phase-by-phase breakdown of the framework
- **[operations.md](../operations.md)** - Post-deployment incident response examples

---

**Note**: The booking cancellation examples are fictional and simplified for educational purposes. Real-world implementations would include more detail specific to your domain, tech stack, and team context.
