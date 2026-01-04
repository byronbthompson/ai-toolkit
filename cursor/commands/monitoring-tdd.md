# Monitoring & Observability TDD

**Purpose**: Design monitoring, logging, and alerting strategy (Technical Design Document)

**When to use**: Ad-hoc during planning phase when you need to design observability strategy

**Output**: `/specs/MONITORING_TDD.md` with monitoring infrastructure design

---

## What This Workflow Does

This is a **planning/design workflow**, not implementation. It helps you:
- Design monitoring strategy
- Choose monitoring tools
- Plan alerting rules
- Document observability approach
- Create monitoring decision records

**This does NOT set up monitoring** - it creates the technical design document for how monitoring will work.

---

## Prerequisites

Before running this workflow:
- [ ] Have basic understanding of app architecture
- [ ] Know deployment platform (from deployment-tdd.md if available)
- [ ] Know criticality level (prototype vs production with real users)

---

## Step 1: Understand Monitoring Needs

Ask user clarifying questions:

**Question 1**: "What is the criticality level of this application?"
- Prototype/MVP (downtime acceptable, learning phase)
- Production (real users, need to know about issues)
- Business-critical (revenue impact if down, SLA requirements)
- Mission-critical (safety/compliance, must have 24/7 monitoring)

🛑 WAIT FOR ANSWER

**Question 2**: "What is your monitoring budget?"
- Free tier only ($0/month)
- Small budget ($10-50/month)
- Production budget ($50-200/month)
- Enterprise budget ($200+/month)

🛑 WAIT FOR ANSWER

**Question 3**: "What is your team size?"
- Solo developer
- Small team (2-5 people)
- Medium team (6-20 people)
- Large team (20+ people)

🛑 WAIT FOR ANSWER

---

## Step 2: Define Monitoring Levels

Based on criticality, recommend monitoring level:

### Level 1: Basic Monitoring (Prototype/MVP)

**Tools**: Sentry (free tier) + UptimeRobot (free tier)
**Cost**: $0/month
**Setup time**: 30 minutes
**Coverage**:
- ✅ Error tracking (know when code breaks)
- ✅ Uptime monitoring (know if app is down)
- ❌ Performance metrics
- ❌ Custom business metrics
- ❌ Log aggregation

**Recommended for**:
- MVPs and prototypes
- Side projects
- Internal tools
- Learning projects

---

### Level 2: Production Monitoring (Real Users)

**Tools**: Sentry + UptimeRobot + (DataDog or New Relic)
**Cost**: $30-80/month
**Setup time**: 2-3 hours
**Coverage**:
- ✅ Error tracking with context
- ✅ Uptime monitoring with alerts
- ✅ Performance monitoring (APM)
- ✅ Infrastructure metrics (CPU, memory)
- ✅ Basic custom metrics
- ✅ Log aggregation
- ✅ Distributed tracing (if microservices)

**Recommended for**:
- Production apps with paying users
- SaaS products
- Customer-facing applications

---

### Level 3: Enterprise Observability (Business-Critical)

**Tools**: Full observability stack + PagerDuty
**Cost**: $200-500+/month
**Setup time**: 1-2 days
**Coverage**:
- ✅ Everything from Level 2
- ✅ Advanced alerting and on-call rotation
- ✅ Incident management
- ✅ Custom dashboards per team
- ✅ Compliance reporting
- ✅ Security monitoring (SIEM)
- ✅ SLA tracking

**Recommended for**:
- Enterprise applications
- High-traffic platforms
- Compliance requirements (HIPAA, SOC 2)
- 24/7 operations

🛑 WAIT FOR LEVEL SELECTION

---

## Step 3: Error Tracking Design

### Tool Selection

**Ask user**: "Which error tracking tool do you prefer?"

**Option A: Sentry** (RECOMMENDED for most apps)
- **Pros**:
  - Best-in-class error tracking
  - Excellent stack traces with source maps
  - Shows user context (what user did before error)
  - Release tracking (know which deploy caused issues)
  - Free tier: 5,000 errors/month
- **Cons**:
  - Can get expensive beyond free tier
- **Cost**: Free tier, then $26+/month
- **Docs**: https://docs.sentry.io

