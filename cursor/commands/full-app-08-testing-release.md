Open /specs/08_TESTING_RELEASE.md and draft:

- Test pyramid (unit/integration/e2e) with coverage targets
- Test Execution Strategy (tiered approach for fast feedback):
  TIER 1 - SMOKE TESTS (run first, fail fast):
  - Application starts without crashes
  - Database connection succeeds
  - Required environment variables present
  - Critical imports/dependencies load
  - Basic health check endpoint responds (if API)
  - Execution time: 5-10 seconds target
  - Purpose: Catch critical breakage before running expensive tests
  TIER 2 - UNIT TESTS (core logic):
  - Business logic functions
  - Data transformations and validations
  - Utility functions
  - Execution time: 30-60 seconds target
  - Purpose: Validate individual components work correctly
  TIER 3 - INTEGRATION TESTS (cross-component):
  - API endpoint tests (request → response)
  - Database operations (CRUD)
  - External service mocks/stubs
  - Execution time: 1-3 minutes target
  - Purpose: Validate components work together
  TIER 4 - E2E TESTS (full workflows):
  - Complete user journeys end-to-end
  - Browser automation (if UI)
  - Real integrations (staging environment)
  - Execution time: 3-10 minutes target
  - Purpose: Validate entire system works as user expects
  EXECUTION ORDER (fail fast principle):
  1. Run Tier 1 (smoke tests)
     - If FAIL: STOP immediately, fix critical issues
     - If PASS: Continue to Tier 2
  2. Run Tier 2 (unit tests)
     - If FAIL: STOP, fix unit test failures
     - If PASS: Continue to Tier 3
  3. Run Tier 3 & 4 (integration/E2E)
     - If FAIL: Fix and rerun from Tier 1
  QUALITY GATE ENFORCEMENT:
  - All tiers must pass 100%
  - Generate coverage report only after all tests pass
  - Report execution time for each tier (track performance regression)
  - Total test suite should run in <15 minutes (optimize if longer)
- Testing approach (ask user to confirm):
  - TDD (Test-Driven Development) - RECOMMENDED for AI-assisted development
    - Write tests from acceptance criteria first
    - Implement code that passes tests
    - Faster with AI, reduces hallucination, higher coverage
    - Best for: complex logic, APIs, business rules, bug fixes
  - Implementation-first with required tests
    - Write implementation, then comprehensive tests
    - Still enforces coverage gates
    - Best for: simple CRUD, rapid prototypes, UI exploration
  - Hybrid approach (default if uncertain)
    - TDD for complex features and all bug fixes
    - Implementation-first for simple features
    - Document which approach for each feature type in 09_DECISIONS.md
- Code coverage requirements:
  - Minimum overall coverage threshold (recommend 80% for new code)
  - Critical path coverage requirement (recommend 90%+)
  - Coverage must never decrease between milestones
  - Per-file/module coverage targets
- Tooling choices:
  - Test framework + coverage tool
  - Linter (must be configured):
    - Python: pylint, flake8, ruff, or black
    - Node.js: eslint
    - Markdown: markdownlint (.markdownlint.json required)
    - Configure for: 4-space indentation, single quotes (where appropriate)
    - Document linter rules and config file location
- Acceptance criteria → tests mapping strategy
- Quality gates for each milestone:
  - All tests pass
  - Coverage threshold met or exceeded
  - No critical security/lint violations
  - Performance benchmarks met (if defined)
- CI checks to add (include coverage reporting and enforcement)
- Pre-commit/pre-push hooks (optional: run tests + coverage locally)
- Environment strategy
- Rollback strategy

Review OFFICIAL testing tool docs and recommended patterns.
Present 2–3 testing strategy options and recommend one.
Record decisions in 09_DECISIONS.md.
Ask ONE blocking question.
No code changes.
