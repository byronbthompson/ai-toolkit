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

## PHASE 2: TECHNOLOGY STACK DECISION (Verbalized Sampling)

Based on your answers:
- Performance needs: [their answer]
- Team expertise: [their answer]
- Infrastructure constraints: [their answer]

### DECISION REQUIRED: Technology Stack

**Context**: This is a "point of no easy return" - changing languages/frameworks mid-project requires significant rewrite effort.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different programming paradigms and ecosystem philosophies, not just minor variations.

For EACH option, provide:

**Option A - Python with FastAPI** (Confidence: **High** if data/ML/API focus, **Medium** for real-time apps)

**Best for**: API-heavy backends, data processing pipelines, ML/AI integration, rapid prototyping

**Pros**:
- Fast development cycles with excellent developer experience
- Extensive libraries (data science, ML, API frameworks, scientific computing)
- Type hints with Pydantic provide runtime validation + IDE support
- Large community and comprehensive documentation
- Excellent for async operations (asyncio)

**Cons**:
- GIL limits true CPU-bound parallel processing (not I/O-bound)
- Slower runtime performance than compiled languages (Go, Rust, Java)
- Package management complexity (pip/conda/poetry ecosystem fragmentation)
- Dynamic typing can hide bugs until runtime (even with type hints)

**Avoid If**:
- You need extreme low-latency (<10ms) or high-throughput (100k+ RPS)
- CPU-bound parallel processing is critical (video encoding, simulations)
- Team has zero Python experience and tight deadlines

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their performance answer]" and "[reference their team expertise answer]". FastAPI is battle-tested for [specific use case from their answers]. However, confidence drops to **Medium** if you need WebSocket-heavy real-time features, where Node.js ecosystem is more mature.

**Reference**: https://fastapi.tiangolo.com/

---

**Option B - Node.js with TypeScript/Express** (Confidence: **High** for full-stack JS teams, **Low** for data-heavy workloads)

**Best for**: JavaScript full-stack teams, I/O-heavy workloads, real-time features (WebSockets), JAMstack architectures

**Pros**:
- JavaScript everywhere (share code/types between frontend + backend)
- Excellent for I/O-bound operations (event loop architecture)
- Massive npm ecosystem (800k+ packages)
- WebSocket support built-in, great for real-time (Socket.io)
- TypeScript adds compile-time type safety

**Cons**:
- Callback/Promise/async-await complexity ("callback hell" risk)
- npm dependency hell (node_modules size, supply chain risks)
- Weaker data science/ML ecosystem than Python
- TypeScript adds build complexity

**Avoid If**:
- Heavy data processing or ML/AI workloads
- Team has no JavaScript experience
- You need strong compile-time guarantees (consider Go/Rust instead)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference if they said full-stack JavaScript team]". Node.js excels at [specific use case]. However, confidence is **Low** if your "[reference performance answer]" includes data processing, where Python's ecosystem is far superior.

**Reference**: https://expressjs.com/

---

**Option C - Go with Gin/Echo** (Confidence: **Medium** - depends on team willingness to learn)

**Best for**: High-performance APIs, microservices, cloud-native apps, systems programming

**Pros**:
- Compiled language = fast (10-100x faster than Python/Node for CPU tasks)
- Built-in concurrency (goroutines) - excellent for parallel processing
- Small binary size, fast startup (great for containers/serverless)
- Strong typing catches bugs at compile-time
- Simple deployment (single binary, no runtime dependencies)

**Cons**:
- Steeper learning curve than Python/Node.js
- Less mature web framework ecosystem
- Verbose error handling (no exceptions)
- Smaller community than Python/JavaScript

**Avoid If**:
- Team has no Go experience and needs rapid delivery
- You need rich ML/data science libraries
- Rapid iteration is more important than raw performance

**Reasoning**: I rate this **Medium confidence** because while Go matches your "[reference performance answer]" needs, you mentioned "[reference team expertise answer]". Go requires learning investment (2-4 weeks for team to become productive), but pays off for high-scale systems. Confidence would be **High** if you explicitly stated "performance-critical microservices."

**Reference**: https://gin-gonic.com/

---

**Option D - [Custom based on team expertise]**
If you mentioned expertise in Java/Spring Boot, .NET/C#, or Rust, I'll provide a tailored option here.

---