**Option B: Rollbar**
- **Pros**:
  - Similar to Sentry
  - Good error grouping
  - Telemetry tracking
- **Cons**:
  - Less popular than Sentry
  - Fewer integrations
- **Cost**: Free tier, then $12+/month
- **Docs**: https://docs.rollbar.com

**Option C: Platform-native** (CloudWatch, Azure Monitor, etc.)
- **Pros**:
  - Built into cloud platform
  - No extra service
  - Good if all-in on that cloud
- **Cons**:
  - Less feature-rich than Sentry
  - Harder to use
  - Vendor lock-in
- **Cost**: Free tier, then pay-per-use
- **Use when**: Already using AWS/Azure/GCP heavily

🛑 WAIT FOR SELECTION

### Error Tracking Configuration

**Error Categorization**:
- **Critical**: User-facing errors, payment failures, data loss
- **High**: Feature broken, non-critical flows blocked
- **Medium**: Edge cases, minor bugs
- **Low**: Deprecation warnings, non-functional issues

**Error Context to Capture**:
- User ID (if logged in)
- Request path and method
- Request parameters
- User agent and IP
- Release version
- Environment (staging/production)
- Custom tags (feature flags, A/B test group, etc.)

**Error Sampling** (if high volume):
- Critical errors: 100% (never sample)
- High errors: 100%
- Medium errors: 50% (sample if > 1000/day)
- Low errors: 10% (just need to know they exist)

---

## Step 4: Uptime Monitoring Design

### Tool Selection

**Ask user**: "Which uptime monitoring tool do you prefer?"

**Option A: UptimeRobot** (RECOMMENDED for basic uptime)
- **Pros**:
  - Free for 50 monitors
  - 5-minute check interval (free tier)
  - Email/SMS/webhook alerts
  - Status page hosting
- **Cons**:
  - Basic features (no advanced checks)
  - 5-min interval (not real-time)
- **Cost**: Free for basic, $7/month for 1-min checks
- **Docs**: https://uptimerobot.com/api

**Option B: Pingdom** (more features, paid)
- **Pros**:
  - Real user monitoring
  - Transaction monitoring
  - Detailed analytics
  - 1-minute checks
- **Cons**:
  - No free tier
  - More expensive
- **Cost**: $10+/month
- **Docs**: https://www.pingdom.com

**Option C: StatusCake** (middle ground)
- **Pros**:
  - Free tier with limits
  - Multiple check types
  - Good value
- **Cons**:
  - Less popular than UptimeRobot/Pingdom
- **Cost**: Free tier, then $25+/month
- **Docs**: https://www.statuscake.com

🛑 WAIT FOR SELECTION

### Health Check Endpoint Design

**CRITICAL**: App needs health check endpoint for uptime monitoring

**Endpoint**: `/health` or `/api/health`

**Response Format**:
```json
{
  "status": "healthy" | "degraded" | "unhealthy",
  "checks": {
    "database": "connected" | "disconnected",
    "redis": "connected" | "disconnected",
    "external_api": "reachable" | "unreachable"
  },
  "version": "1.0.0",
  "uptime": 3600  // seconds since start
}
```

**Status Codes**:
- `200 OK`: All systems healthy
- `503 Service Unavailable`: System unhealthy (database down, etc.)

**Health Checks to Include**:
- Database connectivity (SELECT 1)
- Cache connectivity (Redis PING) - if applicable
- External API reachability (if critical dependency)
- Disk space (if self-hosted)
- Memory usage (if self-hosted)

**Check Interval**: 30-60 seconds (balance between responsiveness and load)

---

## Step 5: Performance Monitoring Design (Optional)

If Level 2 or 3 monitoring selected:

**Ask user**: "Which performance monitoring tool do you prefer?"

**Option A: DataDog APM** (RECOMMENDED for full observability)
- **Pros**:
  - Full-stack observability
  - Infrastructure + application monitoring
  - Excellent dashboards
  - Distributed tracing
  - Log aggregation built-in
- **Cons**:
  - More expensive
  - Can be complex to set up
- **Cost**: $15/host/month + $5/million APM traces
- **Docs**: https://docs.datadoghq.com/tracing/

**Option B: New Relic**
- **Pros**:
  - Full-featured APM
  - Good for many languages
  - Free tier available (100GB data/month)
