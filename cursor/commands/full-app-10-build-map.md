Create /specs/BUILD_MAP.md.

Requirements:
- Milestones ordered for a SINGLE developer
- BROWNFIELD INTEGRATION MILESTONES (if existing codebase):
  MILESTONE 0: CONTEXT DISCOVERY (brownfield only, ~30-60 min)
  - Complete brownfield-context-discovery.md process
  - Generate 00_BROWNFIELD_CONTEXT.md
  - Identify existing patterns, constraints, risks
  - Review with user for accuracy
  - Get user approval of context analysis
  - No code changes, pure exploration
  MILESTONE 1: ENVIRONMENT + BASELINE (brownfield only, ~30-60 min)
  - Set up local development environment
  - Run existing tests → establish baseline (X% coverage, Y tests passing)
  - Run existing linter → establish baseline (N violations)
  - Run existing build → verify builds successfully
  - Verify can run application locally
  - Document baseline in BUILD_MAP.md
  - No code changes, just verification
  MILESTONE 2: INTEGRATION SPIKE (brownfield recommended, ~1-2 hours)
  - Implement SMALLEST possible integration with existing code
  - Example: Add one new API endpoint following existing pattern
  - Example: Add one new model/table with migration
  - Purpose: Validate integration assumptions before full feature build
  - Success criteria:
    - New code integrates successfully
    - All existing tests still pass
    - Existing coverage doesn't decrease
    - Build still succeeds
    - Patterns match existing code
  - If spike fails, revise approach before continuing
  MILESTONE 3+: Feature Implementation
  - Build new features incrementally
  - Each milestone validates no regression (all existing tests pass)
  - Each milestone maintains or improves coverage
  - Each milestone follows patterns from integration spike
- MVP-Focused Milestone Design:
  - Each milestone should deliver working, testable functionality (not partial features)
  - Avoid milestones that create incomplete features (e.g., UI without backend)
  - Pattern: Build features end-to-end in thin vertical slices
  - Example GOOD: "Milestone 2: User login (API + UI + auth flow working)"
  - Example BAD: "Milestone 2: All API endpoints (no UI)", "Milestone 3: All UI (no backend)"
  - User can stop at any milestone and have a working (if limited) application
  - Consider progressive enhancement: Core → Enhanced → Polished
- Golden Path Milestone (recommended as Milestone 2):
  - After environment setup, implement ONE complete end-to-end user flow
  - Example: User signup → login → create item → view item → logout
  - Purpose: Validate architecture choices work together before building all features
  - Includes: Database integration, API, UI (if applicable), auth, basic validation
  - Success criteria: 1 complete user journey works end-to-end
  - Benefits: Catches integration issues early, validates tech stack, provides template
- Environment setup (include in first milestone):
  - Git repository initialization (git init) if not already present
  - .gitignore file appropriate for tech stack
  - Linter configuration appropriate for tech stack:
    - Python: pylint, flake8, ruff, or black (document choice in 09_DECISIONS.md)
    - Node.js: eslint with appropriate config (.eslintrc)
    - Markdown: .markdownlint.json (always required with base config)
    - Configure style: 4-space indentation, single quotes (where appropriate)
    - Include linter config files and document commands
  - Python projects: conda environment setup (environment.yml or requirements.txt)
  - Node.js projects: nvm version specification (.nvmrc file)
- Each milestone has:
  - Milestone number and name
  - Status tracking (pending/in-progress/completed)
  - Inputs (doc sections)
  - Outputs (files created/modified)
  - Commands (build/run/test) - use conda/nvm as applicable
  - Verification steps
  - Quality gates (ALL must pass before proceeding):
    - All tests passing
    - Code coverage threshold met (minimum 80% recommended, must never decrease)
    - Coverage report command and baseline/target documented
    - No blocking lint/security issues
    - Acceptance criteria validated
  - Commit step (AFTER quality gates pass):
    - Update BUILD_MAP.md to mark milestone completed
    - Add completion date and actual results (coverage %, files changed, deviations)
    - Create descriptive commit with milestone summary
    - Include test/coverage results in commit message
  - Rollback notes (if relevant)
- Stop/Ask checkpoints
- Coverage enforcement strategy:
  - Initial baseline set in first milestone with tests
  - Each subsequent milestone must maintain or improve coverage
  - Command to generate coverage report included in each milestone
  - Failed coverage check = milestone incomplete
- Definition of Done at end (includes final coverage report)
- Milestone tracking format in BUILD_MAP.md:
  - [ ] Milestone 1: Setup (pending)
  - [x] Milestone 1: Setup (completed YYYY-MM-DD, coverage: X%, files: N)
  - Use checkboxes or status indicators for clear progress tracking

Do not implement code yet.
Ask ONE final blocking question before we enter implementation mode.
