Create /specs/FEATURE_<name>.md with:

CRITICAL - Existing Codebase Analysis (if brownfield):

If adding feature to existing codebase:
1. Reference 00_BROWNFIELD_CONTEXT.md for existing patterns
2. Analyze related existing code:
   - Use Task tool (Explore agent) to find similar features:
     - "How are similar features currently implemented?"
     - "What files would I need to modify to add this feature?"
     - "Are there existing utilities/helpers I should reuse?"
   - Search for existing patterns:
     - Similar API endpoints (if adding API)
     - Similar database models (if adding data)
     - Similar UI components (if adding UI)
     - Similar business logic (if adding logic)
3. Document integration approach:
   - Files to create (new files)
   - Files to modify (existing files that need changes)
   - Existing code to reuse (DRY principle)
   - Patterns to follow (from existing codebase)
4. Identify risks:
   - Will this break existing functionality? (check high-risk areas from brownfield context)
   - Are there backwards compatibility concerns?
   - Do existing tests cover areas being modified?
5. Ask user if unclear:
   - "Should this follow the pattern from [existing-file.ext] or use a different approach?"
   - "I found two different patterns for [X]. Which should I follow?"
   - "This requires modifying [high-risk-area]. Proceed with caution or alternative approach?"

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
