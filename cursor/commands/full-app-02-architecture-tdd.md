Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}02_ARCHITECTURE.md and draft:

GREENFIELD vs BROWNFIELD CHECK:

IF BROWNFIELD (existing codebase):
- Reference ${SPEC_PATH}00_BROWNFIELD_CONTEXT.md for existing architecture
- New architecture must INTEGRATE with existing, not replace
- Explicitly document:
  - What exists: [describe current architecture from brownfield context]
  - What's being added: [describe new components/modules]
  - Integration points: [how new connects to existing]
  - Pattern alignment: [confirm new follows existing patterns]
- Architecture diagram should show BOTH existing (gray) and new (color) components
- Constraints section must list existing architecture constraints
- Any deviations from existing patterns require justification in ${SPEC_PATH}09_DECISIONS.md

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

Once confirmed, record decision in ${SPEC_PATH}09_DECISIONS.md with reasoning.

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

**Record all multi-tenancy decisions in ${SPEC_PATH}09_DECISIONS.md with**:
- Data isolation model chosen and why
- Tenant identification strategy
- White-label requirements
- Complexity and time impact acknowledged
- Security implications
- Performance considerations

**Document tenant model details in ${SPEC_PATH}03_DATA_MODEL.md**

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

Record in ${SPEC_PATH}09_DECISIONS.md:
- Dependency chosen
- Why chosen over alternatives
- Known risks or limitations
- Fallback plan if unmaintained
- Version pinning strategy

---

## PHASE 5: INFRASTRUCTURE & HOSTING DECISION (Query First)

**CRITICAL**: Infrastructure decisions are "points of no easy return" - changing platforms later is expensive.

### Question 1: Deployment Environment

"Where will this application be deployed?"

**Option A - Cloud Platform** (RECOMMENDED for most applications)
- **AWS**: Industry standard, most mature ecosystem
  - Pros: Richest service catalog, excellent documentation, largest community
  - Cons: Complex pricing, can be expensive without optimization, steep learning curve
  - Best for: Enterprise apps, need for specialized services (ML, IoT, etc.)
  - Effort: Medium (many services to learn)
  - Reference: https://aws.amazon.com/

- **Azure**: Strong for .NET/Microsoft stack, enterprise integration
  - Pros: Excellent Microsoft integration, good for hybrid cloud, enterprise support
  - Cons: Less mature than AWS in some areas, pricing complexity
  - Best for: Microsoft shops, enterprise with Active Directory, hybrid scenarios
  - Effort: Medium
  - Reference: https://azure.microsoft.com/

- **Google Cloud (GCP)**: Strong data/ML capabilities, clean APIs
  - Pros: Best-in-class data analytics/ML, simpler pricing, modern architecture
  - Cons: Smaller ecosystem than AWS, fewer enterprise features
  - Best for: Data-heavy apps, ML/AI workloads, startups
  - Effort: Low-Medium (cleaner than AWS/Azure)
  - Reference: https://cloud.google.com/

- **Multi-Cloud**: Use multiple providers
  - Pros: Avoid vendor lock-in, leverage best-of-breed services
  - Cons: Much higher complexity, need expertise in multiple platforms
  - Best for: Large enterprises with compliance requirements
  - Effort: High (managing multiple platforms)
  - NOT RECOMMENDED for most projects

**Option B - Platform-as-a-Service (PaaS)** (RECOMMENDED for rapid deployment)
- **Vercel**: Best for Next.js/React, edge functions
  - Pros: Zero-config deployment, excellent DX, global CDN, free tier generous
  - Cons: Vendor lock-in, expensive at scale, limited to web apps
  - Best for: Frontend apps, JAMstack, Next.js projects
  - Cost: Free tier available, $20+/month pro
  - Reference: https://vercel.com/

- **Netlify**: Best for static sites, Jamstack
  - Pros: Great for static sites, serverless functions, form handling
  - Cons: Less suitable for full-stack apps, vendor lock-in
  - Best for: Static sites, documentation, marketing pages
  - Cost: Free tier available, $19+/month pro
  - Reference: https://www.netlify.com/

- **Render**: Heroku alternative, full-stack friendly
  - Pros: Simpler than cloud platforms, good for full-stack, auto-deploy from Git
  - Cons: Less mature, fewer services than major clouds
  - Best for: Full-stack web apps, APIs, databases
  - Cost: Free tier available, $7+/month
  - Reference: https://render.com/

