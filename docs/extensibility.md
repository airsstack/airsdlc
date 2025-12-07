# AirSDLC Extensibility

## Overview

The AirSDLC is designed as an **extensible framework**, not a rigid prescription. While it provides opinionated defaults based on proven practices, it recognizes that different teams, domains, and organizations have unique needs.

This document describes how to adapt and extend the framework while maintaining its core principles and benefits.

## Core vs. Extensions

### The Immutable Core

These aspects of AirSDLC should NOT be changed, as they define the framework's identity:

| Core Principle | Why Immutable |
|----------------|---------------|
| **AI-Driven Execution with Human Oversight** | This is the foundational paradigm |
| **Sequential Knowledge Handoff** | Ensures traceability from business to code |
| **Validation Gates** | Human validation is mandatory for quality |
| **Knowledge Repository** | Single source of truth is essential |
| **Phase Structure** | Inception → Design → Construction flow |

### Extension Points

These aspects CAN and SHOULD be adapted to your context:

| Extension Point | Examples |
|-----------------|----------|
| **Artifact Types** | Add custom artifacts (e.g., Security Review, Compliance Checklist) |
| **Artifact Templates** | Customize section names, add company-specific fields |
| **Workflow Ceremonies** | Adjust team review cadence, add approval steps |
| **Tool Integration** | Integrate with Jira, Linear, GitHub, Slack, etc. |
| **Playbook Patterns** | Add domain-specific or company-specific patterns |
| **DDD Interpretation** | Adapt tactical patterns to your domain complexity |

---

## Extension Mechanism 1: Custom Artifacts

### When to Add a Custom Artifact

Add a new artifact type when:
- Your organization has unique compliance requirements (e.g., SOC2 audit trail)
- Your domain has specialized documentation needs (e.g., API Gateway configs for microservices)
- Your team has proven that a specific artifact improves quality or speed

**Example**: A hospitality company might add a "Regulatory Impact Assessment" artifact.

### How to Define a Custom Artifact

Follow the same structure as core artifacts:

#### 1. Define Metadata
- **Artifact Name**: Regulatory Impact Assessment (RIA)
- **Acronym**: RIA
- **Phase**: Design (between RFC and ADR)
- **Generator**: Compliance Officer (with AI assistance)
- **Validator**: Legal team
- **Mandatory or Optional**: Mandatory for features touching PII or booking transactions

#### 2. Define Purpose
**Purpose**: Assess regulatory compliance implications of a proposed design before finalization.

#### 3. Define Structure
```markdown
## RIA Template

### 1. Feature Summary
- Feature Name
- ADR Reference
- Business Justification

### 2. Data Classification
- [ ] Handles Personally Identifiable Information (PII)
- [ ] Handles booking data (reservations, guest information)
- [ ] Handles authentication credentials

### 3. Regulatory Frameworks
Which regulations apply?
- [ ] GDPR (EU data protection)
- [ ] PCI-DSS (payment card security if applicable)
- [ ] SOC2 (security controls)
- [ ] CCPA (California privacy)

### 4. Compliance Requirements
For each applicable framework:
- **Requirement**: Right to be forgotten (GDPR Article 17)
- **Implementation**: User data deletion API in ADR-023
- **Evidence**: Link to code implementing deletion

### 5. Risk Assessment
| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Data breach | Low | High | Encryption at rest and in transit |
| Unauthorized access | Medium | High | Role-based access control |

### 6. Approval
- [ ] Compliance Officer sign-off
- [ ] Legal team review
- [ ] Date: ___________
```

#### 4. Integrate into Workflow
Update your team's workflow to include RIA generation:

```
Phase 2: Design
  ↓
Step 8: Generate AI-ADR (existing)
  ↓
**Step 8.5: Generate RIA** (NEW)
  - Compliance Officer reviews ADR
  - AI assists in identifying regulated data
  - Legal team validates compliance
  ↓
Step 9: Finalize ADR (existing, now conditional on RIA approval)
```

