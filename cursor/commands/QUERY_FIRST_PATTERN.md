# Query-First/Confirm Flow Pattern

This document defines the standard pattern for all cursor commands to ensure no assumptions are made without engineer confirmation.

---

## Core Principles

1. **Query First**: Always ask questions before making architectural decisions
2. **Present Options**: Offer 2-4 options with tradeoffs
3. **Recommend Defaults**: Clearly mark best practices as "RECOMMENDED" but don't assume
4. **Wait for Confirmation**: Never proceed without explicit approval
5. **Make Assumptions Explicit**: If you must assume something, state it clearly and confirm

---

## Standard Flow Template

### Phase 1: Discovery (Information Gathering)

```markdown
## DISCOVERY PHASE

Ask ONE question at a time. Wait for answer before asking next question.

**Question 1**: [First contextual question]
- Option A: [description]
- Option B: [description]
- Other: [allow custom input]

🛑 WAIT FOR ANSWER

**Question 2**: [Second question, informed by answer to Q1]
...

🛑 WAIT FOR ANSWER
```

### Phase 2: Options Presentation (Decision Points)

```markdown
## DECISION REQUIRED: [Decision Name]

**Context**: [Why this decision matters]

**Options**:

**Option A - [Name]** (RECOMMENDED)
- Pros: [list benefits]
- Cons: [list drawbacks]
- Best for: [use cases]
- Effort: [time/complexity estimate]

**Option B - [Name]**
- Pros: [list benefits]
- Cons: [list drawbacks]
- Best for: [use cases]
- Effort: [time/complexity estimate]

**Option C - [Name]**
- Pros: [list benefits]
- Cons: [list drawbacks]
- Best for: [use cases]
- Effort: [time/complexity estimate]

**DEFAULT**: Option A (RECOMMENDED)
**REASONING**: [Why this is recommended - cite best practices, industry standards]

**Question**: "Which option do you prefer? (A/B/C or describe alternative)"

🛑 WAIT FOR CONFIRMATION - Do not proceed without explicit choice
```

### Phase 3: Confirmation Gate

```markdown
## 🛑 APPROVAL CHECKPOINT

Based on your answers, here's what I will implement:

**Decisions Made**:
1. [Decision 1]: [Chosen option] - [Brief rationale]
2. [Decision 2]: [Chosen option] - [Brief rationale]
3. [Decision 3]: [Chosen option] - [Brief rationale]

**Implications**:
- [Implication 1]
- [Implication 2]
- [Points of no easy return]

**Confirm**: "Does this approach align with your vision? (Yes/No/Modify)"

🛑 WAIT FOR APPROVAL - Only proceed after explicit "Yes"

If "Modify": Return to relevant decision point and re-query
If "No": Ask which decisions to reconsider
```

### Phase 4: Implementation

```markdown
## IMPLEMENTATION

[Only reached after approval]

Now implementing based on confirmed decisions...
```

---

## Anti-Patterns to Avoid

### ❌ BAD: Assumption Without Confirmation
```markdown
If technology stack not yet chosen, ask clarifying questions:
- What are the performance requirements?
- Python preferred when: API-heavy, data processing...

[Then immediately provides Python architecture]
```

### ✅ GOOD: Query-First Pattern
```markdown
**Question 1**: "What are your performance and scalability requirements?"
- High performance (< 100ms response): [implications]
- Standard performance (< 500ms response): [implications]
- Batch processing (minutes acceptable): [implications]

🛑 WAIT FOR ANSWER

**Question 2**: "What is your team's existing expertise?"
- Python: [implications]
- Node.js: [implications]
- Java: [implications]
- Go: [implications]

🛑 WAIT FOR ANSWER

## DECISION REQUIRED: Technology Stack

Based on your answers:
- Performance needs: [their answer]
- Team expertise: [their answer]

**RECOMMENDED**: Python with FastAPI
- Pros: Matches team expertise, excellent for API development, rich ecosystem
- Cons: Not as fast as Go, GIL limits multi-threading
- Best for: Your stated requirements

**Alternative**: Node.js with Express
- Pros: Faster for I/O-heavy workloads, JavaScript full-stack
- Cons: Team less familiar, callback complexity

**Question**: "Which stack do you prefer?"

🛑 WAIT FOR CONFIRMATION
```

