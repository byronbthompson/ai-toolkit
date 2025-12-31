x`  Open /specs/01_PRD.md and draft:

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

Generate 3 implementation scenarios based on requirements:

SCENARIO A - LEAN MVP (2-4 milestones, 2-6 hours):
- Scope: Core feature only, minimal error handling, hardcoded configs where acceptable
- Quality: Basic tests, 70%+ coverage, essential validations only
- Features: [list absolute minimum features to prove concept]
- Compromises: Limited edge case handling, basic UI, no advanced features
- Use case: Quick validation, prototype for feedback, proof of concept
- Time estimate: [X hours]

SCENARIO B - STANDARD PRODUCTION (4-8 milestones, 8-16 hours):
- Scope: Full feature set from PRD, proper error handling, security baseline
- Quality: Comprehensive tests, 80%+ coverage, all acceptance criteria met
- Features: [list all features from PRD scope]
- Compromises: Standard security (not hardened), good enough performance
- Use case: Ship to real users, maintain long-term, production-ready
- Time estimate: [Y hours]

SCENARIO C - ENTERPRISE GRADE (8-12 milestones, 16-30 hours):
- Scope: Everything in B + advanced features, hardened security, monitoring
- Quality: Exhaustive tests, 90%+ coverage, performance optimization
- Features: [list all features plus enterprise additions like audit logs, HA, etc.]
- Enhancements: Rate limiting, comprehensive logging, metrics, alerting
- Use case: Critical business app, high scale, compliance requirements
- Time estimate: [Z hours]

SCENARIO D - ENTERPRISE MULTI-TENANT (10-15 milestones, 24-40 hours):
- Scope: Everything in C + multi-tenancy, white-labeling, internationalization
- Quality: Full test coverage including tenant isolation, 90%+ coverage
- Features: [all features + tenant management, custom branding, i18n]
- Complexity: 50-100% additional time for multi-tenant architecture
- Use case: SaaS product, multiple customers, custom branding per customer
- Time estimate: [W hours]

PROGRESSIVE PATH:
- Can start with Scenario A and evolve to B/C/D incrementally
- Each scenario builds on previous (A → B → C → D)
- Recommend starting with [scenario] based on stated goals
- User can decide to stop at any scenario if needs are met

Present 2–4 scope options (Lean MVP / Standard / Ambitious / Enterprise) and recommend one based on scenario analysis.
Ask ONE blocking question.
No code changes.
