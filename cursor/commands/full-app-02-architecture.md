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

---

## PHASE 1: DISCOVERY - Technology Stack (Query First)

Ask ONE question at a time. Wait for answer before proceeding.

### Question 1: Performance and Scalability Requirements

"What are your performance, scalability, and latency requirements?"

**Options to consider**:
- High performance (< 100ms API response, real-time features)
- Standard performance (< 500ms API response, typical web app)
- Batch processing focused (minutes acceptable, data processing)
- Highly scalable (expect millions of users, horizontal scaling critical)
- Moderate scale (thousands of users, vertical scaling sufficient)

🛑 WAIT FOR ANSWER - Record answer before proceeding

### Question 2: Team Expertise

"What is your team's existing expertise and technology preferences?"

**Options to consider**:
- Python (data science, ML, API development)
- Node.js/TypeScript (JavaScript full-stack, real-time apps)
- Java/Spring Boot (enterprise, strong typing)
- Go (high performance, microservices)
- .NET/C# (enterprise, Microsoft ecosystem)
- Mixed team or open to learning new stack

🛑 WAIT FOR ANSWER - Record answer before proceeding

### Question 3: Infrastructure and Deployment Constraints

"Do you have any infrastructure or deployment constraints?"

**Options to consider**:
- Cloud provider preference (AWS/Azure/GCP)
- On-premises requirements
- Containerization required (Docker/Kubernetes)
- Serverless preference (Lambda/Azure Functions)
- Platform constraints (must run on Windows/Linux/macOS)
- Budget constraints (free/open-source only, enterprise budget)

🛑 WAIT FOR ANSWER - Record answer before proceeding

---

## PHASE 2: TECHNOLOGY STACK DECISION

Based on your answers:
- Performance needs: [their answer]
- Team expertise: [their answer]
- Infrastructure constraints: [their answer]

### DECISION REQUIRED: Technology Stack

**Option A - Python with FastAPI** (RECOMMENDED for API-heavy, data processing, ML/AI)
- **Why recommended**: Excellent for rapid development, rich ecosystem, great for data/ML workloads
- **Best for**: Your stated requirements [reference their answers]
- **Pros**:
  - Fast development cycles
  - Extensive libraries (data science, ML, API frameworks)
  - Large community and support
  - Type hints with Pydantic for data validation
- **Cons**:
  - GIL limits CPU-bound multi-threading
  - Not as fast as compiled languages (Go, Rust)
  - Package management can be complex
- **Reference**: https://fastapi.tiangolo.com/ (modern, high-performance framework)
- **Environment management**: conda (recommended) or venv

**Option B - Node.js with TypeScript/Express**
- **Best for**: JavaScript full-stack teams, I/O-heavy workloads, real-time features
- **Pros**:
  - JavaScript everywhere (frontend + backend)
  - Excellent for I/O-bound operations
  - Large npm ecosystem
  - WebSocket support built-in
- **Cons**:
  - Callback/async complexity
  - Less type safety than Python (even with TypeScript)
  - npm dependency hell (large node_modules)
- **Reference**: https://expressjs.com/
- **Environment management**: nvm for Node version management

**Option C - [Other stack based on team expertise]**
- [Customize based on their answer to Question 2]

**Question**: "Which technology stack do you prefer? (A/B/C or describe alternative)"

🛑 WAIT FOR CONFIRMATION - Do not proceed until explicit choice is made

Once confirmed, record decision in /specs/09_DECISIONS.md with reasoning.

---

## PHASE 3: MULTI-TENANCY DECISION (Query First)

### Question 4: Multi-Tenancy Requirements

"Does your application need to support multiple tenants (organizations/customers sharing the same deployment)?"

**Context**: Multi-tenancy adds significant complexity (50-100% more implementation time) but enables SaaS business models.

**Options**:
- Yes, multiple tenants required
- No, single tenant only
- Unsure - describe your use case

🛑 WAIT FOR ANSWER

**IF YES to multi-tenancy, ask follow-up questions:**

### Question 5: Data Isolation Requirements

"What level of data isolation do you require between tenants?"

**Option A - Shared schema with tenant_id** (RECOMMENDED for cost optimization)
- **Why recommended**: Simplest implementation, lowest infrastructure cost, good performance
- **Best for**: SaaS apps where tenants trust the platform, no regulatory isolation requirements
- **Pros**:
  - Simple to implement and maintain
  - Low infrastructure cost (single database)
  - Easy to add features across all tenants
  - Good query performance with proper indexing
- **Cons**:
  - Risk of data leakage if queries miss tenant_id filter
  - All tenants affected by database issues
  - Less appealing to enterprise customers
- **Security**: Row-level security (RLS) policies required
- **Implementation effort**: Low (1-2 days)

**Option B - Schema per tenant** (PostgreSQL schemas)
- **Best for**: Moderate isolation needs, compliance requirements, tenant-specific customization
- **Pros**:
  - Better isolation than shared schema
  - Tenant-specific migrations possible
  - Easier backup/restore per tenant
  - Some regulatory compliance scenarios
