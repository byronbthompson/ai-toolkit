Open /specs/02_ARCHITECTURE.md and draft:

GREENFIELD vs BROWNFIELD CHECK:

IF BROWNFIELD (existing codebase):
- Reference 00_BROWNFIELD_CONTEXT.md for existing architecture
- New architecture must INTEGRATE with existing, not replace
- Explicitly document:
  - What exists: [describe current architecture from brownfield context]
  - What's being added: [describe new components/modules]
  - Integration points: [how new connects to existing]
  - Pattern alignment: [confirm new follows existing patterns]
- Architecture diagram should show BOTH existing (gray) and new (color) components
- Constraints section must list existing architecture constraints
- Any deviations from existing patterns require justification in 09_DECISIONS.md

IF GREENFIELD (new project):
- Design architecture from scratch
- No existing constraints to consider
- Full freedom in architectural choices

- High-level architecture diagram (mermaid ok)
  - If brownfield: Show existing components (gray/dashed) + new components (solid/color)
  - If greenfield: Show proposed architecture
- Frontend architecture
- Backend architecture
- Integration points
- Deployment topology
- Environment strategy (dev/test/prod)
- Observability overview
- Version control strategy:
  - Git branching strategy (main/develop, feature branches, etc.)
  - .gitignore requirements for tech stack
  - Commit strategy: commit after each milestone when all quality gates pass
  - Commit message standards (conventional commits recommended)
- Code quality standards:
  - Linter configuration:
    - Python: pylint/flake8/ruff
    - Node.js: eslint
    - Markdown: markdownlint (.markdownlint.json always required)
  - Formatting standards:
    - Indentation: 4 spaces (no tabs)
    - Quotes: single quotes preferred (where language appropriate)
    - Document in linter config files
  - Pre-commit hooks (optional but recommended)
- Dependency Selection and Risk Assessment:
  For each major dependency (frameworks, libraries), evaluate and document:
  - Health indicators:
    - Last release date (prefer <6 months for active projects)
    - GitHub stars/forks (community size)
    - Open issues vs closed ratio
    - Maintainer responsiveness
    - Security advisories (check CVE databases)
    - License compatibility (MIT/Apache preferred)
  - Compatibility verification:
    - Python/Node.js version compatibility
    - Works with chosen OS/deployment platform
    - Ecosystem maturity (plugins, extensions available)
  - Documentation quality:
    - Official docs comprehensive and up-to-date
    - Good examples and tutorials
    - Active community support (Stack Overflow, Discord, etc.)
  - Risk mitigation:
    - Version pinning strategy (exact vs range)
    - Migration path if switching later needed
    - Fallback alternatives identified
  - RED FLAGS (ask user before proceeding):
    - No updates in >1 year (unless stable/complete project)
    - Major unpatched security vulnerabilities
    - Maintainer announced deprecation
    - Incompatible license
    - Requires paid tier for needed features
  - Document in 09_DECISIONS.md:
    - Why chosen over alternatives
    - Known risks or limitations
    - Fallback plan if unmaintained
- Multi-Tenant and White-Label Architecture (if applicable):
  Ask user if application needs to support:
  - Multiple tenants (organizations/customers) sharing same deployment
  - White-labeling (custom branding per tenant)
  - Data isolation requirements (separate schemas, databases, or row-level)
  If YES to any, include in architecture:
  - Tenant identification strategy (subdomain, path, header, JWT claim)
  - Data isolation model:
    - Shared schema with tenant_id (simple, less isolation)
    - Schema per tenant (moderate isolation, PostgreSQL schemas)
    - Database per tenant (maximum isolation, highest complexity)
  - Tenant context propagation (middleware, request context)
  - White-label asset management (logos, colors, themes per tenant)
  - Tenant-specific configuration (features, limits, integrations)
  - Tenant provisioning/onboarding workflow
  - Tenant admin interface requirements
  - Billing/usage tracking per tenant
  - Performance implications (query patterns, connection pooling)
  - Complexity multiplier: Add 50-100% to implementation time estimate
  - Document tenant model in 03_DATA_MODEL.md
  - Record multi-tenancy decision in 09_DECISIONS.md with tradeoffs

If technology stack not yet chosen, ask clarifying questions:
- What are the performance, scalability, and latency requirements?
- What is the team's existing expertise?
- Any infrastructure or deployment constraints?
- Python preferred when: API-heavy, data processing, ML/AI, rapid iteration needed
- Environment management:
  - Version control: git (git init if new project)
  - Python projects: use conda for env management (document conda env setup)
  - Node.js projects: use nvm for version management (document .nvmrc)

Review OFFICIAL framework docs for the stack and call out constraints.
Present 2–3 architecture options with tradeoffs and recommend one.
Record the decision in /specs/09_DECISIONS.md.
Ask ONE blocking question.
No code changes.