- **Railway**: Modern PaaS, great DX
  - Pros: Simple deployment, good pricing, PostgreSQL included
  - Cons: Newer platform, smaller ecosystem
  - Best for: Side projects, startups, quick deploys
  - Cost: $5+/month usage-based
  - Reference: https://railway.app/

- **Fly.io**: Global edge deployment
  - Pros: Run apps globally, good for low-latency, flexible
  - Cons: More complex than other PaaS
  - Best for: Global apps, low-latency requirements
  - Cost: Pay-as-you-go
  - Reference: https://fly.io/

**Option C - Self-Hosted / On-Premises**
- **Own servers**: VPS (DigitalOcean, Linode, Hetzner)
  - Pros: Full control, predictable costs, no vendor lock-in
  - Cons: YOU manage infrastructure, security, scaling, backups
  - Best for: Cost-sensitive, full control needed, regulatory requirements
  - Effort: High (infrastructure management)
  - NOT RECOMMENDED unless specific requirement

- **On-premises (company data center)**
  - Pros: Data stays in-house, regulatory compliance
  - Cons: High capex, slow provisioning, limited scalability
  - Best for: Regulatory requirements, existing data center
  - Effort: Very High
  - ONLY if mandated by policy

**Option D - Serverless** (RECOMMENDED for event-driven, variable workloads)
- **AWS Lambda + API Gateway**
  - Pros: Pay per use, auto-scaling, no servers to manage
  - Cons: Cold starts, vendor lock-in, debugging complexity
  - Best for: APIs, event processing, variable traffic
  - Cost: Free tier generous, pay per invocation after
  - Reference: https://aws.amazon.com/lambda/

- **Cloudflare Workers**: Edge serverless
  - Pros: Global edge network, very fast cold starts, generous free tier
  - Cons: V8 isolates (not full Node.js), limited execution time
  - Best for: Edge functions, API proxies, low-latency needs
  - Cost: Free tier available
  - Reference: https://workers.cloudflare.com/

🛑 WAIT FOR ANSWER

Record in ${SPEC_PATH}09_DECISIONS.md:
- Hosting platform chosen
- Why chosen (aligns with team expertise, budget, requirements)
- Cost implications ($X/month estimated)
- Lock-in risks and mitigation
- Rollback plan if platform doesn't work out

---

### Question 2: Container Strategy

"Do you need containerization (Docker/Kubernetes)?"

**Option A - Containers (Docker)** (RECOMMENDED for most projects)
- **Why recommended**: Consistent environments, easier deployment, better portability
- **Best for**: Any application going to production
- **Pros**:
  - Dev/prod parity (works the same everywhere)
  - Easy to add services (databases, caching)
  - Portable across cloud providers
  - Industry standard
- **Cons**:
  - Learning curve for Docker
  - Additional complexity for simple apps
  - Build time overhead
- **Effort**: Low-Medium (1-2 days to set up)
- **Cost**: Free (just run Docker locally and on platforms)
- **Reference**: https://docs.docker.com/

**Option B - Kubernetes (K8s)** (for complex/enterprise)
- **Best for**: Microservices, high-scale, enterprise requirements
- **Pros**:
  - Powerful orchestration, auto-scaling, self-healing
  - Industry standard for large-scale deployments
  - Multi-cloud portability
- **Cons**:
  - VERY complex (steep learning curve)
  - Overkill for small/medium apps
  - High operational overhead
  - Expensive (managed K8s or DevOps team needed)
- **Effort**: High (1-2 weeks to learn, ongoing management)
- **Cost**: Managed K8s $70+/month (GKE, EKS, AKS)
- **NOT RECOMMENDED** unless you have:
  - 10+ microservices
  - Team with K8s expertise
  - High-scale requirements (1000+ RPS)
- **Reference**: https://kubernetes.io/

**Option C - No containers**
- **Best for**: Very simple apps, serverless deployments, learning projects
- **Pros**: Simpler, faster to start
- **Cons**: Environment inconsistency ("works on my machine"), harder to replicate
- **NOT RECOMMENDED** for production apps

🛑 WAIT FOR CONFIRMATION

---

### Question 3: CI/CD Pipeline

"What CI/CD platform should we use for automated testing and deployment?"

**Context**: CI/CD is critical for quality, speed, and reliability. Automates:
- Running tests on every commit
- Deploying to staging/production
- Preventing broken code from reaching production