**Recommendation**:
Based on your answers:
- Performance: [their answer]
- Team expertise: [their answer]
- Infrastructure: [their answer]

I recommend **Option [A/B/C]** because [specific reasoning tying their requirements to option strengths].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [explain reasoning]. Factors that could change my recommendation:
- If you need [specific feature], consider [alternative option]
- If your team already knows [language], that reduces risk significantly
- If budget for [infrastructure cost] is constrained, [option] may be cheaper

**Question**: "Which technology stack do you prefer? (A/B/C/D or describe alternative)"

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

### Question 5: Data Isolation Requirements (Verbalized Sampling)

"What level of data isolation do you require between tenants?"

**Context**: This is a fundamental architectural decision that affects security, cost, complexity, and scalability. Migration between isolation models later requires significant database restructuring.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different security/cost tradeoff philosophies, from shared to fully isolated.

For EACH option, provide:

**Option A - Shared schema with tenant_id** (Confidence: **High** for SMB SaaS, **Low** for regulated industries)

**Best for**: B2B SaaS targeting SMBs, consumer apps, startups optimizing for cost and speed

**Pros**:
- Simplest implementation (add tenant_id column to tables)
- Lowest infrastructure cost (single database serves all tenants)
- Easy to add features across all tenants (single migration)
- Good query performance with proper indexing on tenant_id
- Easy cross-tenant analytics and reporting

**Cons**:
- Data leakage risk if ANY query forgets WHERE tenant_id = X filter
- All tenants affected by database outages or performance issues
- Less appealing to enterprise customers (shared database perception)
- Difficult to move individual tenants to different servers
- Regulatory compliance challenges (HIPAA, SOC2 Type II harder)

**Avoid If**:
- Regulatory requirements mandate data isolation (healthcare, finance, government)
- Targeting enterprise customers who demand dedicated infrastructure
- You have limited database expertise (WHERE clause mistakes = data leakage)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their tenant type/size from earlier answers]". Shared schema works well for [X tenants] with [Y data sensitivity]. However, confidence drops to **Low** if you're targeting "[enterprise/regulated industry]" where compliance auditors will scrutinize this choice.

**Security**: Row-level security (RLS) policies REQUIRED (PostgreSQL RLS or application-level checks)

**Implementation effort**: Low (1-2 days)

**Reference**: https://www.postgresql.org/docs/current/ddl-rowsecurity.html

---

**Option B - Schema per tenant** (Confidence: **Medium** - sweet spot for many use cases, but has scaling limits)

**Best for**: Moderate isolation needs, 10-1000 tenants, compliance requirements, tenant-specific customization

**Pros**:
- Better isolation than shared schema (separate PostgreSQL schemas)
- Tenant-specific migrations/customization possible
- Easier backup/restore per tenant
- Satisfies some regulatory compliance scenarios
- Impossible to accidentally query another tenant's data

**Cons**:
- Connection pooling complexity (must switch schema per query)
- Schema count limits (PostgreSQL handles 100-1000s schemas, but performance degrades)
- Migrations must run across ALL schemas (N tenants = N migrations)
- Harder to run cross-tenant analytics
- More complex ORM configuration

