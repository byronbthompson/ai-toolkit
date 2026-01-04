Read ${SPEC_PATH} from the previous step's 00_START_HERE.md file (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}01_PRD.md and draft:

- Problem statement
- Personas
- User journeys (bullet)
- MVP scope + non-goals
- Success metrics
- Acceptance criteria (testable) - CRITICAL for TDD approach:
  - Format: Use Given/When/Then structure (recommended for clarity)
  - Completeness: Must be specific enough to generate tests from
  - Coverage: Include happy path, edge cases, and error scenarios
  - Testability: Each criterion must be objectively verifiable
  - Connection to testing:
    - If using TDD: These criteria will be directly converted to test cases BEFORE implementation
    - Tests must validate every acceptance criterion
    - Missing criteria = missing test coverage
  - Quality guidelines:
    - Good: "Given a VIP user with cart total $100, When 20% discount code is applied, Then final price is $80 and discount is recorded"
    - Poor: "Users should get discounts when they qualify"
    - Good: "Given an unauthenticated user, When accessing /dashboard, Then redirect to /login with 401 status"
    - Poor: "Protected pages should be secure"
  - Minimum requirements:
    - Cover all critical user flows and business rules
    - Include both success and failure scenarios
    - Specify expected system behavior for edge cases
    - Define observable outcomes (API responses, UI changes, data states)
- Out of scope

Before finalizing, review any relevant OFFICIAL docs/standards (name them and what they influence).

SCENARIO PLANNING (present delivery options):

**IMPORTANT**: All time estimates assume **AI-assisted development** (Claude, Cursor, or similar AI agents).
AI agents can code 3-5x faster than manual development for most tasks.

Generate 3-4 implementation scenarios based on requirements:

SCENARIO A - LEAN MVP (2-4 milestones, 1-3 hours AI-assisted):
- Scope: Core feature only, minimal error handling, hardcoded configs where acceptable
- Quality: Basic tests, 70%+ coverage, essential validations only
- Features: [list absolute minimum features to prove concept]
- Compromises: Limited edge case handling, basic UI, no advanced features
- Use case: Quick validation, prototype for feedback, proof of concept
- AI-assisted time: 1-3 hours (planning: 30-45 min, implementation: 30 min-2 hours)
- Manual equivalent: 6-15 hours (3-5x slower)

SCENARIO B - STANDARD PRODUCTION (4-8 milestones, 4-8 hours AI-assisted):
- Scope: Full feature set from PRD, proper error handling, security baseline
- Quality: Comprehensive tests, 80%+ coverage, all acceptance criteria met
- Features: [list all features from PRD scope]
- Compromises: Standard security (not hardened), good enough performance
- Use case: Ship to real users, maintain long-term, production-ready
- AI-assisted time: 4-8 hours (planning: 1-1.5 hours, implementation: 3-6.5 hours)
- Manual equivalent: 20-40 hours (4-5x slower)

SCENARIO C - ENTERPRISE GRADE (8-12 milestones, 10-18 hours AI-assisted):
- Scope: Everything in B + advanced features, hardened security, monitoring
- Quality: Exhaustive tests, 90%+ coverage, performance optimization
- Features: [list all features plus enterprise additions like audit logs, HA, etc.]
- Enhancements: Rate limiting, comprehensive logging, metrics, alerting
- Use case: Critical business app, high scale, compliance requirements
- AI-assisted time: 10-18 hours (planning: 1.5-2 hours, implementation: 8.5-16 hours)
- Manual equivalent: 50-90 hours (5x slower)

SCENARIO D - ENTERPRISE MULTI-TENANT (10-15 milestones, 16-28 hours AI-assisted):
- Scope: Everything in C + multi-tenancy, white-labeling, internationalization
- Quality: Full test coverage including tenant isolation, 90%+ coverage
- Features: [all features + tenant management, custom branding, i18n]
- Complexity: 50-100% additional time for multi-tenant architecture
- Use case: SaaS product, multiple customers, custom branding per customer
- AI-assisted time: 16-28 hours (planning: 2-2.5 hours, implementation: 14-25.5 hours)
- Manual equivalent: 80-140 hours (5x slower)

**AI Efficiency Factors**:
- ✅ Boilerplate code: Near-instant generation (5-10x faster)
- ✅ Test writing: Automated from acceptance criteria (3-5x faster)
- ✅ Documentation: Auto-generated from code (10x faster)
- ✅ Refactoring: Systematic and safe (3-4x faster)
- ⚠️ Complex algorithms: 2-3x faster (still requires human review)
- ⚠️ Architecture decisions: Same speed (human judgment required)
- ⚠️ Debugging edge cases: 1.5-2x faster (human insight critical)

PROGRESSIVE PATH:
- Can start with Scenario A and evolve to B/C/D incrementally
- Each scenario builds on previous (A → B → C → D)
- Recommend starting with [scenario] based on stated goals
- User can decide to stop at any scenario if needs are met

---

## PHASE 2: SCOPE SELECTION (Verbalized Sampling)

**Context**: Scope directly impacts time, cost, and complexity. Starting too ambitious risks never launching; starting too lean risks missing key requirements.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different philosophies (lean startup vs complete product vs enterprise-grade).

