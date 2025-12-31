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
Present 2–3 implementation approaches and recommend one.
Record decisions in 09_DECISIONS.md.
Ask ONE blocking question.
No code changes.
