CRITICAL FIRST STEPS:

## Step 1: Determine Documentation Structure

Ask user: "Do you want to organize specs in a nested folder structure (for parallel work) or flat structure?"

**Option A - Nested Structure (RECOMMENDED for team/parallel work)**:
- Path: `/specs/[ticket-id]/[doc-name].md`
- Example: `/specs/PROJ-123/00_START_HERE.md`
- Benefits: Multiple engineers can work on different tickets without conflicts
- Use case: Team environments, multiple features in parallel

**Option B - Flat Structure (traditional)**:
- Path: `/specs/[doc-name].md`
- Example: `/specs/00_START_HERE.md`
- Benefits: Simpler structure for single developer
- Use case: Solo developer, single feature at a time

🛑 WAIT FOR ANSWER

**IF Option A (Nested)**:
- Ask: "What is the ticket ID or feature name for this work?"
- Set SPEC_PATH = `/specs/[ticket-id]/`
- Example: User says "PROJ-123" → SPEC_PATH = `/specs/PROJ-123/`

**IF Option B (Flat)**:
- Set SPEC_PATH = `/specs/`

**For all subsequent steps**: Replace `/specs/` with `${SPEC_PATH}` in all file paths.

---

## Step 2: Determine Project Type

Open ${SPEC_PATH}00_START_HERE.md and draft it.

Ask user: "Is this a NEW project (greenfield) or adding features to EXISTING code (brownfield)?"

IF BROWNFIELD (existing codebase):
- STOP and use brownfield-context-discovery-tdd.md FIRST
- Complete full context discovery before continuing with this doc
- Generate ${SPEC_PATH}00_BROWNFIELD_CONTEXT.md before 00_START_HERE.md
- Reference brownfield context throughout planning
- All architectural decisions must align with existing patterns
- Return here after brownfield context is complete and approved

IF GREENFIELD (new project):
- Continue with this document as normal
- Note: "Greenfield project - no existing codebase constraints"

**IMPORTANT**: Document the chosen SPEC_PATH at the top of 00_START_HERE.md:
```
SPEC_PATH: [/specs/ OR /specs/TICKET-ID/]
```
This ensures all subsequent steps use the correct path.

Include:
- Order of authority for docs
- Collaboration rules: clarifying questions one at a time, options first, decisions recorded
- Technology selection approach: ask clarifying questions, present options with tradeoffs
- Python preference noted: preferred when it makes sense (apis, data work, ml, scripting)
- Environment management standards:
  - Version control: git (initialize with git init, create .gitignore)
  - Commit regularly after significant feature completions when all quality gates pass
  - Linter: configure appropriate linter for tech stack (including .markdownlint.json)
  - Code style: 4-space indentation, single quotes (where language appropriate)
  - Python projects: conda for environment management
  - Node.js projects: nvm for version management
- Testing approach (will be confirmed in 08_TESTING_RELEASE.md):
  - TDD recommended for AI-assisted development (faster, more accurate)
  - Implementation-first acceptable for simple features
  - Bug fixes always use TDD (regression test first)
- Complexity Assessment (provide early estimate):
  **IMPORTANT**: All time estimates assume **AI-assisted development** (Claude, Cursor, or similar AI agents).
  AI can code 3-5x faster than manual development.

  - Agent implementation time estimate: [X-Y hours AI-assisted]
    - Planning phase: ~30 min-1.5 hours (all docs generated with AI)
    - Milestone implementation: ~N hours (based on milestone count × complexity)
    - Quality gates per milestone: ~10-20 min each (automated tests run faster)

  - Complexity signals to identify (AI-assisted times):
    - **Simple**: CRUD app, single data model, no external integrations
      - AI-assisted: 1-2 hours total (planning: 20-30 min, implementation: 40-90 min)
      - Manual equivalent: 5-10 hours (5x slower)

    - **Moderate**: Multi-model, external APIs, auth, basic workflows
      - AI-assisted: 3-6 hours total (planning: 45-60 min, implementation: 2-5 hours)
      - Manual equivalent: 15-30 hours (5x slower)

    - **Complex**: Real-time features, complex state, ML/AI, multi-service
      - AI-assisted: 8-15 hours total (planning: 1-1.5 hours, implementation: 7-13.5 hours)
      - Manual equivalent: 40-75 hours (5x slower)

    - **Very Complex**: Distributed systems, advanced security, custom protocols
      - AI-assisted: 16-28 hours total (planning: 1.5-2 hours, implementation: 14.5-26 hours)
      - Manual equivalent: 80-140 hours (5x slower)

    - **Enterprise**: Add 50-100% time for multi-tenant, white-label, internationalization
      - AI-assisted: Multiply above by 1.5-2x
      - Manual equivalent: Multiply above by 1.5-2x (same multiplier)

  - AI Efficiency Notes:
    - Boilerplate/repetitive code: 5-10x faster with AI
    - Test generation: 3-5x faster (auto-generated from criteria)
    - Documentation: 10x faster (auto-generated from code)
    - Complex logic: 2-3x faster (still requires human review)
    - Architecture decisions: Same speed (human judgment required)

  - Cost estimate: Document any paid APIs/services in constraints
  - Suggest scope reduction options if complexity exceeds user intent
- Stop/Ask conditions (do not invent API/DB/resources, do not assume tech stack)
- "Single developer executable" standard
- Ticket Interpretation section (goals, non-goals, constraints, risks)
- Open Questions section (include tech stack if not determined)

Then ask the SINGLE most blocking clarifying question.
No code changes.

---
**NEXT STEP**: When 00_START_HERE.md is complete and approved, run `full-app-01-prd-tdd.md` to define product requirements.