---

## Anti-Pattern Examples with Fixes

### Example 1: Multi-Tenancy Architecture

#### ❌ BAD (Current Pattern)
```markdown
- Multi-Tenant and White-Label Architecture (if applicable):
  Ask user if application needs to support:
  - Multiple tenants
  - White-labeling

  If YES to any, include in architecture:
  - Tenant identification strategy (subdomain, path, header, JWT claim)
  - Data isolation model:
    - Shared schema with tenant_id (simple, less isolation)
    - Schema per tenant (moderate isolation)
    - Database per tenant (maximum isolation)
  [Lists implementation details before getting confirmation]
```

#### ✅ GOOD (Query-First Pattern)
```markdown
## DECISION REQUIRED: Multi-Tenancy Support

**Question 1**: "Does your application need to support multiple tenants (organizations/customers sharing same deployment)?"
- Yes, multiple tenants needed
- No, single tenant only
- Unsure (describe your use case)

🛑 WAIT FOR ANSWER

[If YES:]

**Question 2**: "What level of data isolation do you require?"
- Strong isolation (separate database per tenant) - Regulatory/compliance requirements
- Moderate isolation (separate schema per tenant) - Security preference
- Basic isolation (shared schema with tenant_id) - Cost optimization priority

🛑 WAIT FOR ANSWER

**Question 3**: "Do you need white-labeling (custom branding per tenant)?"
- Yes, full white-label support
- Partial (some customization)
- No, single brand

🛑 WAIT FOR ANSWER

## DECISION SUMMARY: Multi-Tenancy Model

Based on your answers, I recommend:

**Data Isolation**: [Their choice]
**White-Label**: [Their choice]

**Recommended Tenant Identification**: JWT claim + subdomain
- Pros: Secure, scalable, supports white-labeling
- Cons: DNS management complexity
- Implementation effort: Medium (3-5 days)

**Alternative**: Path-based (/tenant/{id}/...)
- Pros: Simpler DNS, easier testing
- Cons: Less SEO-friendly, no automatic white-labeling
- Implementation effort: Low (1-2 days)

**Confirm**: "Proceed with JWT claim + subdomain approach?"

🛑 WAIT FOR APPROVAL
```

### Example 2: Dependency Selection

#### ❌ BAD (Current Pattern)
```markdown
- Dependency Selection and Risk Assessment:
  For each major dependency, evaluate:
  - Health indicators (last release, stars, issues)
  - RED FLAGS (ask user before proceeding):
    - No updates in >1 year
    - Major unpatched security vulnerabilities
  [Lists criteria but then proceeds to recommend specific tools]
```

#### ✅ GOOD (Query-First Pattern)
```markdown
## DECISION REQUIRED: [Dependency Name]

**Context**: We need to choose [dependency type] for [purpose]

**Options Evaluated**:

**Option A - [Library 1]** (RECOMMENDED)
- Last updated: [date]
- Health: [stars, active maintainers, recent releases]
- Pros: [specific benefits]
- Cons: [specific drawbacks]
- Risk level: Low

**Option B - [Library 2]**
- Last updated: [date]
- Health: [stats]
- Pros: [benefits]
- Cons: [drawbacks]
- Risk level: Medium
- ⚠️ Warning: [specific concern]

**Option C - [Library 3]**
- Last updated: [date]
- Health: [stats]
- Pros: [benefits]
- Cons: [drawbacks]
- Risk level: High
- 🚨 Red flags: [major concerns]

**DEFAULT RECOMMENDATION**: Option A - [Library 1]
**REASONING**: Active maintenance, strong community, proven stability

**Question**: "Which dependency do you prefer? (A/B/C or suggest alternative)"

🛑 WAIT FOR CONFIRMATION

**Fallback Plan**: If [Library 1] becomes unmaintained, we can migrate to [Library 2] with estimated effort of [X days]
```

