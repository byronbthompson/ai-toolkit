Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}07_SECURITY_NFR.md and draft:

- AuthN/AuthZ requirements
- RBAC + scope model
- Secrets management
- Rate limiting
- Audit logging + event logging requirements
- Cost Tracking and Budget Management:
  For any paid services (APIs, cloud resources, databases):
  SERVICE INVENTORY:
  List each paid service with:
  - Service name: [e.g., OpenAI API, AWS RDS, SendGrid]
  - Purpose: [e.g., AI chat responses, database hosting, email delivery]
  - Pricing model: [e.g., $0.002/1K tokens, $0.12/hour, $15/month base]
  - Estimated usage: [e.g., 1M tokens/month, 730 hours/month, 10K emails/month]
  - Estimated monthly cost: [e.g., $20/month, $87/month, $15/month]
  - Free tier available: [yes/no + limits, e.g., "Yes - 10K emails/month free"]
  - Required payment method: [credit card, enterprise contract, prepaid credits]
  - Pricing documentation URL: [link to official pricing page]
  COST SAFEGUARDS:
  - Rate limiting: [implement in code to stay within budget/quotas]
  - Usage monitoring: [track API calls, database size, etc.]
  - Alerting: [set up alerts at 50%, 75%, 90% of budget/quota]
  - Quota exceeded handling: [graceful degradation, queue requests, user notification]
  - Budget caps: [hard limits if available from provider]
  COST SUMMARY:
  - Total estimated monthly recurring cost: $X/month
  - Total one-time setup costs: $Y (domains, SSL certs, etc.)
  - Cost breakdown by category:
    - Compute: $A/month
    - Storage: $B/month
    - APIs/Services: $C/month
    - Other: $D/month
  - Cost scaling factors: [how costs increase with users/usage]
  - Free tier expiration: [if applicable, when free credits run out]
  USER APPROVAL REQUIRED:
  - Present cost estimate to user before proceeding
  - Ask: "This design will cost approximately $X/month in services. Proceed or adjust scope?"
  - Offer cost reduction alternatives if budget is a concern
  - Document cost acceptance in ${SPEC_PATH}09_DECISIONS.md
- Performance targets
- Performance Budget (for enterprise-level planning):
  Define acceptable performance targets:
  RESPONSE TIME TARGETS:
  - API endpoints: [e.g., p50 < 100ms, p95 < 200ms, p99 < 500ms]
  - Page load time (initial): [e.g., < 2 seconds on 3G, < 1 second on cable]
  - Time to interactive (TTI): [e.g., < 3 seconds]
  - Database queries: [e.g., < 50ms for reads, < 200ms for writes]
  - Background jobs: [e.g., < 30 seconds for async tasks]
  RESOURCE BUDGETS:
  - JavaScript bundle size: [e.g., < 300KB gzipped for main bundle]
  - CSS bundle size: [e.g., < 50KB gzipped]
  - Initial page size (total): [e.g., < 1MB including all assets]
  - API payload size: [e.g., < 100KB per response]
  - Database queries per request: [e.g., < 10 queries, prefer <5]
  - Memory usage: [e.g., < 512MB for API server, < 2GB for background workers]
  - Concurrent users supported: [e.g., 100 concurrent users on single instance]
  MONITORING APPROACH:
  - How performance will be measured: [Lighthouse, New Relic, custom instrumentation]
  - When to profile: [only if budget violated, not prematurely]
  - Baseline performance: [establish in early milestones for comparison]
  - Performance regression detection: [fail CI if metrics degrade >10%]
  - Thresholds for alerts: [if applicable in production]
  OPTIMIZATION STRATEGY:
  - Optimize only when budget violated (avoid premature optimization)
  - Profile before optimizing (measure, don't guess)
  - Focus on user-facing metrics (loading time, responsiveness)
  - Document optimization decisions in ${SPEC_PATH}09_DECISIONS.md
  USER PREFERENCE LEVELS:
  - Fast: Aggressive targets, optimize early (use for user-facing apps)
  - Standard: Reasonable targets, optimize when needed (most projects)
  - Unrestricted: No specific targets, optimize only if problems arise (internal tools)
  Ask user: "What performance expectations do you have? [Fast/Standard/Unrestricted]"
- Compliance constraints (if any)

Review OFFICIAL security hardening docs for stack + cloud.
Present baseline vs hardened options and recommend one.
Record decisions in ${SPEC_PATH}09_DECISIONS.md.
Ask ONE blocking question.
No code changes.

---
**NEXT STEP**: When 07_SECURITY_NFR.md is complete and approved, run `full-app-08-testing-release-tdd.md` to define testing and release strategy.