**Avoid If**:
- You expect 10,000+ tenants (schema-per-tenant doesn't scale to that level)
- Strict regulatory isolation required (HIPAA/PCI may require database-per-tenant)
- Cross-tenant analytics is a core feature

**Reasoning**: I rate this **Medium confidence** because while schema-per-tenant provides good isolation for your "[reference tenant count expectation]", it introduces operational complexity. Confidence would be **High** if you explicitly stated "100-500 tenants with compliance needs" and **Low** if you said "unlimited tenant growth."

**Implementation effort**: Medium (3-5 days + ongoing schema management overhead)

**Reference**: https://www.citusdata.com/blog/2016/10/03/designing-your-saas-database-for-high-scalability/

---

**Option C - Database per tenant** (Confidence: **High** for regulated industries, **Low** for high tenant counts)

**Best for**: Strict regulatory requirements (HIPAA, PCI-DSS, financial), enterprise customers, geographic data residency

**Pros**:
- Complete data isolation (impossible to access another tenant's data)
- Tenant-specific performance tuning and scaling
- Easy to move tenants between servers/regions (geographic compliance)
- Satisfies most compliance requirements (auditors love this)
- Individual tenant backups/restores trivial

**Cons**:
- High infrastructure cost ($10-50/tenant/month for managed databases)
- Complex management (N tenants = N databases to migrate, monitor, backup)
- Connection pool exhaustion risk with 100+ tenants
- Cross-tenant analytics extremely difficult (requires federated queries)
- Provisioning new tenant takes longer (spin up database)

**Avoid If**:
- You expect thousands of tenants (cost becomes prohibitive)
- Budget-conscious startup (infrastructure costs scale linearly with tenants)
- You lack DevOps expertise to manage many databases

**Reasoning**: I rate this **High confidence** because you mentioned "[reference if they said HIPAA/PCI/enterprise]". Database-per-tenant is the gold standard for compliance and enterprises expect this. However, confidence is **Low** if your "[reference tenant count]" is high, as cost becomes ($50/tenant × 1000 tenants = $50k/month).

**Implementation effort**: High (1-2 weeks initial setup + ongoing per-tenant management)

**Cost**: $10-50/tenant/month (managed PostgreSQL on AWS RDS, Azure, GCP)

**Reference**: https://aws.amazon.com/rds/postgresql/pricing/

---

**Recommendation**:
Based on your answers:
- Tenant type: [their answer]
- Compliance needs: [their answer]
- Expected tenant count: [their answer]

I recommend **Option [A/B/C]** because [specific reasoning tying requirements to option].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [explain reasoning]. Factors that could change my recommendation:
- If compliance requirements become stricter, consider upgrading to [higher isolation option]
- If tenant count exceeds [X], consider [different option]
- If budget is constrained to $[Y]/month, [option] may be too expensive

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

## PHASE 5: INFRASTRUCTURE & HOSTING DECISION (Verbalized Sampling)

**CRITICAL**: Infrastructure decisions are "points of no easy return" - changing platforms later is expensive.

### Question 1: Deployment Environment (Verbalized Sampling)

"Where will this application be deployed?"

**Context**: This decision affects cost, scalability, vendor lock-in, and operational complexity. Migration between platforms later requires significant re-architecture.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different philosophies (managed vs self-hosted, cloud vs PaaS vs serverless).

**Option A - Platform-as-a-Service (PaaS)** (Confidence: **High** for MVPs/startups, **Medium** for scale)

**Best for**: Startups, MVPs, small teams prioritizing speed over control, frontend-focused apps

**Pros**:
- Zero-config deployment (git push = deploy)
- Managed infrastructure (no DevOps needed)
- Generous free tiers (Vercel/Netlify/Render/Railway)
- Auto-scaling included
- Built-in SSL, CDN, preview deployments

**Cons**:
- Vendor lock-in (hard to migrate off platform-specific features)
- Expensive at scale ($200-2000+/month for high traffic)
- Limited backend capabilities (Vercel/Netlify optimized for frontend)
- Less control over infrastructure
- May not support all languages/frameworks

**Avoid If**:
- You expect high traffic (cost scales exponentially)
- You need custom infrastructure (specific OS, kernel tuning)
- Background workers/cron jobs are critical (limited support on some PaaS)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their team size/expertise]". PaaS eliminates DevOps overhead for [small teams]. However, confidence drops to **Medium** if you expect "[high scale]" as costs become ($20/month MVP → $500+/month at 100k users).

**Recommended PaaS options**:
- **Vercel**: Next.js/React apps (best-in-class frontend DX)
- **Render**: Full-stack apps with databases
- **Railway**: Modern alternative with great pricing
- **Fly.io**: Global edge deployment for low latency

**Cost**: $0-50/month (MVP), $200-1000+/month (scale)

**Reference**: https://vercel.com/, https://render.com/

---

**Option B - Cloud Platform (AWS/Azure/GCP)** (Confidence: **Medium** - depends on team DevOps expertise)

**Best for**: Production apps expecting scale, need specialized services (ML, data analytics), enterprise requirements

**Pros**:
- Most flexible and powerful (every service imaginable)
- Cost-effective at scale (cheaper than PaaS for high traffic)
- No vendor lock-in to platform abstractions (use standard Docker/K8s)
- Enterprise features (compliance certifications, SLAs, support)
- Specialized services (ML, IoT, data warehouses, etc.)

**Cons**:
- Steep learning curve (100+ services to navigate)
- Complex pricing (surprise bills common without monitoring)
- YOU manage infrastructure (need DevOps expertise)
- Slower initial deployment than PaaS
- More operational overhead

**Avoid If**:
- Team has no DevOps experience (will struggle with setup)
- MVP/prototype (overkill for early stage)
- Small budget with no DevOps time ($500+/month minimum realistic)

**Reasoning**: I rate this **Medium confidence** because while cloud platforms match your "[reference scale expectations]", you mentioned "[reference team expertise]". Success requires DevOps knowledge (or hiring DevOps engineer). Confidence would be **High** if you explicitly stated "have DevOps expertise" or "expect 100k+ users."

**Cloud provider selection**:
- **AWS**: Industry standard, richest ecosystem (RECOMMENDED for most)
- **GCP**: Best for data/ML workloads, simpler than AWS
- **Azure**: Best for .NET/Microsoft shops

**Cost**: $100-500+/month (production), scales with usage

**Reference**: https://aws.amazon.com/, https://cloud.google.com/

---

**Option C - Serverless** (Confidence: **High** for APIs/event-driven, **Low** for stateful apps)

**Best for**: APIs, event-driven workloads, variable/unpredictable traffic, pay-per-use economics

**Pros**:
- Pay only for actual usage (not idle time)
- Infinite auto-scaling (0 to millions of requests)
- No servers to manage (fully managed)
- Great free tiers (AWS Lambda: 1M requests/month free)
- Perfect for variable traffic (news site, campaigns)

**Cons**:
- Cold starts (first request slow: 100ms-3s)
- Vendor lock-in (AWS Lambda code hard to migrate)
- Debugging complexity (distributed tracing required)
- Not suitable for long-running processes (15min timeout AWS Lambda)
- Stateless only (no in-memory session storage)

**Avoid If**:
- You need WebSockets/persistent connections
- Long-running processes (video encoding, batch jobs >15min)
- Consistent low-latency required (cold starts = unpredictable latency)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference if they said API/event-driven]". Serverless excels at [specific workload type]. However, confidence is **Low** if your "[reference app type]" requires persistent connections or stateful sessions.