**Option A - GitHub Actions** (RECOMMENDED if using GitHub)
- **Why recommended**: Native GitHub integration, generous free tier, simple YAML config
- **Best for**: Any project using GitHub
- **Pros**:
  - Integrated with GitHub (no separate login)
  - Free for public repos, 2000 min/month free for private
  - Easy to set up (`.github/workflows/` directory)
  - Large marketplace of pre-built actions
  - Excellent documentation
- **Cons**:
  - Tied to GitHub
  - Can get expensive for heavy usage (minutes)
- **Effort**: Low (1-2 hours to set up basic pipeline)
- **Cost**: Free for public, $0.008/minute after free tier
- **Reference**: https://docs.github.com/actions

**Option B - GitLab CI/CD** (if using GitLab)
- **Best for**: GitLab users, self-hosted needs
- **Pros**:
  - Excellent CI/CD capabilities
  - Built into GitLab
  - Self-hosted option available
  - Good documentation
- **Cons**:
  - Requires GitLab (not GitHub/Bitbucket)
- **Effort**: Low-Medium
- **Cost**: Free tier available
- **Reference**: https://docs.gitlab.com/ee/ci/

**Option C - CircleCI** (mature, powerful)
- **Best for**: Complex pipelines, need advanced features
- **Pros**:
  - Very mature, powerful, good performance
  - Works with GitHub/Bitbucket
  - Good free tier
- **Cons**:
  - More complex than GitHub Actions
  - Separate platform to manage
- **Effort**: Medium
- **Cost**: Free tier available, $15+/month
- **Reference**: https://circleci.com/docs/

**Option D - Cloud-Native CI/CD**
- **AWS CodePipeline** (if all-in on AWS)
- **Azure DevOps** (if all-in on Azure)
- **Google Cloud Build** (if all-in on GCP)
- **Best for**: Deep integration with specific cloud
- **Cons**: Vendor lock-in, more complex than GitHub Actions
- **ONLY if already committed to that cloud**

**Option E - Self-Hosted (Jenkins, Drone)**
- **Best for**: Air-gapped environments, full control needed
- **Cons**: YOU manage the CI/CD infrastructure
- **Effort**: Very High
- **NOT RECOMMENDED** unless required by policy

🛑 WAIT FOR CONFIRMATION

Record in ${SPEC_PATH}09_DECISIONS.md:
- CI/CD platform chosen
- Why chosen
- Cost estimate
- What will be automated (tests, linting, deployment, coverage)

---

### Question 4: Environment Strategy

"How many environments do you need (dev/staging/production)?"

**Option A - Dev + Staging + Production** (RECOMMENDED for production apps)
- **Why recommended**: Safe deployment path, catch issues before prod
- **Environments**:
  - **Development**: Local development, rapid iteration
  - **Staging**: Production-like environment for testing
  - **Production**: Live users, stable, monitored
- **Workflow**: Dev → Staging (auto-deploy on merge to main) → Production (manual approval)
- **Best for**: Production applications with users
- **Cost**: Staging + Production infrastructure (2x hosting cost)
- **Effort**: Medium (1-2 days to set up)

**Option B - Dev + Production** (acceptable for small apps)
- **Best for**: Side projects, MVPs, internal tools
- **Environments**:
  - **Development**: Local + preview deployments
  - **Production**: Live environment
- **Workflow**: Dev → Production (deploy on manual approval)
- **Pros**: Lower cost (no staging environment)
- **Cons**: Higher risk (no staging to catch issues)
- **Cost**: Production infrastructure only
- **Effort**: Low

**Option C - Dev + Staging + Production + Preview** (for teams)
- **Best for**: Teams with multiple features in parallel
- **Environments**:
  - **Development**: Local
  - **Preview**: Auto-deployed per PR/feature branch
  - **Staging**: Pre-production testing
  - **Production**: Live
- **Pros**: Test each feature independently before merge
- **Cons**: Higher cost (preview environments)
- **Cost**: Production + Staging + ephemeral preview environments
- **Platforms with free preview**: Vercel, Netlify, Render
- **Effort**: Medium-High

🛑 WAIT FOR CONFIRMATION

---

### Question 5: Observability & Monitoring

"What monitoring and logging do you need?"

**Context**: You can't improve what you don't measure. Monitoring is essential for production.

**Option A - Basic Monitoring** (RECOMMENDED minimum for production)
- **Metrics**:
  - Application uptime/health checks
  - Error rates (500s, exceptions)
  - Response times (p50, p95, p99)
