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

## Step 2: Present Deployment Platform Options

Based on answers, present 2-4 platform options with tradeoffs:

### For Beginner + Low Scale + Free/Small Budget

**RECOMMENDED: Vercel (if Next.js/React) or Render (if full-stack)**

**Option A: Vercel** (best for Next.js, React, static sites)
- **Pros**:
  - Zero config deployment (connect GitHub, done)
  - Automatic HTTPS, CDN, edge functions
  - Generous free tier
  - Excellent DX (developer experience)
- **Cons**:
  - Best for frontend/serverless, not ideal for long-running backends
  - Vendor lock-in to Vercel platform
- **Cost**: Free for hobby, $20/month pro
- **Deployment complexity**: Very low (push to GitHub)
- **Docs**: https://vercel.com/docs

**Option B: Render** (best for full-stack apps, Python, Node.js)
- **Pros**:
  - Supports any language (Python, Node, Go, Ruby, etc.)
  - Free PostgreSQL database included
  - Auto-deploy from GitHub
  - Simple pricing
- **Cons**:
  - Free tier has cold starts (spins down after inactivity)
  - Limited customization vs self-hosted
- **Cost**: Free tier, $7/month for always-on
- **Deployment complexity**: Low (web UI or render.yaml)
- **Docs**: https://render.com/docs

**Option C: Railway** (best for quick deploys)
- **Pros**:
  - Extremely simple (click to deploy)
  - Built-in PostgreSQL, Redis, etc.
  - Good for prototypes and MVPs
- **Cons**:
  - More expensive than Render for similar features
  - Less mature than other platforms
- **Cost**: $5/month minimum
- **Deployment complexity**: Very low
- **Docs**: https://docs.railway.app

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