**Serverless options**:
- **AWS Lambda**: Most mature, richest ecosystem
- **Cloudflare Workers**: Edge serverless, no cold starts

**Cost**: $0-20/month (low traffic), pay-per-request after free tier

**Reference**: https://aws.amazon.com/lambda/, https://workers.cloudflare.com/

---

**Option D - Self-Hosted VPS** (Confidence: **Low** for most teams, **Medium** for cost-sensitive/regulatory)

**Best for**: Cost-sensitive projects, regulatory requirements (data residency), need full control

**Pros**:
- Lowest cost at scale ($5-40/month for VPS)
- Full control (install anything, kernel tuning)
- No vendor lock-in (standard Linux server)
- Predictable costs (fixed monthly, not usage-based)

**Cons**:
- YOU manage EVERYTHING (security patches, backups, scaling, monitoring)
- High operational overhead (need sysadmin skills)
- Single point of failure (no auto-scaling, manual failover)
- Slow to provision/scale (manual setup)
- Security is YOUR responsibility

**Avoid If**:
- Team lacks Linux sysadmin expertise
- You need high availability (99.9%+ uptime)
- You value time over cost (setup takes days/weeks)

**Reasoning**: I rate this **Low confidence** for most teams because operational burden is high. However, confidence rises to **Medium** if you explicitly stated "[regulatory data residency]" or "[very limited budget with sysadmin skills]". At $5/month VPS, you save money but spend time.

**VPS providers**:
- **DigitalOcean**: Simple, good docs
- **Linode**: Reliable, competitive pricing
- **Hetzner**: Cheapest (Europe-based)

**Cost**: $5-40/month (VPS), but hidden cost = DevOps time

**Reference**: https://www.digitalocean.com/

---

**Recommendation**:
Based on your answers:
- App type: [their answer]
- Team size/expertise: [their answer]
- Expected scale: [their answer]
- Budget: [their answer]

I recommend **Option [A/B/C/D]** because [specific reasoning tying requirements to option].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [explain reasoning]. Factors that could change my recommendation:
- If traffic exceeds [X users], consider migrating from [PaaS] to [Cloud Platform]
- If budget is constrained to $[Y]/month, consider [cheaper option]
- If team gains DevOps expertise, [Cloud Platform] becomes more viable

**Question**: "Which deployment environment do you prefer? (A/B/C/D or describe alternative)"

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