- **Logging**:
  - Application logs (errors, warnings)
  - Request logs (access logs)
- **Alerting**:
  - Email/Slack on critical errors
  - Downtime alerts
- **Tools** (pick based on platform):
  - **Self-hosted**: Prometheus + Grafana (free, powerful, complex)
  - **Cloud platforms**: CloudWatch (AWS), Azure Monitor, Google Cloud Monitoring (built-in)
  - **PaaS**: Built-in dashboards (Vercel Analytics, Render metrics)
  - **SaaS**: Better Stack (formerly Logtail), free tier available
- **Effort**: Low (1-2 hours to set up)
- **Cost**: Free (built-in) to $10-30/month (SaaS)

**Option B - APM (Application Performance Monitoring)** (for production apps)
- **Best for**: Apps with performance requirements, need to track user experience
- **Capabilities**: Everything in Basic + distributed tracing, user sessions, performance profiling
- **Tools**:
  - **Sentry**: Error tracking + performance (RECOMMENDED for most)
    - Free tier: 5K events/month
    - Cost: $26+/month team plan
    - Reference: https://sentry.io/
  - **DataDog**: Full observability (metrics, logs, traces, APM)
    - Very powerful, expensive
    - Best for: Enterprise
    - Cost: $15+/host/month
  - **New Relic**: Full observability platform
    - Good free tier (100GB/month)
    - Cost: Pay-as-you-go after free tier
  - **Elastic APM**: Self-hosted APM (free, complex to set up)
- **Effort**: Medium (1-2 days to integrate)
- **Cost**: $0-100+/month depending on scale

**Option C - Enterprise Observability** (for critical systems)
- **Best for**: Financial services, healthcare, high-scale SaaS
- **Capabilities**: Full stack observability, compliance, audit logs
- **Tools**: DataDog, Dynatrace, Splunk (expensive, powerful)
- **Effort**: High
- **Cost**: $500+/month
- **NOT RECOMMENDED** unless compliance requires

🛑 WAIT FOR CONFIRMATION

Record in ${SPEC_PATH}09_DECISIONS.md:
- Monitoring strategy chosen
- Tools/platforms
- What will be monitored (uptime, errors, performance)
- Alerting setup (who gets notified, when)
- Cost estimate

---

## PHASE 6: ARCHITECTURE DESIGN

Now that ALL key decisions are confirmed (tech stack, dependencies, infrastructure, CI/CD), design architecture:

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

### Architecture Principles & Best Practices

**IMPORTANT**: Following these principles results in maintainable, testable, and scalable code. AI agents (Claude, Cursor) can help enforce these patterns consistently.

#### SOLID Principles (Object-Oriented Design)

**S — Single Responsibility Principle (SRP)**
- **Definition**: A class should have only one reason to change (one responsibility)
- **Why it matters**: Easier to understand, test, and modify
- **Example (Python)**:
  ```python
  # BAD: Multiple responsibilities
  class User:
      def save_to_db(self): pass
      def send_welcome_email(self): pass
      def generate_report(self): pass

  # GOOD: Single responsibility
  class User:
      pass  # Just user data/behavior

  class UserRepository:
      def save(self, user): pass

  class EmailService:
      def send_welcome(self, user): pass

  class ReportGenerator:
      def generate_user_report(self, user): pass
  ```
- **AI Prompt Tip**: "Create separate classes for data, persistence, and business logic following SRP"

**O — Open/Closed Principle (OCP)**
- **Definition**: Open for extension, closed for modification
- **Why it matters**: Add features without changing existing code (reduces bugs)
- **Example (TypeScript)**:
  ```typescript
  // BAD: Modifying existing code for new shapes
  class AreaCalculator {
      calculateArea(shape: any): number {
          if (shape.type === 'circle') return Math.PI * shape.radius ** 2;
          if (shape.type === 'square') return shape.side ** 2;
          // Every new shape requires modifying this method
      }
  }

  // GOOD: Open for extension via interfaces
  interface Shape {
      calculateArea(): number;
  }

  class Circle implements Shape {
      constructor(private radius: number) {}
      calculateArea(): number { return Math.PI * this.radius ** 2; }
  }

  class Square implements Shape {
      constructor(private side: number) {}
      calculateArea(): number { return this.side ** 2; }
  }

  class AreaCalculator {
      calculateArea(shape: Shape): number {
          return shape.calculateArea();  // No modification needed for new shapes
      }
  }
  ```