- **Cons**:
  - Complex pricing
  - Learning curve
- **Cost**: Free tier, then $99+/month
- **Docs**: https://docs.newrelic.com

**Option C: Grafana Cloud** (open source option)
- **Pros**:
  - Open source based (Prometheus + Grafana + Loki)
  - Free tier (10K series, 50GB logs)
  - Very flexible
- **Cons**:
  - More complex setup
  - Need to configure everything
- **Cost**: Free tier, then $50+/month
- **Docs**: https://grafana.com/docs/grafana-cloud/

**Option D: Skip APM** (if basic monitoring sufficient)
- Just use error tracking + uptime
- Add APM later if needed
- **Recommended for**: MVPs, low-traffic apps

🛑 WAIT FOR SELECTION

### Key Metrics to Track

**Application Performance**:
- Request rate (requests/sec)
- Error rate (errors/sec or %)
- Latency percentiles (p50, p95, p99)
- Throughput (requests/sec by endpoint)

**Infrastructure**:
- CPU usage (%)
- Memory usage (%)
- Disk usage (%) - if self-hosted
- Network I/O

**Database**:
- Query time (p50, p95, p99)
- Connection pool usage
- Slow queries (> 1 second)
- Deadlocks/lock waits

**Business Metrics** (custom):
- User signups/day
- Purchases/hour
- Active users
- Feature usage

---

## Step 6: Alerting Strategy Design

### Alert Prioritization

**P0 (Critical - wake up on-call)**:
- App completely down (uptime check fails for 5+ min)
- Error rate > 10% for 5+ min
- Database unreachable
- Payment processing failing

**P1 (High - alert during business hours)**:
- Error rate > 5% for 10 min
- Latency p95 > 5 seconds for 10 min
- Specific critical endpoint failing

**P2 (Medium - review next business day)**:
- Error rate > 2% for 30 min
- Memory usage > 80% for 30 min
- Slow queries increasing

**P3 (Low - weekly review)**:
- New type of error seen (FYI)
- Deprecation warnings

### Alert Channels

**Ask user**: "How should alerts be delivered?"

**Options**:
- Email (always include)
- Slack (recommended for team)
- SMS (for P0 alerts only, costs extra)
- PagerDuty (for on-call rotation)
- Webhook (custom integrations)

**Recommended setup**:
- P0: SMS + Slack + Email
- P1: Slack + Email
- P2: Email only
- P3: Weekly digest email

### Alert Fatigue Prevention

