# Query-First Migration Summary

This document summarizes the migration of cursor commands from an assumption-based pattern to a query-first/confirm flow.

**Migration Date**: 2025-12-31

---

## Core Principle

> **Present options. Recommend defaults. Never assume. Always confirm.**

Every architectural decision now follows:
1. **Query** → 2. **Present Options** → 3. **Recommend** → 4. **Wait for Confirmation** → 5. **Implement**

---

## Files Created

### 1. QUERY_FIRST_PATTERN.md
**Purpose**: Template and guidelines for all cursor commands

**Key Sections**:
- Core Principles (query first, present options, recommend defaults, wait for confirmation)
- Standard Flow Template (Discovery → Options Presentation → Confirmation Gate → Implementation)
- Anti-Patterns to Avoid (with before/after examples)
- Decision Gate Checklist
- When to Use Defaults
- Template for "RECOMMENDED" Labels

**Impact**: Provides reusable pattern for all current and future cursor commands

---

## Files Updated

### 2. full-app-02-architecture-tdd.md
**Changes Made**:

#### Before (Assumptions):
- Listed best practices and immediately moved to implementation
- Asked questions but then presented detailed solutions without waiting for answers
- Multi-tenancy section showed implementation details before confirming need
- Dependency selection had RED FLAGS but didn't require explicit confirmation
- Technology stack: mentioned questions but recommended Python without confirmation

#### After (Query-First):
- **Phase 1**: 3 discovery questions (performance, team expertise, infrastructure) with 🛑 WAIT gates
- **Phase 2**: Technology stack decision with 2-3 options, pros/cons, RECOMMENDED label, wait for confirmation
- **Phase 3**: Multi-tenancy decision - asks IF needed first, THEN presents data isolation options (3 choices with effort estimates)
- **Phase 4**: Dependency selection template - for EACH dependency, present evaluated options, RED FLAGS trigger explicit question
- **Phase 5**: Architecture design only AFTER all decisions confirmed
- **Final Approval Gate**: Summary of all decisions, points of no easy return, wait for approval

**Key Improvements**:
- 🛑 WAIT FOR ANSWER/CONFIRMATION gates at every decision point
- RECOMMENDED labels always include "Why recommended" with reference to requirements
- Options presented with honest pros/cons, effort estimates
- No implementation until final approval

---

### 3. data-lakehouse-builder.md
**Changes Made**:

#### Before (Assumptions):
- Immediately presented Medallion architecture as default
- Assumed star schema dimensional modeling
- Assumed Databricks platform
- Showed detailed implementation patterns before confirming approach
- Storage structure shown without asking about preferences

#### After (Query-First):
- **NEW: Architecture Decision Section** (added at top, before requirements gathering)
  - **Question 1**: Lakehouse architecture approach
    - Option A: Medallion (Bronze/Silver/Gold) - RECOMMENDED with pros/cons
    - Option B: Two-tier (Raw + Curated)
    - Option C: Custom
    - 🛑 WAIT FOR CONFIRMATION

  - **Question 2**: Data modeling approach
    - Option A: Dimensional Modeling (Star Schema) - RECOMMENDED for BI/reporting
    - Option B: Wide Tables (Denormalized)
    - Option C: Normalized (3NF) + Views
    - Option D: Data Vault
    - 🛑 WAIT FOR CONFIRMATION

  - **Question 3**: Platform and tooling
    - Option A: Databricks - RECOMMENDED with pros/cons/cost
    - Option B: Apache Spark + Delta Lake (Open Source)
    - Option C: Snowflake
    - Option D: AWS Glue + Athena + S3
    - 🛑 WAIT FOR CONFIRMATION

- **Storage Location Decision**:
  - Option A: ADLS Gen2 (Azure) - RECOMMENDED if Azure/Databricks
  - Option B: AWS S3
  - Option C: Google Cloud Storage
  - Wait for confirmation on storage structure customization