#### 5. Update Traceability Chain
```
PRD → DAA → RFC → ADR → **RIA** → Bolt → Code
```

---

## Extension Mechanism 2: Custom Templates

### Customizing Existing Artifacts

All AirSDLC artifacts can be customized by:
1. **Adding sections**: Insert company-specific fields
2. **Removing sections**: Skip fields that don't apply (with caution)
3. **Reordering sections**: Prioritize what matters to your team

### Example: Custom ADR Template for Microservices

Standard ADR:
```markdown
# ADR-XXX: Title

## 1. Metadata
## 2. Context
## 3. Decision
## 4. Rationale
## 5. Implementation Details
## 6. Edge Cases
## 7. Monitoring
## 8. Rejected Alternatives
```

Custom ADR for microservices-heavy architecture:
```markdown
# ADR-XXX: Title

## 1. Metadata
## 2. Context
## 3. Service Boundaries  <-- NEW: Which services are affected?
## 4. Decision
## 5. Rationale
## 6. API Contracts  <-- NEW: Inter-service communication
## 7. Implementation Details
## 8. Service Mesh Configuration  <-- NEW: Istio/Linkerd configs
## 9. Edge Cases
## 10. Monitoring & Distributed Tracing  <-- MODIFIED
## 11. Rejected Alternatives
```

### Template Versioning

As your team evolves, template needs change. Use versioning:
- `adr-template-v1.0.md`: Original
- `adr-template-v2.0.md`: Added Service Boundaries section
- `adr-template-v3.0.md`: Added security review checklist

**Migration Strategy**:
- New ADRs use latest template
- Existing ADRs are NOT retrofitted (too costly)
- Major ADRs can be updated during significant amendments

---

## Extension Mechanism 3: Custom Playbook Patterns

### Domain-Specific Patterns

The Architectural Playbook should grow with your team's experience. Add patterns that solve YOUR recurring problems.

**Example Categories**:
- **E-commerce**: Cart abandonment recovery, inventory synchronization
- **Hospitality**: Idempotent booking processing, reservation consistency
- **Healthcare**: HIPAA-compliant logging, patient data anonymization
- **SaaS**: Multi-tenant data isolation, usage-based billing

### How to Add a Playbook Entry