**Option A - Lean MVP** (Confidence: **High** for startups/validation, **Low** for established companies)

**Best for**: Validating product-market fit, quick learning, budget-constrained startups

**Scope**:
- Core user workflow only (1-3 features)
- Single user type (no roles/permissions)
- Basic UI (functional, not polished)
- Manual processes acceptable (email notifications, manual onboarding)
- No advanced features (search, filtering, analytics, integrations)

**Pros**:
- Fastest time to market (2-4 weeks with AI assistance)
- Minimal cost ($0-500 total infrastructure)
- Maximum learning (validate assumptions quickly)
- Easy to pivot (less code to change)

**Cons**:
- May feel incomplete to users
- Limited scalability (technical debt accumulates)
- Missing features users expect (search, notifications, etc.)
- Harder to sell to enterprise customers

**Avoid If**:
- Established company with brand reputation to protect
- Enterprise customers requiring complete features
- Product competing with mature alternatives

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their goal: validation/learning]". Lean MVP maximizes learning/dollar. However, confidence drops to **Low** if you're an "[established company]" where incomplete product damages brand.

**Time**: 2-4 weeks (AI-assisted: 8-16 hours planning + implementation)

**Cost**: $0-500 (free tier hosting, minimal services)

---

**Option B - Standard Product** (Confidence: **High** for most products, **Medium** for competitive markets)

**Best for**: Launching a complete product, competing with existing solutions, professional image

**Scope**:
- Complete core feature set (5-10 features)
- Multiple user roles (admin, standard user)
- Polished UI/UX (professional design)
- Common features (search, filtering, sorting, notifications)
- Basic integrations (email, auth, payment if needed)
- Mobile responsive

**Pros**:
- Feels complete to users (professional product)
- Competitive with existing solutions
- Room to grow (proper architecture from start)
- Suitable for paying customers

**Cons**:
- Longer time to market (6-12 weeks)
- Higher initial cost ($500-2000 infrastructure + design)
- More complexity (more features = more bugs)

**Avoid If**:
- Just validating idea (use Lean MVP instead)
- Very competitive market requiring unique differentiator (add more features)
- Budget is < $1000 total

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their stated scope/goals]". Standard Product balances completeness with speed. Confidence is **Medium** if you're in "[very competitive market]" where differentiation requires more features.

**Time**: 6-12 weeks (AI-assisted: 16-28 hours planning + implementation)

**Cost**: $500-2000 (hosting, design, email service, auth)

---

**Option C - Enterprise Grade** (Confidence: **Low** for most startups, **High** for B2B/compliance-heavy industries)

**Best for**: Enterprise B2B, regulated industries (healthcare, finance), multi-tenant SaaS

**Scope**:
- Complete feature set (15-30 features)
- Advanced user management (roles, permissions, SSO, SAML)
- Enterprise features (audit logs, compliance, white-labeling, API)
- High polish (custom design, animations, onboarding flows)
- Integrations (Slack, webhooks, REST API, SDKs)
- Security certifications (SOC2, HIPAA if needed)

**Pros**:
- Enterprise-ready from day one
- Can charge premium prices ($$$ ARR potential)
- Meets compliance requirements
- Competitive in enterprise market

**Cons**:
- Very long time to market (3-6 months)
- High cost ($5k-20k+ infrastructure, design, compliance)
- Over-engineered for small customers (complexity burden)
- Requires dedicated team (not solopreneur-friendly)

**Avoid If**:
- Targeting SMBs or consumers (overkill)
- Pre-revenue stage (burn too much before validation)
- Solo founder or small team (< 3 people)

**Reasoning**: I rate this **Low confidence** for most startups because enterprise features are expensive pre-revenue. However, confidence rises to **High** if you explicitly stated "[targeting enterprise B2B]" or "[compliance-heavy industry like healthcare]".

**Time**: 3-6 months (AI-assisted: 40-80 hours planning + implementation)

**Cost**: $5k-20k+ (enterprise hosting, security, compliance, design)

---

**Recommendation**:
Based on:
- Your goal: [their answer]
- Timeline: [their answer]
- Budget: [their answer]
- Target customer: [their answer]

I recommend **Option [A/B/C]** because [specific reasoning].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [reasoning]. Factors that could change my recommendation:
- If timeline shortens to [X weeks], must use Lean MVP
- If competing with mature product, may need Standard or Enterprise
- If budget increases to $[Y], can add more features

**Question**: "Which scope do you prefer? (A - Lean MVP / B - Standard / C - Enterprise)"

🛑 WAIT FOR CONFIRMATION - Do not proceed until explicit choice is made

Once confirmed, record decision in ${SPEC_PATH}09_DECISIONS.md with:
- Scope chosen (Lean MVP/Standard/Enterprise)
- Reasoning
- Timeframe estimate
- Budget estimate
- Confidence score: [Low/Medium/High]

---

Present ONE blocking question if any ambiguity remains.

No code changes at this stage - only documentation.

---
**NEXT STEP**: When 01_PRD.md is complete and approved, run `full-app-02-architecture-tdd.md` to design the system architecture.