**Key Improvements**:
- Decisions made BEFORE detailed requirements gathering
- Templates adapt based on confirmed choices
- Each RECOMMENDED option includes reference links to official docs
- Implementation effort estimates for each option
- Explicit note that defaults can be customized

---

### 4. devops-change-builder.md
**Changes Made**:

#### Before (Assumptions):
- Production reference check found patterns and assumed use
- IaC discovery found tools and said "PREFER existing" without asking
- If manual selected, just recommended tool without options

#### After (Query-First):
- **Production Reference Check**:
  - Ask if similar production resources exist (Yes/No/Unsure)
  - 🛑 WAIT FOR ANSWER
  - If YES: Fetch production patterns, then present findings
  - **NEW DECISION**: "Should I match these production patterns or customize?"
    - Match (RECOMMENDED for consistency)
    - Customize (explain what/why)
  - 🛑 WAIT FOR CONFIRMATION
  - Document choice and reasoning

- **IaC Tool Selection**:
  - Auto-discover existing IaC files
  - **If found**:
    - Option A: Use existing [tool] - RECOMMENDED for consistency
    - Option B: Switch to different tool (user must explain why)
    - 🛑 WAIT FOR CONFIRMATION

  - **If not found**:
    - Option A: Terraform - RECOMMENDED for multi-cloud
    - Option B: Bicep - RECOMMENDED for Azure-only
    - Option C: CloudFormation (AWS-only)
    - Option D: Pulumi
    - Option E: Manual (NOT RECOMMENDED with reasoning)
    - 🛑 WAIT FOR CONFIRMATION

**Key Improvements**:
- No automatic preference application
- Every tool choice has reasoning (why recommended, best for, pros/cons)
- Manual option explicitly marked NOT RECOMMENDED with explanation
- Reference links to official docs for each tool

---

### 5. bug-workflow-builder-tdd.md
**Changes Made**:

#### Before (Assumptions):
- Immediately launched into detailed signal flow analysis
- Assumed root cause analysis was always the goal
- No consideration of urgency vs. thoroughness tradeoff

#### After (Query-First):
- **NEW: Bug Fix Approach Decision** (added after Phase 1 triage, before Phase 2 analysis)
  - **Question**: "What is your preferred approach for this bug?"

  - **Option A - Root Cause Analysis** (RECOMMENDED for most bugs)
    - Why recommended, best for, pros/cons
    - Time: 1-4 hours
    - Approach: Detailed signal flow tracing (existing Phase 2 content)

  - **Option B - Quick Symptom Fix** (for urgent hotfixes)
    - Best for: Critical/High severity production bugs
    - Time: 15-60 minutes
    - REQUIREMENT: Must create follow-up ticket for root cause
    - Ask where to apply fix (UI layer, API layer, data layer)

  - **Option C - Workaround Only** (for low-priority bugs)
    - Best for: Low severity, edge cases, deprecated features
    - Time: 5-15 minutes (documentation only)

  - 🛑 WAIT FOR CONFIRMATION

**Key Improvements**:
- Acknowledges different bug fix strategies based on urgency
- Explicit time estimates for each approach
- Option B requires follow-up ticket (prevents shortcuts from becoming permanent)
- Signal flow analysis clearly marked as for "Root Cause Analysis approach"
- Aligns fix strategy with business needs (fast mitigation vs. proper fix)

---

### 6. network-flow-builder.md
**Changes Made**:

#### Before (Assumptions):
- Asked if user has access, then immediately ran full network scan
- No consideration of discovery scope (could run 100+ commands)
- No choice between targeted vs. comprehensive discovery