Follow the standard Playbook structure (see [Artifacts](artifacts.md#6-architectural-playbook)):

```markdown
## PLAYBOOK-042: Idempotent Booking Processing

### Problem
How do we prevent duplicate bookings when a client retries a booking API call?

### Context
**Use when**:
- Handling reservation transactions
- Client may retry on network timeout
- Consequences of duplication are severe (overbooking, guest dissatisfaction)

**Do NOT use when**:
- Operations are naturally idempotent (e.g., GET requests)
- Duplicate processing is acceptable

### Solution
Use idempotency keys:
1. Client generates unique key (UUID) and sends with request
2. Server stores key in database with transaction result
3. On retry, server checks if key exists
4. If exists, return cached result (do not re-process)
5. Keys expire after 24 hours

### Implementation Checklist
- [ ] Database table: `idempotency_keys(key, result, created_at)`
- [ ] API requires `Idempotency-Key` header
- [ ] Server returns `409 Conflict` if result differs from cached
- [ ] Background job cleans up keys > 24 hours old
- [ ] Monitoring: Track duplicate key hits

### Trade-offs
**Pros**:
- ✅ Prevents duplicate bookings
- ✅ Client can safely retry

**Cons**:
- ❌ Adds database dependency
- ❌ Requires client cooperation (must send key)
- ❌ Storage cost for key cache

### Example
ADR-087: Booking Processing API
```

### Playbook Governance

As the Playbook grows:
- **Review Regularly**: Quarterly architecture review
- **Deprecate Obsolete Patterns**: Mark as "Deprecated" if technology changes
- **Version Patterns**: If a pattern evolves significantly, create a new version
- **Cross-Reference**: Link related patterns (e.g., "See also: Circuit Breaker Pattern")

---

## Extension Mechanism 4: Workflow Customization

### Adjusting Ceremony Cadence

The framework suggests specific validation rituals (Mob Elaboration, Mob Construction). Adapt the frequency and format to your team size and maturity:

| Team Size | Suggested Ceremony |
|-----------|-------------------|
| **1-3 engineers** | Async review (PR comments, Slack threads) |
| **4-10 engineers** | Weekly design review meeting (1 hour) |
| **10+ engineers** | Dedicated design review sessions per feature |

### Adding Approval Gates

Some organizations require additional sign-offs:

**Example: Enterprise Approval Workflow**
```
Standard AirSDLC:
  RFC → Collaborative Design → AI-ADR → Construction

Enterprise Extension:
  RFC → Collaborative Design → AI-ADR 
    → **Architecture Board Review** (NEW)
    → **Security Team Approval** (NEW)
    → **Budget Approval** (NEW)
    → Construction
```

**Implementation**:
1. Add ADR status: `awaiting-approval`
2. Document required approvals in ADR metadata:
   ```markdown
   ## Approvals
   - [ ] Architecture Board: Pending (scheduled for 2024-01-20)
   - [ ] Security Team: Approved (2024-01-18, signed by @jane)
   - [ ] Budget: Pending (waiting for Q1 allocation)
   ```
3. ADR transitions to `accepted` only after all approvals

### Lightweight vs. Full Workflow Threshold

The framework provides a Decision Matrix for choosing workflow paths. Customize thresholds for your domain:

**Standard Thresholds**:
- Complex domain → Full Workflow
- Simple feature → Lightweight Workflow

**Custom Thresholds for a High-Compliance Hospitality System**:
- Touches PII or booking data → Full Workflow (always)
- Integrates with external property management system → Full Workflow
- Internal admin tool → Lightweight Workflow
- Performance optimization (no logic change) → Lightweight Workflow

Document your custom matrix:
```markdown
## Our Workflow Decision Matrix

| Feature Characteristic | Workflow |
|------------------------|----------|
| Handles bookings/PII | Full (AI-DAA required) |
| Third-party integration | Full |
| Internal tools | Lightweight (TIP) |
| Bug fix (no new logic) | Lightweight (TIP) |
| Performance tuning | Lightweight (TIP) |
```

---

## Extension Mechanism 5: Tool Integration

The AirSDLC is tool-agnostic. Integrate with your existing toolchain:

### Project Management Integration

**Jira / Linear / Asana**:
- **PRD** → Epic in Jira
- **DAA/ADR** → Attached as PDF or link to git repo
- **Bolt** → Jira Story with link to ADR
- **Fix-It Bolt** → Jira Bug with link to incident

**Custom Fields**:
- `ADR Reference`: Link to ADR document
- `Bolt ID`: BOLT-042
- `Phase`: Inception, Design, Construction, Operations

### CI/CD Integration

**Automated Checks**:
- **On PR**: Check if code references a Bolt
- **On Merge**: Update Bolt status to "Completed"
- **On Deploy**: Create Deployment Unit artifact

**GitHub Actions Example**:
```yaml
name: Validate Bolt Reference
on: pull_request

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - name: Check PR has Bolt reference
        run: |
          if ! grep -q "BOLT-[0-9]" ${{ github.event.pull_request.body }}; then
            echo "Error: PR must reference a Bolt ID"
            exit 1
          fi
```

### Notification Integration

**Slack / Teams**:
- **ADR Approved** → Notify #engineering channel
- **Incident Declared** → Ping @oncall in #incidents
- **Post-Mortem Published** → Share in #learning channel

**Example Slack Integration**:
```bash
# Post ADR approval to Slack
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"🎉 ADR-023 (Booking Cancellation) has been approved! Ready for construction."}' \
  https://hooks.slack.com/services/YOUR/WEBHOOK/URL
```

### AI Tool Integration

**OpenAI API / Claude API**:
- Automate DAA generation from PRD
- Automate Coverage Report generation
- Automate Compliance Report generation

**Example Script**:
```python
import openai

def generate_daa(prd_text):
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{
            "role": "system",
            "content": "You are a DDD expert. Generate an AI-DAA from this PRD."
        }, {
            "role": "user",
            "content": prd_text
        }]
    )
    return response['choices'][0]['message']['content']

prd = open('docs/prds/booking-cancel.md').read()
daa = generate_daa(prd)
open('docs/daas/booking-cancel-daa.md', 'w').write(daa)
print("DAA generated: docs/daas/booking-cancel-daa.md")
```

---

## Extension Mechanism 6: State Machine Customization

The AirSDLC workflow can be modeled as a state machine. Extend the states and transitions for your needs.

### Standard State Machine (Simplified)

```
PRD → DAA (draft) → DAA (validated) → RFC (draft) → RFC (under_review) 
  → RFC (approved) → ADR (accepted) → Bolt (in_progress) → Bolt (done) 
  → Deployed
```

### Extended State Machine (Enterprise)

```
PRD → DAA (draft) → DAA (validated) → RFC (draft) → RFC (under_review) 
  → RFC (approved) → ADR (proposed) 
  → ADR (awaiting_security_review)  <-- NEW STATE
  → ADR (awaiting_budget_approval)  <-- NEW STATE
  → ADR (accepted) → Bolt (in_progress) → Bolt (done) 
  → Staging_Deployed  <-- NEW STATE
  → Production_Deployed
```

### Defining Custom Transitions

Define who can trigger each transition:

| Transition | Trigger | Required Action |
|------------|---------|-----------------|
| `DAA (draft) → DAA (validated)` | Architect | Review + approval comment |
| `RFC (approved) → ADR (proposed)` | AI + Engineer | Generate ADR from RFC |
| `ADR (proposed) → ADR (awaiting_security_review)` | Engineer | Submit to security team |
| `ADR (awaiting_security_review) → ADR (awaiting_budget_approval)` | Security Team | Approve + sign-off |
| `ADR (awaiting_budget_approval) → ADR (accepted)` | Finance/PM | Budget allocated |

**Implementation**:
Use git tags or metadata files to track state:
```bash
# Tag ADR as "awaiting security review"
git tag adr-023-awaiting-security-review
git push --tags
```

Or use YAML frontmatter:
```markdown
---
adr_id: ADR-023
title: Booking Cancellation Implementation
status: awaiting_security_review
approvals:
  - security: pending
  - budget: not_required
---

# ADR-023: Booking Cancellation Implementation
...
```

---

## Extension Mechanism 7: Metrics and Reporting

Extend the framework with custom metrics that matter to YOUR organization.

### Standard Metrics

AirSDLC inherently provides:
- **Traceability Coverage**: % of code with traceable Bolt → ADR → DAA → PRD
- **Lead Time**: Time from PRD to production deployment
- **Change Failure Rate**: % of deployments causing incidents

### Custom Metrics Examples

**For Product Teams**:
- **Feature ROI**: Business metric improvement per feature (from PRD success metrics)
- **Requirement Drift**: % of user stories that changed after DAA validation

**For Engineering Teams**:
- **ADR Reuse**: % of ADRs that reference existing Playbook patterns
- **Bolt Size**: Average effort per Bolt (target: < 2 days)
- **Code-to-ADR Divergence**: % of PRs that don't match their ADR

**For Operations Teams**:
- **Context-Assisted Incident Resolution**: % of incidents where AI correlation accelerated MTTR
- **Knowledge Repository Queries**: How often is the repo accessed during incidents?

### Reporting Dashboard

Build a dashboard that visualizes AirSDLC metrics:

**Example Sections**:
1. **Pipeline Health**:
   - PRDs in progress
   - DAAs awaiting validation
   - ADRs awaiting approval
   - Bolts in progress

2. **Quality Indicators**:
   - Traceability coverage
   - ADR compliance rate
   - Test coverage per Bolt

3. **Operational Excellence**:
   - MTTR trend
   - Incident recurrence rate
   - Post-mortem completion rate

**Implementation**:
- Parse git repo metadata (tags, file paths)
- Query project management tool APIs (Jira, Linear)
- Aggregate in dashboard tool (Grafana, Datadog, custom webapp)

---

## Extension Mechanism 8: Multi-Team Coordination

For organizations with multiple teams, extend the framework to coordinate dependencies.

### Cross-Team ADRs

When a feature spans multiple teams:

**Example**: A booking feature requires changes to:
- **Team A**: Booking Service (cancel bookings before checkout)
- **Team B**: Reservation Service (process cancellation)
- **Team C**: Notification Service (alert guest)

**Approach**:
1. **Primary ADR** (owned by Team B, the coordinating team):
   - ADR-050: Multi-Service Booking Flow
   - Describes overall architecture

2. **Dependent ADRs** (owned by each team):
   - ADR-051: Booking Cancellation Integration (Team A)
   - ADR-052: Reservation Processor Integration (Team B)
   - ADR-053: Booking Notification Trigger (Team C)

3. **Cross-Reference**:
   Each dependent ADR references the primary ADR:
   ```markdown
   ## Context
   This ADR implements the Booking Service portion of ADR-050 (Multi-Service Booking Flow).
   
   **Dependencies**:
   - ADR-052 must complete before integration testing
   - ADR-053 consumes events published by this service
   ```

### Shared Playbook

Multiple teams can contribute to a shared Architectural Playbook:
- **Governance**: Architecture guild reviews and approves new entries
- **Ownership**: Each pattern has a designated owner/team
- **Versioning**: Use semantic versioning for breaking changes

---

## Conformance Criteria

To be considered "AirSDLC-compliant," a tool or process MUST:

### Mandatory Requirements

1. **✅ Implement Core Artifacts**:
   - PRD or equivalent requirement document
   - DAA or TIP (domain model or technical proposal)
   - ADR (architectural decision record)

2. **✅ Support Sequential Phases**:
   - Phase 1: Requirements → Domain/Technical Understanding
   - Phase 2: Design with human validation
   - Phase 3: Construction with traceability

3. **✅ Enforce Validation Gates**:
   - Human must approve artifacts before phase transitions
   - AI-generated artifacts cannot auto-proceed without review

4. **✅ Maintain Traceability**:
   - Code must be traceable to ADR
   - ADR must be traceable to DAA/TIP
   - DAA/TIP must be traceable to PRD

5. **✅ Store in Knowledge Repository**:
   - All artifacts are persisted and queryable
   - Repository is the single source of truth

### Optional Extensions (Recommended but not required)

- Custom artifact types
- Custom workflow ceremonies
- Tool integrations
- Advanced metrics and reporting

---

## Adoption Strategy: Start Small, Extend Gradually

### Phase 1: Pilot (Weeks 1-4)
- Choose 1-2 features
- Use core artifacts only (PRD, DAA, ADR)
- Manual workflow (no tooling yet)
- Gather feedback

### Phase 2: Refine (Weeks 5-8)
- Customize templates based on pilot learnings
- Add 3-5 Playbook entries
- Introduce TIP for simple features (Lightweight Workflow)
- Formalize validation process

### Phase 3: Scale (Weeks 9-16)
- Roll out to all teams
- Build tool integrations (Jira, CI/CD, Slack)
- Add custom artifacts if needed
- Establish metrics and reporting

### Phase 4: Mature (Month 4+)
- Continuous Playbook growth
- AI-assisted generation becomes standard
- Knowledge Repository enables powerful queries
- Operational excellence (context-aware incidents)

---

## Summary

AirSDLC is a **framework, not a straitjacket**. Its power comes from:
1. **Immutable core principles** (AI-driven, human-validated, traceable)
2. **Flexible extension points** (artifacts, templates, workflows, tools)
3. **Pragmatic adoption** (start small, extend as you learn)

By respecting the core while customizing the extensions, teams can achieve both **consistency** (across teams) and **flexibility** (for unique needs).

---

**End of Core Documentation**

You've now completed the core AirSDLC framework documentation. For practical examples, see [Examples](examples/README.md).
