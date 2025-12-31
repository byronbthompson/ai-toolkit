Open /specs/00_START_HERE.md and draft it.

CRITICAL FIRST STEP - Determine Project Type:

Ask user: "Is this a NEW project (greenfield) or adding features to EXISTING code (brownfield)?"

IF BROWNFIELD (existing codebase):
- STOP and use brownfield-context-discovery.md FIRST
- Complete full context discovery before continuing with this doc
- Generate 00_BROWNFIELD_CONTEXT.md before 00_START_HERE.md
- Reference brownfield context throughout planning
- All architectural decisions must align with existing patterns
- Return here after brownfield context is complete and approved

IF GREENFIELD (new project):
- Continue with this document as normal
- Note: "Greenfield project - no existing codebase constraints"

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
  - Agent implementation time estimate: [X-Y hours]
    - Planning phase: ~1-2 hours (all docs generated)
    - Milestone implementation: ~N hours (based on milestone count × complexity)
    - Quality gates per milestone: ~15-30 min each
  - Complexity signals to identify:
    - Simple: CRUD app, single data model, no external integrations (2-4 hours)
    - Moderate: Multi-model, external APIs, auth, basic workflows (6-12 hours)
    - Complex: Real-time features, complex state, ML/AI, multi-service (12-24 hours)
    - Very Complex: Distributed systems, advanced security, custom protocols (24+ hours)
    - Enterprise: Add 50-100% time for multi-tenant, white-label, internationalization
  - Cost estimate: Document any paid APIs/services in constraints
  - Suggest scope reduction options if complexity exceeds user intent
- Stop/Ask conditions (do not invent API/DB/resources, do not assume tech stack)
- "Single developer executable" standard
- Ticket Interpretation section (goals, non-goals, constraints, risks)
- Open Questions section (include tech stack if not determined)

Then ask the SINGLE most blocking clarifying question.
No code changes.
