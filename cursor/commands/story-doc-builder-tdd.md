Create /specs/STORY_<id>.md with:

---

## PHASE 0: PROJECT CONTEXT CHECK

**CRITICAL**: Determine if this is existing codebase (brownfield) or new project (greenfield).

Ask user:
1. "Is this story for an existing codebase (brownfield) or new project (greenfield)?"

**IF BROWNFIELD** (existing codebase - most common for stories):
2. "Does `/specs/00_BROWNFIELD_CONTEXT.md` exist in your project?"
   - **IF NO**:
     - "⚠️ RECOMMENDATION: Run `brownfield-context-discovery-tdd.md` first."
     - "For story implementation, brownfield context helps you:"
     - "  - Find existing code patterns to follow"
     - "  - Identify reusable utilities and components"
     - "  - Understand touchpoints in existing code"
     - "  - Know testing patterns to match"
     - "This takes ~20-40 minutes but ensures consistency with codebase."
     - "Proceed without brownfield context? [Yes/No]"
       - **IF NO**: Exit and prompt user to run `brownfield-context-discovery-tdd.md`
       - **IF YES**:
         - Set `BROWNFIELD_MODE=true`
         - Set `BROWNFIELD_CONTEXT=missing`
         - ⚠️ Warning: "Proceeding without brownfield context. Risk of pattern inconsistency."
   - **IF YES**:
     - Read `/specs/00_BROWNFIELD_CONTEXT.md`
     - Set `BROWNFIELD_MODE=true`
     - Set `BROWNFIELD_CONTEXT=available`
     - Note existing patterns, code style, test patterns

**IF GREENFIELD** (new project):
- Set `BROWNFIELD_MODE=false`
- Note: "Greenfield project story"

3. Record decision in STORY spec being created:
```markdown
## Project Context
- Type: [Brownfield/Greenfield]
- Brownfield Context: [Available at /specs/00_BROWNFIELD_CONTEXT.md / Missing - proceeded anyway / N/A]
```

---

## STORY SPECIFICATION

Build the story specification document:

- **User story**: As a [user type], I want to [action], so that [benefit]
- **Constraints**:
  - Technical constraints (from brownfield context if available)
  - Time constraints
  - Scope constraints
- **Touch points** (where in code):
  - IF BROWNFIELD_CONTEXT=available:
    - Reference patterns from `/specs/00_BROWNFIELD_CONTEXT.md`
    - Identify files to modify (follow existing structure)
    - Identify reusable components (DRY principle)
  - IF BROWNFIELD_CONTEXT=missing:
    - Use Task tool (Explore) to find relevant code areas
    - Search for similar functionality
    - Identify integration points
- **Acceptance criteria** (Given/When/Then format):
  - Functional criteria
  - Non-functional criteria (performance, usability)
  - Testing criteria (must match existing test patterns in brownfield)
- **Edge cases**:
  - Error scenarios
  - Boundary conditions
  - Integration edge cases

---

## IMPLEMENTATION APPROACHES

Present 2–3 approaches and recommend one based on:
- IF BROWNFIELD: Existing patterns from brownfield context
- IF GREENFIELD: Best practices for new code
- Story complexity and constraints
- Testing requirements

**Option A**: [Approach following existing patterns]
- Pros: [Consistency with codebase, reuses existing utilities]
- Cons: [Tradeoffs]
- Best for: [When this makes sense]

**Option B**: [Alternative approach]
- Pros: [Advantages]
- Cons: [Tradeoffs]
- Best for: [When this makes sense]

**Option C** (if applicable): [Third approach]
- Pros: [Advantages]
- Cons: [Tradeoffs]
- Best for: [When this makes sense]

**Recommendation**: Option [A/B/C] because [reasoning based on context]

---

## BROWNFIELD INTEGRATION PLAN (if BROWNFIELD_MODE=true)

**Files to modify**: [List existing files that need changes]
**Files to create**: [List new files needed]
**Existing utilities to reuse**: [List from brownfield context]
**Patterns to follow**: [Reference specific patterns from brownfield context]
**Tests to update**: [Existing tests affected]
**Quality gates**:
- All existing tests must pass
- Coverage must not decrease
- Linter violations must not increase

---

Ask ONE blocking question if any ambiguity remains.
No code changes at this stage - only documentation.
