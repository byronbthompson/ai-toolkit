Read ${SPEC_PATH} from the previous step's 00_START_HERE.md file (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}01_PRD.md and draft:

- Problem statement
- Personas
- User journeys (bullet)
- MVP scope + non-goals
- Success metrics
- Acceptance criteria (testable) - CRITICAL for TDD approach:
  - Format: Use Given/When/Then structure (recommended for clarity)
  - Completeness: Must be specific enough to generate tests from
  - Coverage: Include happy path, edge cases, and error scenarios
  - Testability: Each criterion must be objectively verifiable
  - Connection to testing:
    - If using TDD: These criteria will be directly converted to test cases BEFORE implementation
    - Tests must validate every acceptance criterion
    - Missing criteria = missing test coverage
  - Quality guidelines:
    - Good: "Given a VIP user with cart total $100, When 20% discount code is applied, Then final price is $80 and discount is recorded"
    - Poor: "Users should get discounts when they qualify"
    - Good: "Given an unauthenticated user, When accessing /dashboard, Then redirect to /login with 401 status"
    - Poor: "Protected pages should be secure"
  - Minimum requirements:
    - Cover all critical user flows and business rules
    - Include both success and failure scenarios
    - Specify expected system behavior for edge cases
    - Define observable outcomes (API responses, UI changes, data states)
- Out of scope

Before finalizing, review any relevant OFFICIAL docs/standards (name them and what they influence).

SCENARIO PLANNING (present delivery options):

**IMPORTANT**: All time estimates assume **AI-assisted development** (Claude, Cursor, or similar AI agents).
AI agents can code 3-5x faster than manual development for most tasks.

Generate 3-4 implementation scenarios based on requirements:

SCENARIO A - LEAN MVP (2-4 milestones, 1-3 hours AI-assisted):
- Scope: Core feature only, minimal error handling, hardcoded configs where acceptable
- Quality: Basic tests, 70%+ coverage, essential validations only
- Features: [list absolute minimum features to prove concept]
- Compromises: Limited edge case handling, basic UI, no advanced features
- Use case: Quick validation, prototype for feedback, proof of concept
- AI-assisted time: 1-3 hours (planning: 30-45 min, implementation: 30 min-2 hours)
- Manual equivalent: 6-15 hours (3-5x slower)

SCENARIO B - STANDARD PRODUCTION (4-8 milestones, 4-8 hours AI-assisted):
- Scope: Full feature set from PRD, proper error handling, security baseline
- Quality: Comprehensive tests, 80%+ coverage, all acceptance criteria met
- Features: [list all features from PRD scope]
- Compromises: Standard security (not hardened), good enough performance
- Use case: Ship to real users, maintain long-term, production-ready
- AI-assisted time: 4-8 hours (planning: 1-1.5 hours, implementation: 3-6.5 hours)
- Manual equivalent: 20-40 hours (4-5x slower)

SCENARIO C - ENTERPRISE GRADE (8-12 milestones, 10-18 hours AI-assisted):
- Scope: Everything in B + advanced features, hardened security, monitoring
- Quality: Exhaustive tests, 90%+ coverage, performance optimization
- Features: [list all features plus enterprise additions like audit logs, HA, etc.]
- Enhancements: Rate limiting, comprehensive logging, metrics, alerting
- Use case: Critical business app, high scale, compliance requirements
- AI-assisted time: 10-18 hours (planning: 1.5-2 hours, implementation: 8.5-16 hours)
- Manual equivalent: 50-90 hours (5x slower)

SCENARIO D - ENTERPRISE MULTI-TENANT (10-15 milestones, 16-28 hours AI-assisted):
- Scope: Everything in C + multi-tenancy, white-labeling, internationalization
- Quality: Full test coverage including tenant isolation, 90%+ coverage
- Features: [all features + tenant management, custom branding, i18n]
- Complexity: 50-100% additional time for multi-tenant architecture
- Use case: SaaS product, multiple customers, custom branding per customer
- AI-assisted time: 16-28 hours (planning: 2-2.5 hours, implementation: 14-25.5 hours)
- Manual equivalent: 80-140 hours (5x slower)

**AI Efficiency Factors**:
- ✅ Boilerplate code: Near-instant generation (5-10x faster)
- ✅ Test writing: Automated from acceptance criteria (3-5x faster)
- ✅ Documentation: Auto-generated from code (10x faster)
- ✅ Refactoring: Systematic and safe (3-4x faster)
- ⚠️ Complex algorithms: 2-3x faster (still requires human review)
- ⚠️ Architecture decisions: Same speed (human judgment required)
- ⚠️ Debugging edge cases: 1.5-2x faster (human insight critical)

PROGRESSIVE PATH:
- Can start with Scenario A and evolve to B/C/D incrementally
- Each scenario builds on previous (A → B → C → D)
- Recommend starting with [scenario] based on stated goals
- User can decide to stop at any scenario if needs are met

Present 2–4 scope options (Lean MVP / Standard / Ambitious / Enterprise) and recommend one based on scenario analysis.
Ask ONE blocking question.
No code changes.

---
**NEXT STEP**: When 01_PRD.md is complete and approved, run `full-app-02-architecture-tdd.md` to design the system architecture.