**Rules to avoid alert fatigue**:
- Don't alert on expected behavior (e.g., 404s for non-existent pages)
- Group related alerts (don't alert on same issue 100 times)
- Set reasonable thresholds (don't alert if 1 error in 1000 requests)
- Have "flapping" protection (require issue to persist before alerting)
- Review alerts monthly, remove/adjust noisy ones

---

## Step 7: Logging Strategy Design

### Log Levels

**ERROR**: Errors and exceptions that need attention
- Example: Payment processing failed, database query failed

**WARN**: Warnings that might indicate issues
- Example: Using deprecated API, retry attempted

**INFO**: Important events for business logic
- Example: User logged in, order placed, payment succeeded

**DEBUG**: Detailed debugging info (not in production)
- Example: Variable values, function entry/exit

**Production logging**:
- Log ERROR and WARN always
- Log INFO for important business events only
- Never log DEBUG in production (too verbose, may leak sensitive data)

### What to Log

**DO log**:
- Error messages with stack traces
- User actions (login, purchase, key feature usage)
- External API calls (duration, success/failure)
- Background job status (started, completed, failed)
- Security events (failed login, permission denied)

**DON'T log**:
- Passwords or secrets (NEVER)
- Credit card numbers or PII (compliance issue)
- Entire request/response bodies (too verbose)
- High-frequency events (every DB query) unless debugging

### Log Retention

**Ask user**: "How long should logs be retained?"

**Options**:
- **7 days**: Minimum for debugging recent issues (free tier limit)
- **30 days**: Standard retention (recommended minimum)
- **90 days**: Better for investigations
- **1 year+**: Compliance requirements

**Cost consideration**:
- More retention = higher cost
- Balance between debugging needs and cost

---

## Step 8: Dashboard Design

### Key Dashboards to Create

**Dashboard 1: Application Health** (first screen, always visible)
- Request rate (last hour, last 24 hours)
- Error rate (last hour, last 24 hours)
- P95 latency (last hour, last 24 hours)
- Current active users (if applicable)
- Status: 🟢 Healthy / 🟡 Degraded / 🔴 Down

**Dashboard 2: Infrastructure** (for ops team)
- CPU usage by instance
- Memory usage by instance
- Disk usage (if applicable)
- Network I/O
- Instance count (if auto-scaling)

**Dashboard 3: Database** (if database-heavy app)
- Query count and rate
- Slow query count (> 1 sec)
- Connection pool usage
- Database size and growth
- Top 10 slowest queries

**Dashboard 4: Business Metrics** (for stakeholders)
- Signups (daily, weekly, monthly)
- Active users (DAU, WAU, MAU)
- Revenue/transactions (if applicable)
- Feature usage (which features are used most)

---

## Step 9: Incident Response Plan

Design incident response workflow:

### Incident Severity Levels

**SEV1 (Critical)**:
- System completely down
- Data loss occurring
- Security breach
- **Response time**: Immediate (< 5 min)
- **Communication**: All stakeholders

**SEV2 (High)**:
- Major feature broken
- Significant performance degradation
- **Response time**: < 30 min
- **Communication**: Engineering team + management

**SEV3 (Medium)**:
- Minor feature broken
- Intermittent issues
- **Response time**: < 2 hours
- **Communication**: Engineering team

**SEV4 (Low)**:
- Cosmetic issues
- Low-priority bugs
- **Response time**: Next business day
- **Communication**: Log in backlog

### Incident Response Workflow

1. **Detection**: Monitoring alert or user report
2. **Acknowledge**: On-call engineer acknowledges within SLA
3. **Assess**: Determine severity level
4. **Communicate**: Notify stakeholders based on severity
5. **Investigate**: Identify root cause
6. **Mitigate**: Apply temporary fix or rollback
7. **Resolve**: Permanent fix deployed
8. **Document**: Post-mortem (for SEV1/SEV2)

### On-Call Rotation (if needed)

**Ask user**: "Will you have on-call rotation?"

**Options**:
- **No**: Manual monitoring, best-effort response
- **Yes**: Formal on-call rotation with PagerDuty/similar

**If Yes**:
- Rotation schedule: Weekly or daily
- Escalation: Primary on-call → Secondary → Manager
- Response time SLA: < 15 min for P0, < 30 min for P1

---

## Step 10: Create Monitoring TDD Document

Generate `/specs/MONITORING_TDD.md`:

```markdown
# Monitoring & Observability - Technical Design Document

**Date**: [YYYY-MM-DD]
**Author**: [Your name or "AI Agent"]
**Status**: Draft / Approved

---

## Executive Summary

**Monitoring Level**: [Basic / Production / Enterprise]
**Estimated Cost**: $[X-Y]/month
**Setup Complexity**: [Low/Medium/High]
**Incident Response**: [Best-effort / SLA-based / 24/7]

**Key Tools**:
- Error tracking: [Sentry]
- Uptime: [UptimeRobot]
- APM: [DataDog / New Relic / None]
- Logging: [DataDog / CloudWatch / None]

---

## 1. Monitoring Architecture

**Criticality Level**: [Prototype / Production / Business-critical / Mission-critical]
**Team Size**: [Solo / Small / Medium / Large]
**Budget**: $[X]/month

**Monitoring Components**:
```
[Application] --> [Error Tracking (Sentry)]
              --> [APM (DataDog)]
              --> [Logs (DataDog/CloudWatch)]
[Health Check] --> [Uptime Monitor (UptimeRobot)]
[Metrics] --> [Dashboard (DataDog/Grafana)]
[Alerts] --> [Slack/Email/PagerDuty]
```

---

## 2. Error Tracking

**Tool**: [Sentry / Rollbar / CloudWatch]
**Plan**: [Free tier / Pro]
**Cost**: $[X]/month

**Configuration**:
- DSN: Stored in environment variable `SENTRY_DSN`
- Environment: `production` / `staging`
- Release tracking: Enabled (tag releases with git SHA)
- Sample rate: 100% for critical, 50% for medium, 10% for low

**Error Categorization**:
| Severity | Description | Alert Channel | Example |
|----------|-------------|---------------|---------|
| Critical | User-facing, data loss | Slack + Email | Payment processing failed |
| High | Feature broken | Slack + Email | Login endpoint down |
| Medium | Edge case | Email | Rare validation error |
| Low | Non-functional | Weekly digest | Deprecation warning |

**Error Context**:
- User ID (if authenticated)
- Request path and method
- Release version
- Custom tags: [feature flags, tenant ID, etc.]

---

## 3. Uptime Monitoring

**Tool**: [UptimeRobot / Pingdom / StatusCake]
**Plan**: [Free tier / Paid]
**Cost**: $[X]/month

**Monitors**:
| Monitor | URL | Interval | Alert Threshold |
|---------|-----|----------|-----------------|
| Production API | https://api.example.com/health | 5 min | Down for 5 min |
| Staging API | https://staging.api.example.com/health | 5 min | Down for 10 min |

**Health Check Endpoint**:
- Path: `/health`
- Response: 200 OK if healthy, 503 if unhealthy
- Checks: Database connectivity, cache connectivity (if applicable)

**Alert Contacts**:
- Email: [team@company.com]
- Slack: [#alerts channel]
- SMS: [+1-XXX-XXX-XXXX] (on-call only)

---

## 4. Performance Monitoring (APM)

**Tool**: [DataDog / New Relic / Grafana / None]
**Plan**: [Free tier / Pro]
**Cost**: $[X]/month

**Key Metrics**:
| Metric | Target | Alert Threshold |
|--------|--------|-----------------|
| Request rate | [X req/sec baseline] | N/A (informational) |
| Error rate | < 1% | > 5% for 10 min |
| P95 latency | < 500ms | > 2000ms for 10 min |
| P99 latency | < 1000ms | > 5000ms for 10 min |
| Database query time | < 100ms (p95) | > 500ms for 10 min |
| Memory usage | < 70% | > 85% for 15 min |
| CPU usage | < 60% | > 80% for 15 min |

**Distributed Tracing** (if microservices):
- Enabled: [Yes / No]
- Sampling rate: [1% / 10% / 100%]
- Trace critical paths: [User registration, checkout, etc.]

---

## 5. Logging

**Tool**: [DataDog / CloudWatch / Grafana Loki / None]
**Retention**: [7 days / 30 days / 90 days]
**Cost**: $[X]/month

**Log Levels in Production**:
- ERROR: Always logged
- WARN: Always logged
- INFO: Important business events only (login, purchase, etc.)
- DEBUG: Never in production

**Structured Logging Format**:
```json
{
  "timestamp": "2026-01-02T12:00:00Z",
  "level": "ERROR",
  "message": "Payment processing failed",
  "user_id": "user_123",
  "error": "Stripe API timeout",
  "stack_trace": "...",
  "request_id": "req_abc123",
  "environment": "production"
}
```

**What to Log**:
- ✅ Errors with stack traces
- ✅ User actions (login, purchases)
- ✅ External API calls
- ✅ Background job results
- ❌ Passwords, secrets, PII
- ❌ High-frequency events (every DB query)

---

## 6. Alerting Rules

### Alert Definitions

**P0 - Critical (wake up on-call)**:
| Alert | Condition | Action |
|-------|-----------|--------|
| App down | Health check fails for 5 min | SMS + Slack + Email |
| High error rate | Error rate > 10% for 5 min | SMS + Slack + Email |
| Database down | Cannot connect for 2 min | SMS + Slack + Email |
| Payment failing | Payment error rate > 5% | SMS + Slack + Email |

**P1 - High (alert during business hours)**:
| Alert | Condition | Action |
|-------|-----------|--------|
| Elevated errors | Error rate > 5% for 10 min | Slack + Email |
| Slow responses | P95 latency > 5s for 10 min | Slack + Email |
| Memory high | Memory > 85% for 15 min | Slack + Email |

**P2 - Medium (review next day)**:
| Alert | Condition | Action |
|-------|-----------|--------|
| Moderate errors | Error rate > 2% for 30 min | Email |
| Slow queries | Slow query count up 50% | Email |
| Disk space | Disk > 85% | Email |

**P3 - Low (weekly review)**:
| Alert | Condition | Action |
|-------|-----------|--------|
| New error type | First occurrence | Weekly digest |
| Deprecation warning | Using deprecated API | Weekly digest |

### Alert Channels

- **Slack**: `#alerts` channel (P0, P1)
- **Email**: `team@company.com` (all priorities)
- **SMS**: `+1-XXX-XXX-XXXX` (P0 only, on-call)
- **PagerDuty**: [Configured / Not used]

### Alert Fatigue Prevention

- Group similar alerts (max 1 alert per issue)
- Require 3 consecutive failures before alerting (avoid flapping)
- Auto-resolve alerts when issue cleared
- Weekly review: disable noisy, low-value alerts

---

## 7. Dashboards

**Dashboard 1: Application Health** (main screen)
- Request rate (last hour, last 24h)
- Error rate (last hour, last 24h)
- P95/P99 latency (last hour, last 24h)
- Current status: 🟢 Healthy / 🟡 Degraded / 🔴 Down
- Recent deployments

**Dashboard 2: Infrastructure**
- CPU usage by instance
- Memory usage by instance
- Disk usage (if applicable)
- Active instances count

**Dashboard 3: Database**
- Query rate
- Slow query count (> 1s)
- Connection pool usage
- Top 10 slowest queries

**Dashboard 4: Business Metrics**
- Daily/weekly/monthly signups
- Active users (DAU, WAU, MAU)
- Revenue/transactions (if applicable)
- Feature usage breakdown

**Access**:
- Engineering team: All dashboards
- Management: Dashboard 4 (business metrics)
- Public status page: [Yes / No]

---

## 8. Incident Response

**Severity Levels**:
| Level | Definition | Response Time | Notification |
|-------|------------|---------------|--------------|
| SEV1 | System down, data loss | < 5 min | All stakeholders |
| SEV2 | Major feature broken | < 30 min | Eng team + management |
| SEV3 | Minor feature broken | < 2 hours | Eng team |
| SEV4 | Cosmetic issue | Next business day | Backlog |

**Incident Workflow**:
1. **Detection**: Alert fires or user reports issue
2. **Acknowledge**: On-call acknowledges within [5/15/30] minutes
3. **Assess**: Determine severity (SEV1-4)
4. **Communicate**: Notify per severity level
5. **Investigate**: Check logs, metrics, recent deployments
6. **Mitigate**: Rollback or hotfix
7. **Resolve**: Deploy permanent fix
8. **Document**: Post-mortem (required for SEV1/SEV2)

**On-Call Rotation**: [Yes / No]
- If yes: Weekly rotation, primary + secondary
- Escalation path: On-call → Team lead → Engineering manager
- Tools: [PagerDuty / Manual]

**Post-Mortem Template** (for SEV1/SEV2):
- What happened (timeline)
- Root cause (5 whys)
- Impact (users affected, duration)
- Resolution (what fixed it)
- Prevention (how to avoid in future)
- Action items with owners

---

## 9. Costs

**Monthly Cost Breakdown**:
- Error tracking: $[X]/month ([Sentry Pro / Free tier])
- Uptime monitoring: $[Y]/month ([UptimeRobot / Free tier])
- APM: $[Z]/month ([DataDog / New Relic / None])
- Logging: $[W]/month (included in APM or separate)
- Alerting: $[V]/month ([PagerDuty / Free])
- **Total**: $[Sum]/month

**Scaling Costs**:
- If traffic doubles: $[X]/month (APM scales with hosts/requests)
- If add more services: $[Y]/month per service

---

## 10. Implementation Checklist

**Phase 1: Basic Monitoring (Week 1)**:
- [ ] Set up error tracking (Sentry)
- [ ] Implement health check endpoint
- [ ] Set up uptime monitoring (UptimeRobot)
- [ ] Configure basic alerts (app down, high error rate)
- [ ] Test alerts (trigger intentional errors)

**Phase 2: Production Monitoring (Week 2-3)**:
- [ ] Set up APM (DataDog/New Relic) - if applicable
- [ ] Create Application Health dashboard
- [ ] Configure performance alerts (latency, memory)
- [ ] Set up log aggregation
- [ ] Create runbooks for common issues

**Phase 3: Optimization (Week 4+)**:
- [ ] Create business metrics dashboard
- [ ] Fine-tune alert thresholds (reduce noise)
- [ ] Set up on-call rotation (if needed)
- [ ] Document incident response process
- [ ] Conduct incident response drill

---

## 11. Success Metrics

**Monitoring effectiveness**:
- Mean time to detection (MTTD): [Target: < 5 min]
- Mean time to resolution (MTTR): [Target: < 30 min for SEV1]
- False positive rate: [Target: < 10% of alerts]
- Alert response rate: [Target: 100% acknowledged within SLA]

**Review cadence**:
- Daily: Check dashboards for anomalies
- Weekly: Review alert fatigue (disable noisy alerts)
- Monthly: Review monitoring costs vs value
- Quarterly: Update runbooks, test incident response

---

## 12. Future Considerations

**If scale increases**:
- More detailed distributed tracing
- Real user monitoring (RUM)
- Synthetic monitoring (test critical flows)
- Advanced anomaly detection (ML-based)

**If team grows**:
- Team-specific dashboards
- Formal on-call rotation with PagerDuty
- Dedicated SRE/DevOps role
- Runbook repository

---

## Approval

**Approved by**: [Name]
**Date**: [YYYY-MM-DD]
**Next steps**: Implement monitoring per this TDD

---

**Generated using monitoring-tdd.md**
**Last updated**: [Date]
```

---

## Step 11: Record Decisions

Update `/specs/09_DECISIONS.md`:

```markdown
## Monitoring & Observability

**Date**: [YYYY-MM-DD]

**Monitoring Level**: [Basic / Production / Enterprise]
**Error Tracking**: [Sentry / Rollbar / etc.]
**Uptime**: [UptimeRobot / Pingdom / etc.]
**APM**: [DataDog / New Relic / None]
**Logging**: [DataDog / CloudWatch / None]

**Cost**: $[X]/month
**Alert channels**: [Slack, Email, SMS]
**On-call**: [Yes / No]

**Reasoning**: [Why this level chosen - criticality, budget, team size]

**Health check endpoint**: `/health`
**Key metrics**: Error rate, latency, uptime

**References**: /specs/MONITORING_TDD.md
```

---

## Best Practices

**DO**:
- ✅ Start with basic monitoring (error tracking + uptime)
- ✅ Add APM when you have real users and budget
- ✅ Set reasonable alert thresholds (avoid alert fatigue)
- ✅ Test alerts after setup (trigger intentional errors)
- ✅ Review and adjust monthly (remove noisy alerts)
- ✅ Create runbooks for common issues

**DON'T**:
- ❌ Over-monitor (too many metrics = noise)
- ❌ Alert on everything (alert fatigue)
- ❌ Log sensitive data (PII, passwords, credit cards)
- ❌ Set up monitoring and never check dashboards
- ❌ Ignore repeated alerts (either fix or remove)

---

## Common Mistakes

**Mistake 1**: "We'll add monitoring later"
- **Problem**: Can't debug production issues without monitoring
- **Solution**: Set up basic monitoring (Sentry + UptimeRobot) from day 1

**Mistake 2**: "Alert on every error"
- **Problem**: Alert fatigue, team ignores all alerts
- **Solution**: Only alert on actionable, important errors

**Mistake 3**: "Monitoring is expensive, skip it"
- **Problem**: Downtime costs more than monitoring
- **Solution**: Start with free tier ($0/month for basic monitoring)

**Mistake 4**: "Too many dashboards, no one looks"
- **Problem**: Monitoring theater (looks good but unused)
- **Solution**: 1-2 key dashboards that team checks daily

---

## Next Steps

After this TDD is approved:

1. **Implement monitoring**: Set up tools per this TDD
2. **Test alerts**: Trigger intentional errors to verify
3. **Create dashboards**: Build key dashboards
4. **Write runbooks**: Document common issues and fixes
5. **Train team**: Show team where dashboards/logs are

---

**This is a design document, not monitoring implementation.**

For actual monitoring implementation, follow the steps in the generated MONITORING_TDD.md document.

---

**Generated using monitoring-tdd.md**
**Last updated**: 2026-01-02