- **Cons**:
  - Connection pooling complexity
  - Schema count limits (PostgreSQL: 100-1000s schemas practical)
  - Migrations must run across all schemas
- **Implementation effort**: Medium (3-5 days)

**Option C - Database per tenant** (MAXIMUM ISOLATION)
- **Best for**: Strict regulatory requirements (HIPAA, financial), enterprise customers, geographic data residency
- **Pros**:
  - Complete data isolation
  - Tenant-specific performance tuning
  - Easy to move tenants between servers
  - Satisfies most compliance requirements
- **Cons**:
  - High infrastructure cost (database per tenant)
  - Complex management (migrations, monitoring, backups)
  - Connection pool exhaustion risk
  - Difficult to run cross-tenant analytics
- **Implementation effort**: High (1-2 weeks)

**Question**: "Which data isolation model do you prefer? (A/B/C)"

🛑 WAIT FOR CONFIRMATION

### Question 6: White-Label Requirements

"Do you need white-labeling (custom branding per tenant)?"

**Options**:
- Yes, full white-label support (custom logos, colors, domains, themes)
- Partial customization (limited branding options)
- No, single brand across all tenants

🛑 WAIT FOR ANSWER

**IF YES to white-labeling:**

### DECISION REQUIRED: Tenant Identification Strategy

