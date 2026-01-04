# Deployment Infrastructure TDD

**Purpose**: Design deployment infrastructure and CI/CD pipeline (Technical Design Document)

**When to use**: Ad-hoc during planning phase when you need to design deployment strategy

**Output**: `/specs/DEPLOYMENT_TDD.md` with deployment infrastructure design

---

## What This Workflow Does

This is a **planning/design workflow**, not execution. It helps you:
- Design deployment infrastructure
- Choose deployment platforms
- Plan CI/CD pipeline
- Document deployment strategy
- Create deployment decision records

**This does NOT deploy your app** - it creates the technical design document for how deployment will work.

---

## Prerequisites

Before running this workflow:
- [ ] Have basic understanding of app architecture (from 02_ARCHITECTURE.md if full-app workflow)
- [ ] Know if app is containerized or not
- [ ] Know approximate scale/traffic expectations

---

## Step 1: Understand Deployment Context

Ask user clarifying questions:

**Question 1**: "What type of application is this?"
- Web API (backend service)
- Full-stack web app (frontend + backend)
- Frontend only (SPA, static site)
- Background workers/jobs
- Microservices
- Monolith

🛑 WAIT FOR ANSWER

**Question 2**: "What is your deployment expertise level?"
- Beginner (never deployed before, want simplest option)
- Intermediate (have deployed before, comfortable with PaaS)
- Advanced (comfortable with Docker, Kubernetes, infrastructure as code)

🛑 WAIT FOR ANSWER

**Question 3**: "What are your scale/traffic expectations?"
- Low (< 100 users, personal/internal tool)
- Medium (100-10K users, startup/small business)
- High (10K-1M users, growing product)
- Very high (1M+ users, enterprise scale)

🛑 WAIT FOR ANSWER

**Question 4**: "What is your budget for infrastructure?"
- Free tier only ($0/month)
- Small budget ($10-50/month)
- Medium budget ($50-200/month)
- Enterprise budget ($200+/month)

🛑 WAIT FOR ANSWER

---

## Step 2: Present Deployment Platform Options (Verbalized Sampling)

Based on answers:
- App type: [their answer]
- Expertise level: [their answer]
- Scale/traffic: [their answer]
- Budget: [their answer]

**Context**: Platform choice is a "point of difficult return" - migrating platforms later requires significant re-architecture and downtime.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different deployment philosophies (fully managed PaaS vs cloud infrastructure vs self-hosted).

### For Beginner + Low Scale + Free/Small Budget

**Option A: Vercel** (Confidence: **High** for Next.js/React, **Low** for traditional backends)

**Best for**: Next.js, React, static sites, frontend-focused apps, JAMstack

**Pros**:
- Zero config deployment (connect GitHub repository, automatic deploys)
- Automatic HTTPS, global CDN, edge functions included
- Generous free tier (hobby projects free forever)
- Excellent developer experience (preview deployments per PR)
- Fast deployment (30-60 seconds)

**Cons**:
- Optimized for frontend/serverless (not ideal for long-running backend processes)
- Vendor lock-in to Vercel platform features
- Serverless functions have cold starts (100-300ms)
- Limited to web workloads (no background jobs/workers)

**Avoid If**:
- You need traditional backend servers (Django, Rails, Express with WebSockets)
- You need background workers or cron jobs (limited support)
- High traffic expected (costs scale quickly beyond free tier)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their app type if frontend]" which aligns perfectly with Vercel's strengths. However, confidence is **Low** if your "[reference app type]" includes backend services, as Vercel's serverless model has limitations.

**Cost**: Free (hobby), $20/month (pro), scales with usage

**Deployment complexity**: Very low (git push = deploy)

**Reference**: https://vercel.com/docs

---

**Option B: Render** (Confidence: **High** for full-stack apps, **Medium** for high-traffic apps)

**Best for**: Full-stack web apps, Python/Node.js backends, traditional server architecture

**Pros**:
- Supports any language/framework (Python, Node.js, Go, Ruby, Docker)
- Free PostgreSQL/Redis databases included
- Auto-deploy from GitHub (similar to Vercel)
- Simple, predictable pricing
- Native services (web services, workers, cron jobs)

**Cons**:
- Free tier has cold starts (services spin down after 15min inactivity)
- Limited regions compared to AWS/GCP (US/EU only)
- Smaller ecosystem than major clouds
- Less customization than self-hosted