### Example 3: IaC Tool Selection

#### ❌ BAD (Current Pattern)
```markdown
**IaC Discovery**:
- Search for existing IaC files
- If found: "Should I use existing tool?"
- PREFER: Using existing tool for consistency
[Assumes preference without understanding constraints]
```

#### ✅ GOOD (Query-First Pattern)
```markdown
## IaC TOOL SELECTION

**Automatic Discovery**:
[Running discovery commands...]

**Found**: Terraform files in ./infrastructure/

**Question 1**: "I found existing Terraform setup. Are there any constraints I should know about?"
- Continue with Terraform (maintain consistency)
- Switch to different tool because: [user explains]
- Terraform is fine but has issues: [user explains]

🛑 WAIT FOR ANSWER

[If "Continue with Terraform":]

**RECOMMENDED**: Continue with Terraform
- Pros: Consistency with existing infrastructure, team familiar, state already managed
- Cons: None identified
- Migration effort: 0 (no change)

**Confirm**: "Proceed with Terraform?"

🛑 WAIT FOR APPROVAL

[If "Switch to different tool":]

**Question 2**: "What tool would you prefer and why?"
[Gather requirements, then present options]
```

---

## Decision Gate Checklist

Before proceeding with implementation, ensure:

- [ ] All discovery questions asked and answered
- [ ] Options presented with pros/cons/effort estimates
- [ ] Best practice marked as "RECOMMENDED" with clear reasoning
- [ ] User made explicit choice (not assumed)
- [ ] Implications and "points of no return" explained
- [ ] Final approval obtained with 🛑 gate
- [ ] Decision recorded in 09_DECISIONS.md

---

## When to Use Defaults

Defaults are acceptable for:

✅ **Non-critical technical details**:
- Code formatting (4 spaces, single quotes) - can be changed easily
- Linter configuration - can be reconfigured
- Folder structure - can be refactored

✅ **Industry-standard best practices**:
- Git branching strategy (main/feature branches)
- Commit message format (conventional commits)
- Security baselines (HTTPS, encryption at rest)
- **BUT**: Still present as "RECOMMENDED" with option to customize

❌ **Never default without confirmation**:
- Architecture patterns (monolith vs microservices)
- Database choice (SQL vs NoSQL)
- Cloud provider or major services
- Multi-tenancy model
- API design (REST vs GraphQL vs gRPC)
- Authentication strategy
- Technology stack
- Data modeling approach

---

## Template for "RECOMMENDED" Labels

When presenting recommendations:

```markdown
**[Option Name]** (RECOMMENDED)
- **Why recommended**: [Cite best practice, industry standard, or specific advantage]
- **Best for**: [Your specific requirements as stated]
- **Reference**: [Link to official docs, case studies, or standards]
- **Pros**: [Benefits]
- **Cons**: [Tradeoffs - be honest]
- **Fallback**: [What to do if this doesn't work out]
```

Example:
```markdown
**PostgreSQL** (RECOMMENDED)
- **Why recommended**: ACID compliance, proven reliability, excellent for relational data with complex queries
- **Best for**: Your stated requirements (transactional data, reporting, data integrity)
- **Reference**: https://www.postgresql.org/docs/ (industry-standard open-source RDBMS)
- **Pros**: Strong consistency, rich feature set, mature ecosystem, free
- **Cons**: Vertical scaling limits (consider sharding for >1TB), more complex than NoSQL for simple K/V
- **Fallback**: If scaling needs exceed PostgreSQL, migrate to distributed SQL (CockroachDB, YugabyteDB)
```

---

## Summary

**The Golden Rule**:
> Present options. Recommend defaults. Never assume. Always confirm.

Every architectural decision should follow:
1. **Query** → 2. **Present Options** → 3. **Recommend** → 4. **Wait for Confirmation** → 5. **Implement**