**Option A - JWT claim + subdomain** (RECOMMENDED for white-label)
- **Why recommended**: Supports custom domains, secure, scalable
- **Example**: tenant1.yourapp.com, tenant2.yourapp.com, custom-domain.com
- **Pros**:
  - SEO-friendly (each tenant has own domain)
  - Supports custom domains for white-label
  - Browser isolation (cookies don't leak between tenants)
  - Professional appearance
- **Cons**:
  - DNS management complexity
  - SSL certificate management (wildcard or per-tenant)
  - Load balancer configuration
- **Implementation effort**: Medium (3-5 days)

**Option B - Path-based** (/tenant/{tenant_id}/...)
- **Best for**: Simpler deployments, internal multi-tenant tools
- **Pros**:
  - No DNS management
  - Single domain, single SSL cert
  - Easy to test locally
- **Cons**:
  - No custom domain support (limits white-labeling)
  - URL includes tenant ID (less professional)
  - SEO challenges (all under one domain)
- **Implementation effort**: Low (1-2 days)

**Option C - Header-based** (X-Tenant-ID header)
- **Best for**: API-only applications, mobile apps
- **Pros**: Clean URLs, simple for API clients
- **Cons**: Not suitable for web apps, custom domain support requires proxy
- **Implementation effort**: Low (1-2 days)

**Question**: "Which tenant identification strategy do you prefer?"

🛑 WAIT FOR CONFIRMATION

**Record all multi-tenancy decisions in /specs/09_DECISIONS.md with**:
- Data isolation model chosen and why
- Tenant identification strategy
- White-label requirements
- Complexity and time impact acknowledged
- Security implications
- Performance considerations

**Document tenant model details in /specs/03_DATA_MODEL.md**

---

## PHASE 4: DEPENDENCY SELECTION (Query First)

For each major dependency (web framework, database, ORM, etc.), present options:

### Template for Each Dependency Decision

**DECISION REQUIRED: [Dependency Type - e.g., Database]**

**Context**: We need to choose [what this dependency is for]

**Evaluation criteria applied**:
- Last release date
- Community health (stars, issues, maintainer responsiveness)
- Security advisories
- License compatibility
- Documentation quality
- Compatibility with chosen stack

**Option A - [Library Name]** (RECOMMENDED)
- **Last updated**: [date]
- **Health**: [GitHub stars, active maintainers, recent releases]
- **License**: [MIT/Apache/etc.]
- **Why recommended**: [Industry standard, best fit for requirements, proven at scale]
- **Pros**: [specific benefits]
- **Cons**: [honest tradeoffs]
- **Security**: [CVE check results]
- **Documentation**: [quality assessment]
- **Fallback plan**: If unmaintained, migrate to [Alternative] (estimated [X days] effort)
- **Reference**: [Official docs link]

**Option B - [Alternative Library]**
- [Same evaluation format]

**Option C - [Another Alternative]**
- [Same evaluation format]

**🚨 RED FLAGS DETECTED** (if applicable):
- [ ] No updates in >1 year
- [ ] Major unpatched security vulnerabilities: [CVE-XXXX-XXXX]
- [ ] Maintainer announced deprecation
- [ ] Incompatible license
- [ ] Requires paid tier for needed features: [details]

**Question**: "Which [dependency type] do you prefer? (A/B/C or suggest alternative)"

🛑 WAIT FOR CONFIRMATION

**If RED FLAGS present, explicitly ask**: "I found concerning issues with [library]. Are you comfortable proceeding, or should we choose a different option?"

Record in /specs/09_DECISIONS.md:
- Dependency chosen
- Why chosen over alternatives
- Known risks or limitations
- Fallback plan if unmaintained
- Version pinning strategy

---

## PHASE 5: ARCHITECTURE DESIGN

Now that key decisions are confirmed, design architecture:

### Architecture Components to Document

- High-level architecture diagram (mermaid ok)
  - If brownfield: Show existing components (gray/dashed) + new components (solid/color)
  - If greenfield: Show proposed architecture
- Frontend architecture (if applicable)
- Backend architecture
- Integration points
- Deployment topology
- Environment strategy (dev/test/prod)
- Observability overview

### Version Control Strategy (Defaults with Recommendations)

**RECOMMENDED DEFAULTS** (can be customized):

- **Git branching strategy**:
  - Default: main branch + feature branches (GitHub Flow)
  - Alternative: main + develop + feature branches (Git Flow) if you need release branches
  - Question: "Use simple main+feature or main+develop+feature?"

- **Commit strategy**:
  - Commit after each milestone when all quality gates pass
  - Atomic commits (one logical change per commit)

- **Commit message standards** (RECOMMENDED):
  - Conventional Commits format
  - Example: `feat: add user authentication`, `fix: resolve login timeout`
  - Reference: https://www.conventionalcommits.org/

- **.gitignore requirements**:
  - Language-specific (Python: `*.pyc`, `__pycache__/`, `.venv/`)
  - IDE-specific (`.vscode/`, `.idea/`)
  - Environment files (`.env`, `.env.local`)
  - Generated based on chosen stack

**Question**: "Accept recommended version control strategy or customize?"

🛑 WAIT FOR CONFIRMATION (if customization requested)

### Code Quality Standards (Defaults with Recommendations)

**RECOMMENDED DEFAULTS**:

- **Linter configuration**:
  - Python: `ruff` (modern, fast) or `pylint`/`flake8` (traditional)
  - Node.js: `eslint` with TypeScript support
  - Markdown: `markdownlint` (.markdownlint.json REQUIRED)

- **Formatting standards**:
  - Indentation: 4 spaces (Python, JavaScript, JSON)
  - Quotes: single quotes (JavaScript), either for Python (consistent within project)
  - Line length: 100 characters (Python PEP 8 allows 79-100)
  - Trailing commas: yes (easier diffs)

- **Code formatter** (RECOMMENDED):
  - Python: `black` (opinionated, zero config) or `ruff format`
  - JavaScript/TypeScript: `prettier`
  - Auto-format on save (VS Code/Cursor setting)

- **Pre-commit hooks** (RECOMMENDED but optional):
  - Run linter and formatter before commit
  - Catch issues early
  - Setup: `pre-commit` framework (Python) or `husky` (Node.js)

**Question**: "Accept recommended code quality defaults or customize?"

🛑 WAIT FOR CONFIRMATION (if customization requested)

Document chosen standards in linter config files (created during implementation).

---

## PHASE 6: ARCHITECTURE OPTIONS PRESENTATION

After gathering all requirements and confirming key decisions, present 2-3 complete architecture options:

### ARCHITECTURE OPTION 1: [Name] (RECOMMENDED)

**Description**: [High-level overview]

**Components**:
- Frontend: [Technology and approach]
- Backend: [Technology and approach]
- Database: [Technology and approach]
- Infrastructure: [Deployment approach]

**Pros**:
- [Benefit 1]
- [Benefit 2]
- [Benefit 3]

**Cons**:
- [Tradeoff 1]
- [Tradeoff 2]

**Best for**: [Your specific requirements as stated]

**Estimated complexity**: [Simple/Moderate/Complex]

**Implementation effort**: [X weeks]

**Diagram**: [Mermaid or reference to diagram]

### ARCHITECTURE OPTION 2: [Alternative Name]

[Same format]

### ARCHITECTURE OPTION 3: [Another Alternative]

[Same format]

---

## 🛑 FINAL ARCHITECTURE APPROVAL

**Summary of Decisions Made**:
1. Technology stack: [Confirmed choice]
2. Multi-tenancy: [Yes/No, model if yes]
3. Key dependencies: [List with versions]
4. Code quality approach: [Linters, formatters chosen]

**Recommended Architecture**: Option 1 - [Name]

**Reasoning**: [Why this is the best fit based on all gathered requirements]

**Points of No Easy Return**:
- Database choice: [Chosen DB] - Migration to different DB later requires significant effort
- Multi-tenancy model: [If applicable] - Fundamental architecture decision, hard to change
- Technology stack: [Chosen stack] - Rewriting in different language is costly

**Question**: "Does this architecture align with your vision? Approve Option 1 or select different option (1/2/3)?"

🛑 WAIT FOR FINAL APPROVAL

**If approved**: Record final decision in /specs/09_DECISIONS.md with full rationale.

**If modifications requested**: Return to relevant decision point and re-query.

---

## DELIVERABLES

After approval, create /specs/02_ARCHITECTURE.md containing:

- All confirmed decisions from this workflow
- Architecture diagrams
- Technology stack with versions
- Dependency list with rationale
- Multi-tenancy model (if applicable)
- Code quality standards
- Version control strategy
- References to official documentation for all major components

**No code changes at this stage** - only documentation and decisions.

Ask ONE final blocking question if any ambiguity remains.