**Avoid If**:
- You need global low-latency (limited regions)
- Always-on free tier required (free tier has cold starts)
- Very high scale (100k+ concurrent users)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference expertise level: beginner]" and "[reference app type: full-stack]". Render provides the simplicity you need with traditional backend support. Confidence is **Medium** if you expect "[high traffic]" as Render's scale limits are lower than cloud platforms.

**Cost**: Free (with cold starts), $7/month (always-on web service), $25/month (production database)

**Deployment complexity**: Low (web UI or render.yaml file)

**Reference**: https://render.com/docs

---

**Option C: Railway** (Confidence: **Medium** - good DX but less mature)

**Best for**: Prototypes, MVPs, quick experiments, developer-friendly deployments

**Pros**:
- Extremely simple (one-click deploy from template)
- Built-in services (PostgreSQL, Redis, MongoDB, etc.)
- Modern interface and great developer experience
- Usage-based pricing (pay only for what you use)
- Quick to get started (< 5 minutes to deploy)

**Cons**:
- More expensive than Render for similar features
- Newer platform (less mature, smaller community)
- Limited documentation compared to alternatives
- Fewer regions available

**Avoid If**:
- Budget is primary concern (Render is cheaper for same workload)
- You need extensive documentation/tutorials
- You need proven stability (Render/Vercel more established)

**Reasoning**: I rate this **Medium confidence** because while Railway's DX is excellent for your "[reference expertise: beginner]" level, it's pricier than Render. Confidence would be **High** if you explicitly valued "fastest time to deploy" over cost, and **Low** if budget is constrained to free tier.

**Cost**: $5/month minimum, usage-based after

**Deployment complexity**: Very low (click to deploy)

**Reference**: https://docs.railway.app

---

**Recommendation**:
Based on your answers:
- App type: [their answer]
- Expertise: [their answer]
- Budget: [their answer]

I recommend **[Vercel/Render/Railway]** because [specific reasoning].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [reasoning]. Factors that could change my recommendation:
- If app includes background workers, must use [Render]
- If app is Next.js/React only, [Vercel] is ideal
- If budget is constrained to $0, [Render free tier with cold starts]

**Question**: "Which platform do you prefer for deployment? (A/B/C)"

🛑 WAIT FOR PLATFORM SELECTION

---

### For Intermediate + Medium Scale + Medium Budget

**RECOMMENDED: Platform-as-a-Service with more control**

**Option A: Fly.io** (best for Docker apps)
- **Pros**:
  - Full Docker support (run anything)
  - Global edge deployment
  - Postgres included
  - More control than Vercel/Render
- **Cons**:
  - Requires Docker knowledge
  - CLI-heavy (less GUI)
- **Cost**: Pay as you go (~$30-100/month)
- **Deployment complexity**: Medium (need Dockerfile)
- **Docs**: https://fly.io/docs

**Option B: DigitalOcean App Platform**
- **Pros**:
  - Simple PaaS with good control
  - Predictable pricing
  - Managed databases
  - Good documentation
- **Cons**:
  - Less feature-rich than AWS/GCP
  - US-focused (fewer regions)
- **Cost**: $5-50/month depending on resources
- **Deployment complexity**: Low-medium
- **Docs**: https://docs.digitalocean.com/products/app-platform/

**Option C: Heroku** (traditional PaaS)
- **Pros**:
  - Very mature, lots of addons
  - Simple deployment model
  - Good for Rails, Node, Python
- **Cons**:
  - Expensive for what you get (free tier removed)
  - Salesforce acquisition changed pricing
- **Cost**: $25+/month (no free tier)
- **Deployment complexity**: Low
- **Docs**: https://devcenter.heroku.com/

🛑 WAIT FOR PLATFORM SELECTION

---

### For Advanced + High Scale + Enterprise Budget

**RECOMMENDED: Cloud infrastructure with IaC**

**Option A: AWS ECS/Fargate** (Docker containers without managing servers)
- **Pros**:
  - Industry standard, huge ecosystem
  - Serverless containers (no EC2 management)
  - Integrates with all AWS services
  - Enterprise-grade reliability
- **Cons**:
  - Complex pricing
  - Steeper learning curve
  - More moving parts to manage