- **AI Prompt Tip**: "Design using interfaces/abstract classes so I can add new implementations without changing existing code"

**L — Liskov Substitution Principle (LSP)**
- **Definition**: Subtypes must be substitutable for their base types without breaking the program
- **Why it matters**: Ensures inheritance is used correctly
- **Example (Python)**:
  ```python
  # BAD: Subclass breaks expectations
  class Bird:
      def fly(self): return "Flying"

  class Penguin(Bird):
      def fly(self): raise Exception("Penguins can't fly!")  # Violates LSP

  # GOOD: Proper abstraction
  class Bird:
      def move(self): pass

  class FlyingBird(Bird):
      def move(self): return "Flying"
      def fly(self): return "Flying"

  class Penguin(Bird):
      def move(self): return "Swimming"
  ```
- **AI Prompt Tip**: "Ensure subclasses can replace base class without breaking behavior"

**I — Interface Segregation Principle (ISP)**
- **Definition**: Clients shouldn't be forced to depend on interfaces they don't use
- **Why it matters**: Smaller, focused interfaces are easier to implement
- **Example (TypeScript)**:
  ```typescript
  // BAD: Fat interface
  interface Worker {
      work(): void;
      eat(): void;
      sleep(): void;
  }

  class Robot implements Worker {
      work(): void { /* ... */ }
      eat(): void { /* Robots don't eat! */ }
      sleep(): void { /* Robots don't sleep! */ }
  }

  // GOOD: Segregated interfaces
  interface Workable {
      work(): void;
  }

  interface Eatable {
      eat(): void;
  }

  interface Sleepable {
      sleep(): void;
  }

  class Human implements Workable, Eatable, Sleepable {
      work(): void { /* ... */ }
      eat(): void { /* ... */ }
      sleep(): void { /* ... */ }
  }

  class Robot implements Workable {
      work(): void { /* ... */ }
  }
  ```
- **AI Prompt Tip**: "Create small, focused interfaces instead of one large interface"