#### After (Query-First):
- **NEW: Discovery Depth Decision**
  - **Question 4**: "How comprehensive should the network discovery be?"

  - **Option A - Targeted Discovery** (RECOMMENDED for specific issues)
    - Why recommended: Faster, focused
    - Scope: Only components related to Question 1 aspect
    - Time: 5-15 minutes
    - Commands: 10-20 commands

  - **Option B - Full Network Scan** (for complete documentation)
    - Best for: Initial setup, comprehensive audit, security review
    - Scope: All network components
    - Time: 15-45 minutes
    - Commands: 50-100+ commands
    - Warning: May be very large output

  - **Option C - Custom Scope**
    - User specifies exactly what to discover

  - 🛑 WAIT FOR CONFIRMATION

- **Command Set Adaptation**: Note added that commands shown are for Option B (full), adapt based on choice

**Key Improvements**:
- Prevents running expensive discovery when not needed
- Time and command count estimates
- Adapts command execution based on confirmed scope
- Respects user's time and focus

---

## Pattern Comparison

### Old Pattern (Assumption-Based)
```markdown
**Multi-Tenancy** (if applicable):
Ask user if application needs multi-tenancy.

If YES, include in architecture:
- Tenant identification strategy (subdomain, path, header, JWT claim)
- Data isolation model:
  - Shared schema with tenant_id
  - Schema per tenant
  - Database per tenant
[Shows implementation details before confirmation]
```

**Problems**:
- "If applicable" is vague - when is it applicable?
- Asks IF needed, but then shows HOW without waiting for answer
- No guidance on which data isolation model to choose
- No effort estimates
- No confirmation gate

### New Pattern (Query-First)
```markdown
## DECISION REQUIRED: Multi-Tenancy Support

**Question 1**: "Does your application need to support multiple tenants?"
- Yes, multiple tenants needed
- No, single tenant only
- Unsure (describe your use case)

🛑 WAIT FOR ANSWER

[If YES:]

**Question 2**: "What level of data isolation do you require?"

**Option A - Shared schema with tenant_id** (RECOMMENDED for cost optimization)
- **Why recommended**: Simplest implementation, lowest cost, good performance
- **Best for**: SaaS apps where tenants trust platform, no regulatory isolation
- **Pros**: [list]
- **Cons**: [list - honest about data leakage risk]
- **Security**: Row-level security (RLS) policies required
- **Implementation effort**: Low (1-2 days)

**Option B - Schema per tenant**
- **Best for**: Moderate isolation, compliance requirements
- **Pros**: [list]
- **Cons**: [list]
- **Implementation effort**: Medium (3-5 days)

**Option C - Database per tenant**
- **Best for**: Strict regulatory (HIPAA, financial), enterprise customers
- **Pros**: [list]
- **Cons**: [list - honest about cost and complexity]
- **Implementation effort**: High (1-2 weeks)

**Question**: "Which data isolation model? (A/B/C)"

🛑 WAIT FOR CONFIRMATION

**Record in /specs/09_DECISIONS.md**: [details]
```

**Improvements**:
- Clear decision point with stop gate
- Three options with detailed evaluation
- RECOMMENDED label with clear reasoning
- Honest pros AND cons (including risks)
- Implementation effort estimates
- Best-for guidance based on requirements
- Explicit documentation requirement

---

## Common Patterns Applied

