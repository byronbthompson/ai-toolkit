BROWNFIELD CONTEXT DISCOVERY

Use this command when adding features to EXISTING codebases (not greenfield projects).

CRITICAL: Before planning or implementing ANY changes to existing code, complete this discovery process.

PHASE 1: DETERMINE PROJECT TYPE

Ask user:
1. "Is this a new project (greenfield) or adding to existing code (brownfield)?"
2. If brownfield: "What is the root directory of the existing project?"
3. "Are there existing specs/docs I should review first?"

PHASE 2: CODEBASE EXPLORATION (brownfield only)

ARCHITECTURE DISCOVERY:
- [ ] Identify project structure:
  - Use Task tool (Explore agent, thoroughness: "very thorough") to understand:
    - "What is the overall architecture pattern?" (monolith, microservices, layered, etc.)
    - "What are the main directories and their purposes?"
    - "Where are the core business logic files?"
- [ ] Technology stack inventory:
  - Language(s) and versions (check package.json, requirements.txt, go.mod, etc.)
  - Framework(s) in use (check imports, dependencies)
  - Database type and ORM (check config files, migrations directory)
  - Testing framework (check test files, package.json scripts)
  - Build tools (check package.json scripts, Makefile, etc.)
- [ ] Read configuration files:
  - Environment variables (.env.example, config files)
  - Database configuration
  - API configuration (ports, endpoints, middleware)
  - Linter configuration (already exists or need to add?)

EXISTING PATTERNS DISCOVERY:
- [ ] Code style analysis:
  - Indentation style (tabs vs spaces, 2 vs 4)
  - Quote style (single vs double)
  - Naming conventions (camelCase, snake_case, PascalCase)
  - File naming patterns
  - Comment style and documentation patterns
- [ ] Architectural patterns in use:
  - Use Task tool (Explore agent) to find:
    - "How is authentication/authorization implemented?"
    - "What patterns are used for database access?" (repositories, DAOs, direct ORM)
    - "How are API routes/endpoints organized?"
    - "What error handling patterns are used?"
    - "How is configuration managed?"
    - "Are there any middleware or interceptors?"