**D — Dependency Inversion Principle (DIP)**
- **Definition**: Depend on abstractions, not concretions (high-level modules shouldn't depend on low-level modules)
- **Why it matters**: Easier to swap implementations, better testability
- **Example (Python)**:
  ```python
  # BAD: High-level depends on low-level
  class MySQLDatabase:
      def save(self, data): pass

  class UserService:
      def __init__(self):
          self.db = MySQLDatabase()  # Tightly coupled to MySQL

  # GOOD: Depend on abstraction
  from abc import ABC, abstractmethod

  class Database(ABC):
      @abstractmethod
      def save(self, data): pass

  class MySQLDatabase(Database):
      def save(self, data): pass

  class PostgreSQLDatabase(Database):
      def save(self, data): pass

  class UserService:
      def __init__(self, db: Database):  # Depends on abstraction
          self.db = db

  # Easy to swap: UserService(MySQLDatabase()) or UserService(PostgreSQLDatabase())
  ```
- **AI Prompt Tip**: "Use dependency injection with interfaces so implementations can be swapped"

#### Additional Best Practices

**DRY (Don't Repeat Yourself)**
- Extract duplicated code into reusable functions/classes
- Use inheritance, composition, or utility functions
- **AI advantage**: AI excels at identifying duplication and refactoring

**KISS (Keep It Simple, Stupid)**
- Prefer simple solutions over clever ones
- Write code for humans to read, not just machines
- **AI advantage**: AI can generate simple implementations when prompted clearly

**YAGNI (You Aren't Gonna Need It)**
- Don't build features until you need them
- Avoid over-engineering for hypothetical future requirements
- **Particularly important with AI**: Don't ask AI to generate "flexible" code unless you have concrete extension needs

**Separation of Concerns**
- Separate data access, business logic, and presentation
- Follow layered architecture (controller → service → repository)
- **AI Prompt Tip**: "Separate data layer, business logic, and API routes into different modules"

**Composition over Inheritance**
- Favor composing objects over class inheritance
- More flexible, easier to test
- **Example**: Instead of `class AdminUser extends User`, use `class User { permissions: Permissions }`

#### Applying Principles in This Project

**Question**: "Do you want to enforce SOLID principles strictly, or apply them pragmatically?"

**Option A - Strict SOLID** (RECOMMENDED for enterprise/complex apps)
- All dependencies injected via interfaces
- Every class has single responsibility
- Full abstraction layers for all external services
- **Best for**: Large codebases, teams, long-term maintenance
- **Effort**: +20-30% initial development time
- **Benefit**: Much easier to maintain, test, and extend

**Option B - Pragmatic SOLID** (RECOMMENDED for MVPs/small apps)
- Apply SOLID where it provides clear value
- Skip abstraction for simple, stable dependencies (e.g., don't abstract `console.log`)
- Use interfaces for external services (databases, APIs)
- **Best for**: Startups, MVPs, small teams
- **Effort**: Minimal overhead
- **Benefit**: Clean code without over-engineering

**Option C - Relaxed** (NOT RECOMMENDED except for prototypes)
- Focus on working code first, refactor later
- Only for throwaway prototypes or learning exercises
- **Risk**: Technical debt accumulates fast

🛑 WAIT FOR CONFIRMATION

Record in ${SPEC_PATH}09_DECISIONS.md:
- Architecture principles approach chosen (Strict/Pragmatic/Relaxed)
- Specific patterns to follow (e.g., Repository pattern for data access, Service layer for business logic)
- Rationale for choice

#### Code Organization Patterns

Based on chosen tech stack, recommend standard patterns:

**For Python (Django/Flask/FastAPI)**:
```
/src
  /models       # Data models (ORM or Pydantic)
  /repositories # Data access layer (SRP, DIP)
  /services     # Business logic (SRP)
  /api          # Controllers/routes (thin layer)
  /utils        # Shared utilities
```

**For Node.js/TypeScript (Express/NestJS)**:
```
/src
  /entities     # Domain models
  /repositories # Data access (DIP)
  /services     # Business logic (SRP)
  /controllers  # Request handlers (thin)
  /middlewares  # Cross-cutting concerns
  /types        # TypeScript interfaces (ISP)
```

**For React/Next.js Frontend**:
```
/src
  /components   # UI components (SRP - one component, one responsibility)
  /hooks        # Reusable logic (custom hooks)
  /services     # API clients (DIP)
  /utils        # Pure functions
  /types        # TypeScript interfaces
```

**AI Implementation Note**: When implementing milestones, AI will:
- Generate code following chosen principles (Strict/Pragmatic)
- Create interfaces/abstractions per DIP
- Separate concerns per SRP
- Use composition and dependency injection
- Provide refactoring suggestions if principles are violated

---

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

## PHASE 7: ARCHITECTURE OPTIONS PRESENTATION

After gathering all requirements and confirming ALL key decisions (tech stack, multi-tenancy, dependencies, infrastructure, CI/CD, monitoring), present 2-3 complete architecture options:

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

**If approved**: Record final decision in ${SPEC_PATH}09_DECISIONS.md with full rationale.

**If modifications requested**: Return to relevant decision point and re-query.

---

## DELIVERABLES

After approval, create ${SPEC_PATH}02_ARCHITECTURE.md containing:

### Core Architecture
- All confirmed decisions from this workflow
- Architecture diagrams (high-level + detailed)
- Technology stack with versions
- Dependency list with rationale
- Multi-tenancy model (if applicable)
- Code quality standards
- Version control strategy

### Infrastructure & Operations (NEW - Critical!)
- **Hosting platform**: [AWS/Azure/GCP/PaaS] with reasoning
- **Container strategy**: [Docker/K8s/None] with configuration
- **CI/CD pipeline**: [Platform] with automation plan
  - What's automated: Tests, linting, deployment, coverage
  - Deployment workflow: Dev → Staging → Production
- **Environment strategy**: [Dev/Staging/Prod setup]
  - Environment variables management
  - Secrets management (how API keys/credentials stored)
- **Monitoring & observability**: [Tools and metrics]
  - What's monitored: Uptime, errors, performance
  - Alerting: Who gets notified, when
  - Logging strategy
- **Cost breakdown**:
  - Infrastructure: $X/month
  - CI/CD: $Y/month
  - Monitoring: $Z/month
  - Total estimated monthly: $N/month
- **Rollback plan**: How to rollback deployments if issues occur

### Documentation References
- References to official documentation for all major components
- Links to platform-specific guides (AWS/Azure/GCP docs, GitHub Actions docs, etc.)

**No code changes at this stage** - only documentation and decisions.

Ask ONE final blocking question if any ambiguity remains.

---
**NEXT STEP**: When 02_ARCHITECTURE.md is complete and approved, run `full-app-03-data-model-tdd.md` to design the data model.