- **Cost**: $50-500+/month depending on usage
- **Deployment complexity**: High (requires IaC, Docker)
- **Docs**: https://docs.aws.amazon.com/ecs/

**Option B: Google Cloud Run** (serverless containers)
- **Pros**:
  - Simplest serverless container platform
  - Pay per request (cost-effective for low traffic)
  - Auto-scaling to zero
  - Fast cold starts
- **Cons**:
  - GCP smaller than AWS (fewer regions, services)
  - Request timeout limits (60 min max)
- **Cost**: Pay per use, very cheap for low traffic
- **Deployment complexity**: Medium (need Docker)
- **Docs**: https://cloud.google.com/run/docs

**Option C: Kubernetes (GKE, EKS, AKS)** (for microservices/very high scale)
- **Pros**:
  - Maximum control and flexibility
  - Industry standard for microservices
  - Cloud-agnostic (can run anywhere)
  - Handles very high scale
- **Cons**:
  - Very complex (overkill for most apps)
  - Requires dedicated DevOps expertise
  - Expensive ($300+/month minimum)
- **Cost**: $300-1000+/month
- **Deployment complexity**: Very high
- **When to use**: Only if you have microservices or very high scale

🛑 WAIT FOR PLATFORM SELECTION

---

## Step 3: Design CI/CD Pipeline

After platform is selected, design CI/CD automation:

**Ask user**: "Do you want automated deployments when code is pushed?"

**Options**:
- **Yes** (recommended): Auto-deploy on push to main branch
- **No**: Manual deployments only

### If Yes (Automated CI/CD):

**Question**: "Where is your code hosted?"
- GitHub
- GitLab
- Bitbucket
- Other

Based on answer, recommend CI/CD approach:

#### For GitHub:

**RECOMMENDED: GitHub Actions** (native, free for public repos)

**Design**:
```yaml
# .github/workflows/deploy.yml
name: Deploy to [Platform]

on:
  push:
    branches: [main]  # or master

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests
        run: |
          npm install  # or pip install, etc.
          npm test

  deploy:
    needs: test  # Only deploy if tests pass
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to [Platform]
        run: |
          # Platform-specific deploy command
          # e.g., vercel --prod, fly deploy, etc.
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
```

**Workflow**:
1. Developer pushes to main branch
2. GitHub Actions runs tests
3. If tests pass, deploys to platform
4. If tests fail, deployment blocked

**Cost**: Free for public repos, included in GitHub paid plans

---

#### For GitLab:

**RECOMMENDED: GitLab CI** (built into GitLab)

**Design**:
```yaml
# .gitlab-ci.yml
stages:
  - test
  - deploy

test:
  stage: test
  script:
    - npm install
    - npm test

deploy:
  stage: deploy
  only:
    - main
  script:
    - # Deploy command
  environment:
    name: production
    url: https://your-app.com
```

---

### If No (Manual Deployments):

Document manual deployment process:
- Developer runs deploy command locally
- No automation, full control
- Good for: Learning, prototypes, sensitive deployments

---

## Step 4: Environment Strategy

**Ask user**: "How many environments do you need?"

**Options**:

**Option A: Production only** (simplest, acceptable for prototypes)
- Just production
- Test locally before deploying
- **Risk**: Issues go straight to production
- **Cost**: 1x hosting cost
- **Recommended for**: Side projects, MVPs, internal tools

**Option B: Staging + Production** (recommended minimum)
- Staging: Test environment identical to production
- Production: Live users
- **Workflow**: Test in staging → Deploy to production
- **Cost**: 2x hosting cost
- **Recommended for**: Most production apps

**Option C: Dev + Staging + Production** (enterprise)
- Dev: Shared development environment
- Staging: Pre-production testing
- Production: Live
- **Cost**: 3x hosting cost
- **Recommended for**: Teams, critical apps

**Option D: Preview + Staging + Production** (for teams)
- Preview: Auto-deployed per pull request (Vercel, Render support this)
- Staging: Integration testing
- Production: Live
- **Benefit**: Test each feature independently
- **Cost**: Staging + Production + ephemeral previews
- **Recommended for**: Teams doing parallel work

🛑 WAIT FOR SELECTION

---

## Step 5: Database Strategy (if applicable)

If app uses database:

**Ask user**: "Where should the database be hosted?"

**Options**:

**Option A: Same platform as app** (simplest)
- Example: Render app + Render PostgreSQL
- **Pros**: One platform, simple config, often included
- **Cons**: Tied to that platform
- **Cost**: Often free tier or bundled
- **Recommended for**: Most apps

**Option B: Managed database service** (more reliable)
- Examples: AWS RDS, Supabase, PlanetScale, MongoDB Atlas
- **Pros**: Better reliability, automatic backups, scaling
- **Cons**: Extra service to manage, extra cost
- **Cost**: $15-50+/month
- **Recommended for**: Production apps with important data

**Option C: Self-managed** (maximum control)
- Run your own database server
- **Pros**: Full control, no vendor lock-in
- **Cons**: You handle backups, scaling, security
- **Cost**: Server cost + time to manage
- **NOT recommended** unless you have strong reason

🛑 WAIT FOR SELECTION

**Database migration strategy**:
- Ask: "How will database migrations be handled?"
- Options:
  - Auto-migrate on deploy (run migrations in deploy script)
  - Manual migrations (DBA runs migrations separately)
  - Recommended: Auto-migrate for most apps

---

## Step 6: Secrets Management

**Ask user**: "Where should secrets/credentials be stored?"

**Options**:

**Option A: Platform environment variables** (simplest, recommended)
- Store in Vercel/Render/AWS dashboard
- Access as environment variables in code
- **Pros**: Simple, built-in
- **Cons**: Tied to platform
- **Cost**: Free
- **Recommended for**: Most apps

**Option B: Secrets manager** (enterprise)
- AWS Secrets Manager, HashiCorp Vault, etc.
- **Pros**: Centralized, audit logs, rotation
- **Cons**: More complex, extra cost
- **Cost**: $1-5/secret/month
- **Recommended for**: Enterprise, compliance requirements

🛑 WAIT FOR SELECTION

**Required secrets** (ask user to list):
- Database URL
- API keys (Stripe, SendGrid, etc.)
- JWT secret / encryption keys
- Any third-party credentials

---

## Step 7: Rollback Strategy

Design how to rollback if deployment fails:

**Platform-specific**:

**Vercel**: Automatic rollback on deploy failure, manual rollback via dashboard or CLI
**Render**: Rollback via dashboard (redeploy previous version)
**Railway**: Redeploy previous deployment
**AWS/GCP**: Rollback task definition or container image
**Heroku**: `heroku releases:rollback`

**Design rollback procedure**:
1. Detect failed deployment (how?)
   - Health check fails
   - Error rate spikes
   - Manual observation
2. Rollback command
3. Verify rollback successful
4. Investigate issue in safe environment

---

## Step 8: Monitoring & Health Checks

**CRITICAL**: All deployments need health checks

**Ask user**: "Should we include monitoring/health check setup in deployment TDD?"

**Options**:
- **Yes**: Include basic health check endpoint design
- **No**: Handle in separate monitoring TDD

### If Yes:

**Health Check Endpoint Design**:
```
GET /health or /api/health

Response (200 OK):
{
  "status": "healthy",
  "database": "connected",
  "version": "1.0.0",
  "timestamp": "2026-01-02T12:00:00Z"
}

Response (503 Service Unavailable):
{
  "status": "unhealthy",
  "database": "disconnected",
  "error": "Connection refused"
}
```

**Platform health checks**:
- Vercel: Automatic (monitors function errors)
- Render: Configure health check path in dashboard
- AWS: ELB health checks, configure path and timeout
- Fly.io: Configure in fly.toml

---

## Step 9: Create Deployment TDD Document

Generate `/specs/DEPLOYMENT_TDD.md`:

```markdown
# Deployment Infrastructure - Technical Design Document

**Date**: [YYYY-MM-DD]
**Author**: [Your name or "AI Agent"]
**Status**: Draft / Approved

---

## Executive Summary

**Deployment Platform**: [Vercel/Render/AWS/etc.]
**Estimated Cost**: $[X-Y]/month
**Deployment Complexity**: [Low/Medium/High]
**Deployment Time**: [X minutes per deploy]

**Key Decisions**:
- Platform: [Why this platform was chosen]
- CI/CD: [Automated via GitHub Actions / Manual]
- Environments: [Staging + Production]
- Database: [Managed PostgreSQL on Render]

---

## 1. Platform Selection

**Chosen Platform**: [Platform name]

**Reasoning**:
- [Reason 1: Matches expertise level]
- [Reason 2: Fits budget]
- [Reason 3: Supports required features]

**Alternatives Considered**:
- [Platform A]: [Why not chosen]
- [Platform B]: [Why not chosen]

**Official Documentation**: [URL]

---

## 2. Deployment Architecture

**Application Type**: [Web API / Full-stack / Frontend-only / etc.]

**Architecture Diagram**:
```
[User] --> [CDN/Load Balancer] --> [App Instance(s)] --> [Database]
                                                      --> [Redis Cache]
                                                      --> [External APIs]
```

**Components**:
- **App**: [Technology, number of instances]
- **Database**: [Type, where hosted]
- **Cache**: [Redis/Memcached if applicable]
- **CDN**: [Cloudflare/Vercel Edge if applicable]

---

## 3. CI/CD Pipeline

**Automation Level**: [Fully automated / Manual]

**Workflow**:
1. Developer pushes code to [main/master] branch
2. [GitHub Actions / GitLab CI / etc.] triggers
3. Run tests (npm test / pytest / etc.)
4. If tests pass, deploy to [staging] environment
5. [Manual approval or automatic] to deploy to production

**Pipeline Configuration**:
- Location: `.github/workflows/deploy.yml` (or equivalent)
- Triggers: Push to main branch, or manual trigger
- Secrets required: [DEPLOY_TOKEN, DATABASE_URL, etc.]

**Deployment Command**:
```bash
# Example
vercel --prod --token=$VERCEL_TOKEN
# or
fly deploy
# or
aws ecs update-service...
```

---

## 4. Environment Strategy

**Environments**:
- **Staging**: [URL, purpose, deploy trigger]
- **Production**: [URL, purpose, deploy trigger]
- **Preview** (optional): Auto-deployed per PR

**Environment Variables**:
| Variable | Staging | Production | Stored In |
|----------|---------|------------|-----------|
| DATABASE_URL | staging-db.render.com | prod-db.render.com | Platform env vars |
| API_KEY | test_key | live_key | Platform env vars |
| NODE_ENV | staging | production | Platform env vars |

---

## 5. Database Deployment

**Database Type**: [PostgreSQL 15 / MongoDB 6 / etc.]
**Hosting**: [Render PostgreSQL / AWS RDS / etc.]

**Migration Strategy**:
- Tool: [Prisma / Alembic / Knex / Django migrations]
- When: Before deploying new app code
- Command: `npx prisma migrate deploy` (or equivalent)
- Rollback: [How to rollback migrations if needed]

**Backup Strategy**:
- Frequency: Daily automated backups
- Retention: 7 days
- Provider: [Platform built-in / AWS S3 / etc.]

---

## 6. Secrets Management

**Secrets Storage**: [Platform environment variables / AWS Secrets Manager / etc.]

**Required Secrets**:
- `DATABASE_URL`: PostgreSQL connection string
- `SECRET_KEY`: Application encryption key (generate: `openssl rand -hex 32`)
- `[THIRD_PARTY]_API_KEY`: [Stripe, SendGrid, etc.]

**Secret Rotation**:
- Database credentials: Rotate quarterly (or on team changes)
- API keys: Rotate as required by provider
- Process: [Manual process or automated]

**Access Control**:
- Who can view secrets: [Team leads only / All developers]
- How to access: [Platform dashboard / CLI / Secrets Manager]

---

## 7. Rollback Procedures

**Rollback Decision Criteria**:
- Health check fails for 5+ minutes
- Error rate > 5%
- Critical bug reported

**Rollback Steps**:
1. Identify issue (monitoring alerts, user reports)
2. Decide to rollback (team lead approval)
3. Execute rollback:
   ```bash
   # Example for Vercel
   vercel rollback [deployment-url]

   # Example for AWS
   aws ecs update-service --task-definition previous-version
   ```
4. Verify rollback successful (health check returns 200)
5. Notify team
6. Investigate root cause in safe environment

**Rollback Time Target**: < 5 minutes

---

## 8. Health Checks & Monitoring

**Health Check Endpoint**: `/health`

**Response Format**:
```json
{
  "status": "healthy",
  "database": "connected",
  "version": "1.0.0",
  "uptime": 3600
}
```

**Platform Health Check Configuration**:
- Path: `/health`
- Interval: 30 seconds
- Timeout: 5 seconds
- Unhealthy threshold: 3 consecutive failures

**Monitoring** (see separate MONITORING_TDD.md):
- Error tracking: [Sentry / Rollbar]
- Uptime: [UptimeRobot / Pingdom]
- Performance: [DataDog / New Relic]

---

## 9. Costs

**Monthly Cost Breakdown**:
- App hosting: $[X]/month ([Platform tier])
- Database: $[Y]/month (included or separate)
- CI/CD: $0 (GitHub Actions free tier)
- **Total**: $[X+Y]/month

**Scaling Costs**:
- Current capacity: [N users, M requests/sec]
- If double traffic: $[Z]/month estimated
- If 10x traffic: $[W]/month estimated

---

## 10. Deployment Checklist

Before deploying:
- [ ] All tests passing locally
- [ ] Environment variables configured
- [ ] Database migrations tested
- [ ] Health check endpoint implemented
- [ ] Rollback procedure documented
- [ ] Team notified of deployment

After deploying:
- [ ] Health check returns 200
- [ ] Smoke tests passed
- [ ] Monitoring alerts configured
- [ ] No error spikes in logs

---

## 11. Risks & Mitigations

**Risk 1**: Platform outage
- **Probability**: Low
- **Impact**: High (app down)
- **Mitigation**: Have multi-cloud deployment plan (future), monitor platform status page

**Risk 2**: Database migration fails
- **Probability**: Medium
- **Impact**: High (deployment blocked)
- **Mitigation**: Test migrations on staging first, have rollback migrations ready

**Risk 3**: Secrets leak
- **Probability**: Low
- **Impact**: Critical
- **Mitigation**: Never commit .env files, use secrets manager, rotate regularly

---

## 12. Future Considerations

**If scale increases**:
- Add load balancer
- Horizontal scaling (more instances)
- Database read replicas
- CDN for static assets

**If team grows**:
- More environments (per-developer or per-feature)
- Better secrets management (Vault)
- Infrastructure as Code (Terraform)

---

## Approval

**Approved by**: [Name]
**Date**: [YYYY-MM-DD]
**Next steps**: Implement deployment pipeline, test on staging

---

**Generated using deployment-tdd.md**
**Last updated**: [Date]
```

