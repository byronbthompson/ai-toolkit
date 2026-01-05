Create /specs/FEATURE_<name>.md with:

---

## PHASE 0: PROJECT CONTEXT CHECK

**CRITICAL**: Determine if this is existing codebase (brownfield) or new project (greenfield).

Ask user:
1. "Is this work for an existing codebase (brownfield) or a new project (greenfield)?"

**IF BROWNFIELD** (existing codebase):
2. "Does `/specs/00_BROWNFIELD_CONTEXT.md` exist in your project?"
   - **IF NO**:
     - "⚠️ RECOMMENDATION: Run `brownfield-context-discovery-tdd.md` first to understand existing patterns, tech stack, and constraints."
     - "This takes ~20-40 minutes but prevents costly mistakes and rework."
     - "Benefits:"
     - "  - Discover existing patterns you MUST follow"
     - "  - Identify high-risk areas to avoid"
     - "  - Document quality baselines (coverage, linter)"
     - "  - Find reusable utilities and components"
     - "Proceed without brownfield context? [Yes/No]"
       - **IF NO**: Exit and prompt user to run `brownfield-context-discovery-tdd.md`
       - **IF YES**:
         - Set `BROWNFIELD_MODE=true`
         - Set `BROWNFIELD_CONTEXT=missing`
         - ⚠️ Warning: "Proceeding without brownfield context. Risk of pattern inconsistency and breaking changes."
   - **IF YES**:
     - Read `/specs/00_BROWNFIELD_CONTEXT.md`
     - Set `BROWNFIELD_MODE=true`
     - Set `BROWNFIELD_CONTEXT=available`
     - Reference it throughout workflow

**IF GREENFIELD** (new project):
- Set `BROWNFIELD_MODE=false`
- Note: "Greenfield project - no existing patterns to follow"
- Python preferred when appropriate (data work, apis, scripting)

3. Record decision in FEATURE spec being created:
```markdown
## Project Context
- Type: [Brownfield/Greenfield]
- Brownfield Context: [Available at /specs/00_BROWNFIELD_CONTEXT.md / Missing - proceeded anyway / N/A]
```

---

## EXISTING CODEBASE ANALYSIS (if BROWNFIELD_MODE=true)

**IF BROWNFIELD_CONTEXT=available** (recommended path):
1. Reference `/specs/00_BROWNFIELD_CONTEXT.md` for:
   - Existing architectural patterns
   - Code style and naming conventions
   - Testing patterns and coverage baseline
   - High-risk areas to avoid
   - Technology stack and versions

**IF BROWNFIELD_CONTEXT=missing** (user proceeded without context):
⚠️ Perform minimal brownfield analysis inline:
1. Analyze related existing code:
   - Use Task tool (Explore agent) to find similar features:
     - "How are similar features currently implemented?"
     - "What files would I need to modify to add this feature?"
     - "Are there existing utilities/helpers I should reuse?"
   - Search for existing patterns:
     - Similar API endpoints (if adding API)
     - Similar database models (if adding data)
     - Similar UI components (if adding UI)
     - Similar business logic (if adding logic)
2. Document integration approach:
   - Files to create (new files)
   - Files to modify (existing files that need changes)
   - Existing code to reuse (DRY principle)
   - Patterns to follow (from existing codebase)
3. Identify risks:
   - Will this break existing functionality?
   - Are there backwards compatibility concerns?
   - Do existing tests cover areas being modified?
4. Ask user if unclear:
   - "Should this follow the pattern from [existing-file.ext] or use a different approach?"
   - "I found two different patterns for [X]. Which should I follow?"
   - "This requires modifying [critical-file]. Proceed with caution or alternative approach?"

**REGRESSION PROTECTION** (all brownfield work):
- [ ] Note current test coverage baseline (must not decrease)
- [ ] Identify existing tests affected by changes
- [ ] Plan to run ALL existing tests after implementation
- [ ] Check if linter baseline will be affected

---

## FEATURE SPECIFICATION

Build the feature specification document:

- Problem and goal
- Scope/non-scope
- UI changes
- API changes
- Data changes
- Acceptance criteria (Given/When/Then)
- Test requirements:
  - Testing approach (refer to 08_TESTING_RELEASE.md decision):
    - TDD (recommended for AI, complex logic, APIs, business rules)
    - Implementation-first (acceptable for simple CRUD, prototypes)
    - Confirm approach based on feature complexity
  - Unit tests for new/modified functions
  - Integration tests for API/data changes
  - Coverage target (must maintain or improve overall coverage)
  - Current coverage baseline documented
- Quality gates:
  - All tests pass
  - Coverage threshold met (no decrease allowed)
  - Lint/security checks pass
- Completion criteria:
  - Update relevant tracking docs (BUILD_MAP.md if part of milestone plan)
  - Mark feature status as completed with results
  - Commit changes after all quality gates pass
  - Descriptive commit message with feature summary and results

Before choosing approach, ask clarifying questions:
- What is the existing tech stack in the codebase?
- What is the current code coverage baseline?
- Any performance or integration constraints?
- If greenfield: python preferred when appropriate (data work, apis, scripting)

Review OFFICIAL docs for any touched frameworks/tools.

---

## IMPLEMENTATION APPROACH SELECTION (Verbalized Sampling)

**Context**: Implementation approach affects maintainability, testability, and alignment with existing codebase patterns.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different architectural approaches (simple/pragmatic vs robust/scalable vs innovative).

For EACH approach, provide:

**Option A - [Simple/Direct Approach]** (Confidence: **[High/Medium/Low]** based on requirements)

**Best for**: [When this approach makes sense]

**Implementation**:
- [Key technical decisions]
- Files to create: [list]
- Files to modify: [list]
- Existing code to reuse: [list]

**Pros**:
- [3-5 advantages]

**Cons**:
- [3-5 honest tradeoffs]

**Avoid If**:
- [When this approach fails]

**Reasoning**: I rate this **[confidence level]** because you mentioned "[reference their requirements/constraints]". This approach works well for [specific use case]. However, confidence drops to **[lower level]** if "[condition that changes recommendation]".

**Time**: [X hours]

---

**Option B - [Robust/Scalable Approach]** (Confidence: **[High/Medium/Low]** based on requirements)

[Same structure as Option A]

---

**Option C - [Alternative/Innovative Approach]** (Confidence: **[High/Medium/Low]** based on requirements)

[Same structure as Option A]

---

**Recommendation**:
Based on:
- Feature complexity: [their answer]
- Existing patterns: [from brownfield analysis]
- Performance requirements: [their answer]
- Tech stack: [their answer]

I recommend **Option [A/B/C]** because [specific reasoning tying requirements to approach strengths].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [reasoning]. Factors that could change my recommendation:
- If [condition X], consider [alternative option]
- If existing codebase uses [pattern Y], must follow that pattern
- If performance becomes critical, may need [different approach]

**Question**: "Which implementation approach do you prefer? (A/B/C)"

🛑 WAIT FOR CONFIRMATION

Once confirmed, record decision in 09_DECISIONS.md with:
- Approach chosen
- Reasoning (tying to requirements and existing patterns)
- Files to create/modify
- Reused components
- Confidence score: [Low/Medium/High]

---

Ask ONE blocking question if any ambiguity remains.
No code changes at this stage - only documentation.
