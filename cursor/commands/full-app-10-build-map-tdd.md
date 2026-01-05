Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

---

## MILESTONE PLANNING STRATEGY

**PHILOSOPHY**: Agile development prioritizes early tangible deliverables for fast validation, not waterfall planning.

**Present user with milestone planning approach choices**:

```
📋 MILESTONE PLANNING STRATEGY

How would you like to plan your milestones?

A. 🚀 AGILE GOLD PATH (Recommended)
   → Build ONE complete end-to-end user flow first (Golden Path)
   → Then add features incrementally as vertical slices
   → Fast validation: Working app in first 1-2 milestones
   → Tight feedback loops: Each milestone = testable functionality
   → Best for: Most projects, especially new features

B. 💪 LAYERED APPROACH (Traditional)
   → Backend first → Frontend → Integration
   → Or: Database → API → UI
   → Slower validation: No working app until later milestones
   → Risk: Integration issues discovered late
   → Best for: When layers have strict dependencies

C. 📋 CUSTOM BREAKDOWN
   → Define your own milestone strategy
   → AI will review for agile principles
   → Best for: Unusual architectures or specific constraints
```

🛑 **WAIT FOR CHOICE** → Store as MILESTONE_STRATEGY

---

## STRATEGY IMPLEMENTATION

### IF A (Agile Gold Path - Recommended):

**Milestone Structure**:
1. **Milestone 1**: Environment setup + project scaffolding
2. **Milestone 2 (GOLDEN PATH)**: ONE complete end-to-end user flow
   - Example: User signup → login → create item → view item → logout
   - Includes: Database + API + UI (if applicable) + auth working together
   - Purpose: Validate architecture choices integrate correctly
   - **This is your first tangible deliverable** (working app, even if limited)
3. **Milestone 3+**: Add features as vertical slices
   - Each milestone = one feature working end-to-end
   - Each milestone = testable, demonstrable progress
   - Can stop at any milestone with working (if limited) app

**Benefits**:
- ✅ Working app visible in 1-2 milestones (not 5+ milestones)
- ✅ Integration issues caught early (Milestone 2)
- ✅ Tight feedback loops (demo after each milestone)
- ✅ Lower risk (validate architecture before building all features)

**Say**: "I'll plan milestones using Agile Gold Path approach. First milestone will be environment setup, second milestone will be a complete end-to-end user flow to validate the architecture, then incremental feature additions."

### IF B (Layered Approach):

**Milestone Structure**:
1. **Milestone 1**: Environment setup
2. **Milestone 2**: Backend/Database layer
3. **Milestone 3**: API layer
4. **Milestone 4**: Frontend/UI layer
5. **Milestone 5**: Integration

⚠️ **Warning**: "Layered approach means no working end-to-end functionality until Milestone 5. Integration issues will be discovered late. Consider Agile Gold Path for faster validation."

**Ask**: "Are you sure you want layered approach? This delays working deliverables. [Yes to proceed / No to switch to Agile]"

🛑 **WAIT** → If No, set MILESTONE_STRATEGY to "A" and use Agile Gold Path

**Say**: "I'll plan milestones using Layered approach as requested. Note: First working end-to-end functionality won't be available until integration milestone."

### IF C (Custom Breakdown):

**Ask**: "Describe your preferred milestone breakdown approach. What should each milestone deliver?"

🛑 **WAIT FOR DESCRIPTION** → Store as CUSTOM_STRATEGY

**AI Review**:
- Analyze custom strategy against agile principles:
  - Does it provide early working deliverables?
  - Are there tight feedback loops?
  - Are milestones testable and demonstrable?
  - Is integration validated early?

**Provide Feedback**:
```
📊 CUSTOM STRATEGY REVIEW

Your approach: [summarize CUSTOM_STRATEGY]

Agile principles analysis:
- Early deliverables: [✅ Good / ⚠️ Delayed until Milestone X / ❌ No working app until end]
- Feedback loops: [✅ Tight (each milestone testable) / ⚠️ Loose / ❌ Only at end]
- Integration validation: [✅ Early (Milestone X) / ⚠️ Mid-way / ❌ Late]
- Risk level: [Low / Medium / High]

Recommendations:
[AI suggestions to improve strategy if needed]

Proceed with this custom approach? [Yes / No / Modify]
```

🛑 **WAIT** → Handle choice

**Say**: "I'll plan milestones using your custom approach [with recommended modifications]."

---

## BUILD_MAP GENERATION

Create ${SPEC_PATH}BUILD_MAP.md with milestones following chosen strategy.

Requirements:
- Milestones ordered for a SINGLE developer
- **IMPORTANT**: All time estimates assume **AI-assisted development** (Claude, Cursor, or similar)

- BROWNFIELD INTEGRATION MILESTONES (if existing codebase):
  MILESTONE 0: CONTEXT DISCOVERY (brownfield only, ~20-40 min AI-assisted)
  - Complete brownfield-context-discovery-tdd.md process
  - Generate 00_BROWNFIELD_CONTEXT.md
  - Identify existing patterns, constraints, risks
  - Review with user for accuracy
  - Get user approval of context analysis
  - No code changes, pure exploration
  - AI speedup: Automated codebase scanning, pattern recognition (2x faster)

  MILESTONE 1: ENVIRONMENT + BASELINE (brownfield only, ~20-40 min AI-assisted)
  - Set up local development environment
  - Run existing tests → establish baseline (X% coverage, Y tests passing)
  - Run existing linter → establish baseline (N violations)
  - Run existing build → verify builds successfully
  - Verify can run application locally
  - Document baseline in BUILD_MAP.md
  - No code changes, just verification
  - AI speedup: Automated test/build execution, baseline documentation (1.5x faster)

  MILESTONE 2: INTEGRATION SPIKE (brownfield recommended, ~30-60 min AI-assisted)
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
  - AI speedup: Pattern matching, boilerplate generation (2-3x faster)
  - Manual equivalent: 2-3 hours

  MILESTONE 3+: Feature Implementation
  - Build new features incrementally
  - Each milestone validates no regression (all existing tests pass)
  - Each milestone maintains or improves coverage
  - Each milestone follows patterns from integration spike
  - AI speedup: Code generation, test writing (3-5x faster per milestone)

- MILESTONE DESIGN PRINCIPLES (Applied based on chosen strategy):
  - **Vertical Slices** (Agile Gold Path): Each milestone delivers one feature end-to-end
    - Example GOOD: "Milestone 3: User profile (API + UI + DB working)"
    - Example BAD: "Milestone 3: All API endpoints (no UI)", "Milestone 4: All UI (no backend)"
  - **Working Functionality**: User can stop at any milestone and have a working (if limited) application
  - **Progressive Enhancement**: Core → Enhanced → Polished within each feature
  - **Testable & Demonstrable**: Each milestone can be tested and demoed independently
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

---
**NEXT STEP**: When 10_BUILD_MAP.md is complete and approved, run `full-app-10a-plan-approval-gate-tdd.md` for final plan review and approval.