- [ ] Testing patterns:
  - Use Glob to find test files: **/*test*.{js,ts,py,go}
  - Analyze 2-3 test files to understand:
    - Test structure (describe/it, test classes, etc.)
    - Mocking approach (fixtures, mocks, stubs)
    - Assertion style
    - Test data setup patterns
- [ ] Data model discovery:
  - Find models/entities: Use Grep for "class.*Model", "Schema", "Entity", "Table"
  - Find migrations: Look for migrations directory or migration files
  - Read recent migrations to understand current schema
  - Identify relationships and constraints

INTEGRATION POINTS DISCOVERY:
- [ ] External dependencies:
  - APIs being called (search for http/fetch/requests in code)
  - Third-party services (search for API keys, SDK imports)
  - Database connections (config files, connection strings)
  - Message queues, caches, etc.
- [ ] Internal module dependencies:
  - How do modules import/reference each other?
  - Are there circular dependencies?
  - What's the dependency graph?
- [ ] Entry points:
  - Main application entry (index.js, main.py, main.go, etc.)
  - API server startup
  - Background workers or cron jobs
  - CLI commands

QUALITY BASELINE DISCOVERY:
- [ ] Current test coverage:
  - Run existing coverage command (check package.json, Makefile, README)
  - Document current coverage percentage (this is baseline)
  - Identify untested areas
- [ ] Linter status:
  - Check if linter is configured
  - Run linter to see current violation count
  - Document current linter baseline (can't make it worse)
- [ ] Build status:
  - Run build command (if applicable)
  - Document if build currently passes
  - Note any warnings or errors
- [ ] Existing documentation:
  - README.md (setup instructions, architecture notes)
  - API documentation (Swagger, OpenAPI, etc.)
  - Inline code comments and docstrings
  - Architecture Decision Records (ADRs) if they exist

PHASE 3: CONSTRAINT IDENTIFICATION

Document constraints from existing codebase:

TECHNICAL CONSTRAINTS:
- Language/framework versions (can't easily change)
- Database type and schema (migrations required to change)
- Authentication mechanism (must integrate with existing)
- API versioning strategy (must maintain compatibility)
- Deployment platform (AWS, Azure, on-prem, etc.)
- Performance requirements (based on existing SLAs or monitoring)

PATTERN CONSTRAINTS (must follow existing patterns):
- Code organization (match existing structure)
- Naming conventions (match existing style)
- Error handling (use existing error types/formats)
- Logging (use existing logger, format)
- Configuration (use existing config mechanism)
- Testing approach (match existing test structure)

COMPATIBILITY CONSTRAINTS:
- Backwards compatibility requirements (can't break existing APIs)
- Database migrations (must be reversible)
- Existing user data (must migrate or remain compatible)
- Client/frontend dependencies (if API changes affect clients)
- Third-party integrations (must not break existing integrations)

PHASE 4: RISK ASSESSMENT

Identify risks of making changes:

HIGH RISK AREAS (ask user before modifying):
- Core authentication/authorization logic
- Database schema changes affecting existing data
- Shared utilities used by many modules
- API endpoints with external consumers
- Payment or financial logic
- Data migration or transformation logic

MEDIUM RISK AREAS (proceed with caution):
- New API endpoints (ensure consistent with existing)
- New database tables (ensure no naming conflicts)
- New dependencies (check compatibility with existing)
- Refactoring existing code (ensure tests cover it)

LOW RISK AREAS (safer to modify):
- New features in isolated modules
- Adding new optional parameters to existing functions
- Adding new routes that don't conflict with existing
- Adding tests to untested code

PHASE 5: GENERATE BROWNFIELD CONTEXT DOCUMENT

Create /specs/00_BROWNFIELD_CONTEXT.md with:

```markdown
# Brownfield Context for [Project Name]

## Project Type
- Type: Brownfield (adding to existing codebase)
- Project root: [path]
- Existing codebase age: [if known]

## Current Technology Stack
- Language: [language + version]
- Framework: [framework + version]
- Database: [type + version]
- Testing: [framework]
- Build tool: [tool]
- Linter: [tool + config location]

## Architecture Pattern
[High-level description of current architecture]
- Pattern: [monolith/microservices/layered/etc.]
- Key directories: [list with purposes]
- Entry points: [list]

## Existing Patterns to Follow
### Code Style
- Indentation: [2/4 spaces or tabs]
- Quotes: [single/double]
- Naming: [conventions observed]
- File naming: [patterns observed]

### Architectural Patterns
- Database access: [pattern description]
- API structure: [pattern description]
- Error handling: [pattern description]
- Configuration: [pattern description]
- Authentication: [pattern description]

### Testing Patterns
- Test structure: [describe/it, classes, etc.]
- Mocking: [approach used]
- Test data: [fixtures, factories, etc.]

## Current Quality Baseline
- Test coverage: [X%] (must not decrease)
- Linter status: [N violations] (must not increase)
- Build status: [passing/failing + warnings]

## Integration Points
- External APIs: [list]
- Third-party services: [list]
- Internal dependencies: [key modules]

## Constraints
### Technical Constraints
- [List constraints that can't easily change]

### Pattern Constraints
- [List patterns that must be followed]

### Compatibility Constraints
- [List backwards compatibility requirements]

## High-Risk Areas
- [List areas to avoid or get user approval before modifying]

## Medium-Risk Areas
- [List areas to modify with caution]

## Recommended Approach
Based on existing codebase analysis:
- New code should be added in: [suggested location]
- Tests should follow pattern from: [example file]
- Database changes should use: [migration approach]
- API endpoints should follow: [existing pattern]

## Open Questions
- [List anything unclear about existing codebase]
- [Ask user for clarification on ambiguous patterns]
```

PHASE 6: INTEGRATION STRATEGY

Update 10_BUILD_MAP.md to include integration milestones:

MILESTONE 0 (Brownfield Only): CONTEXT DISCOVERY
- Complete brownfield-context-discovery.md process
- Generate 00_BROWNFIELD_CONTEXT.md
- Review with user for accuracy
- Get user approval before planning new features
- Duration: ~30-60 minutes

MILESTONE 1: ENVIRONMENT SETUP + BASELINE
- Set up local development environment
- Run existing tests (establish baseline)
- Run existing linter (establish baseline)
- Run build (verify can build existing code)
- Verify can run application locally
- Document any setup issues in 00_BROWNFIELD_CONTEXT.md
- Duration: ~30-60 minutes

MILESTONE 2: INTEGRATION SPIKE (Recommended)
- Implement smallest possible integration with existing code
- Example: Add one new endpoint following existing pattern
- Example: Add one new database table with migration
- Verify integration approach works with existing architecture
- Run all existing tests (must still pass)
- Purpose: Validate assumptions before building full feature
- Duration: ~1-2 hours

MILESTONE 3+: Feature Implementation
- Build new features following established patterns
- Each milestone runs existing + new tests
- Each milestone ensures existing coverage doesn't decrease
- Each milestone validates no regression in existing functionality

PHASE 7: VALIDATION REQUIREMENTS (Brownfield)

For EVERY milestone in brownfield projects:

REGRESSION PROTECTION:
- [ ] Run ALL existing tests (not just new tests)
  - ALL existing tests must still pass
  - If existing tests fail, STOP and investigate
  - New code should not break existing functionality
- [ ] Run existing linter
  - New code must pass linter
  - Existing violations should not increase
  - If touching existing files, can improve linter violations
- [ ] Run full build
  - Build must succeed
  - No new build warnings introduced
- [ ] Test existing functionality manually (if no tests)
  - Verify existing features still work
  - Focus on areas adjacent to changes

INTEGRATION VALIDATION:
- [ ] New code follows existing patterns (document in commit message)
- [ ] New code integrates with existing architecture
- [ ] No duplicate functionality (DRY with existing code)
- [ ] Naming consistent with existing conventions
- [ ] Configuration consistent with existing approach

DOCUMENTATION UPDATES:
- [ ] Update README if setup changes
- [ ] Update API docs if endpoints changed
- [ ] Add migration guide if breaking changes (should avoid)
- [ ] Document deviations from existing patterns in 09_DECISIONS.md

ASK USER BEFORE:
- Modifying high-risk areas identified in Phase 4
- Changing existing APIs (breaking changes)
- Major refactoring of existing code
- Adding dependencies that conflict with existing
- Changing authentication or authorization logic

IMPORTANT REMINDERS FOR BROWNFIELD:
1. "When in Rome, do as Romans do" - follow existing patterns even if not ideal
2. Refactoring existing code is separate from adding features (ask user first)
3. Backwards compatibility is critical (existing APIs must keep working)
4. Existing tests passing is a hard requirement (no regressions)
5. Document WHY you deviated from existing patterns (if absolutely necessary)
6. Integration spike (Milestone 2) is highly recommended to validate approach