---

## Step 10: Record Decisions

Update `/specs/09_DECISIONS.md`:

```markdown
## Deployment Infrastructure

**Date**: [YYYY-MM-DD]

**Platform**: [Vercel/Render/AWS/etc.]
**Reasoning**: [Why chosen - expertise level, cost, features]
**Cost**: $[X-Y]/month
**Deployment complexity**: [Low/Medium/High]

**CI/CD**: [GitHub Actions / Manual]
**Environments**: [Staging + Production]
**Database**: [Managed PostgreSQL on Platform X]
**Secrets**: [Platform environment variables]

**Points of no easy return**:
- Platform choice (migration to different platform is significant effort)
- CI/CD setup (changing pipelines requires rework)

**References**: /specs/DEPLOYMENT_TDD.md
```

---

## Best Practices

**DO**:
- ✅ Choose simplest platform that meets needs (avoid over-engineering)
- ✅ Always have staging environment for production apps
- ✅ Automate deployments (reduces human error)
- ✅ Include health checks
- ✅ Document rollback procedure
- ✅ Test migrations on staging first

**DON'T**:
- ❌ Use Kubernetes unless you have clear need (overkill for most apps)
- ❌ Skip staging environment for production apps
- ❌ Store secrets in code
- ❌ Deploy without tests passing
- ❌ Deploy Friday afternoon (if prod app with users)

---

## Next Steps

After this TDD is approved:

1. **Implement deployment pipeline**: Set up CI/CD configuration
2. **Configure environments**: Create staging and production environments
3. **Test deployment**: Deploy to staging first
4. **Create monitoring TDD**: Run `monitoring-tdd.md` (separate workflow)
5. **Deploy to production**: Follow deployment TDD when ready

---

**This is a design document, not deployment execution.**

For actual deployment execution, follow the steps in the generated DEPLOYMENT_TDD.md document.

---

**Generated using deployment-tdd.md**
**Last updated**: 2026-01-02