### 1. "RECOMMENDED" Label Structure
Every recommendation now includes:
- **Why recommended**: [Cite best practice, industry standard, or specific advantage]
- **Best for**: [Your specific requirements as stated]
- **Pros**: [Benefits]
- **Cons**: [Tradeoffs - be honest]
- **Reference**: [Link to official docs]
- **Implementation effort**: [Time estimate]
- **Fallback**: [What to do if this doesn't work out]

### 2. Decision Gate Structure
```markdown
## DECISION REQUIRED: [Decision Name]

**Context**: [Why this decision matters]

**Question**: [Clear question]

**Option A** (RECOMMENDED)
[Full evaluation as above]

**Option B**
[Full evaluation]

**Option C**
[Full evaluation if applicable]

**Question**: "Which option? (A/B/C or describe alternative)"

🛑 WAIT FOR CONFIRMATION
```

### 3. Stop Gates (🛑)
Every decision point now has explicit wait gate:
- 🛑 WAIT FOR ANSWER (for information gathering)
- 🛑 WAIT FOR CONFIRMATION (for decisions)
- 🛑 WAIT FOR APPROVAL (for final go/no-go)

### 4. Effort Estimates
All options now include:
- Time estimates (minutes, hours, days, weeks)
- Complexity level (Low/Medium/High or Simple/Moderate/Complex)
- Number of commands/steps when applicable

### 5. Honest Tradeoffs
Every option includes honest cons:
- ❌ NOT: "No cons" or "Perfect solution"
- ✅ YES: "Risk of data leakage if queries miss tenant_id filter"
- ✅ YES: "Higher cost (compute + DBUs), vendor lock-in"
- ✅ YES: "Takes more time (thorough investigation)"

---

## Impact Summary

### Files Created: 1
- QUERY_FIRST_PATTERN.md (template and guidelines)

### Files Updated: 6
1. full-app-02-architecture-tdd.md
2. data-lakehouse-builder.md
3. devops-change-builder.md
4. bug-workflow-builder-tdd.md
5. network-flow-builder.md
6. QUERY_FIRST_MIGRATION_SUMMARY.md (this file)

### Total Changes:
- **Decision gates added**: 20+
- **🛑 WAIT gates added**: 30+
- **RECOMMENDED labels standardized**: 15+
- **Options presented (with full evaluation)**: 40+
- **Effort estimates added**: 25+
- **Reference links added**: 10+

### Patterns Eliminated:
- ❌ "If [condition], include..." (assumption-based)
- ❌ "Ask user about X" followed by implementation details
- ❌ "PREFER [option]" without explanation
- ❌ "Recommended" without pros/cons/reasoning
- ❌ Presenting solutions before confirming requirements

### Patterns Added:
- ✅ Query → Options → Recommend → Confirm → Implement
- ✅ Every recommendation has "Why recommended" + reference
- ✅ Honest pros AND cons for every option
- ✅ Explicit wait gates at every decision
- ✅ Effort estimates for informed decisions
- ✅ Fallback plans for risky choices
- ✅ "Best for" guidance tied to stated requirements

---

## Validation Checklist

For each updated command, verify:

- [ ] No assumptions made without explicit confirmation
- [ ] Every decision has 2-4 options presented
- [ ] Every RECOMMENDED option has:
  - [ ] "Why recommended" section
  - [ ] Reference to requirements or best practices
  - [ ] Official docs link
  - [ ] Honest cons listed
  - [ ] Effort estimate
- [ ] 🛑 WAIT gates at all decision points
- [ ] Decisions recorded in appropriate spec files
- [ ] Final approval gate before implementation
- [ ] Points of no easy return explicitly called out

---

## Next Steps (Recommended)

### Other Files to Update (Lower Priority)
Consider applying query-first pattern to:
- full-app-01-prd-tdd.md (scope decisions)
- full-app-03-data-model-tdd.md (normalization vs denormalization)
- full-app-05-ui-ux-tdd.md (UX complexity level)
- full-app-07-security-nfr-tdd.md (security baseline level)

### Pattern Reinforcement
- Add QUERY_FIRST_PATTERN.md reference to each command file
- Create examples for common decision types
- Consider adding decision templates to .claude/ directory

### Continuous Improvement
- Collect feedback on decision gates (too many? too few?)
- Refine effort estimates based on actual experience
- Update RECOMMENDED options as best practices evolve
- Add more reference links to official documentation

---

## Migration Complete

**Status**: ✅ Core cursor commands migrated to query-first/confirm flow

**Result**: Engineers will now be queried for all key architectural decisions with:
- Clear options and tradeoffs
- Recommended defaults (not assumptions)
- Explicit confirmation required
- Honest evaluation of all alternatives

**Philosophy**:
> We present expertise through recommendations, but respect that the engineer owns the decision.
