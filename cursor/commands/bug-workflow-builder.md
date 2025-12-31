Create /specs/BUG_<id>.md with:

TICKET IMPORT (if bug from external tracker):

If bug ticket from external tracker (JIRA, Linear, GitHub Issues, etc.):
- Ticket ID: [e.g., JIRA-1234, #567, LIN-89]
- Ticket URL: [link to external tracker]
- Reporter: [who reported the bug]
- Report date: [when was bug reported]
- Import all ticket details:
  - Title/Summary from ticket
  - Description from ticket
  - Steps to reproduce from ticket
  - Expected vs actual from ticket
  - Attachments (screenshots, logs) from ticket
  - Comments/discussion from ticket (may contain important context)
  - Related tickets (duplicates, blockers, related issues)
- Ask user: "Should I sync resolution back to [tracker] when fixed?"

PHASE 1: BUG TRIAGE AND CONTEXT GATHERING

BUG CLASSIFICATION:
Ask user to classify the bug:
- Severity: [Critical/High/Medium/Low]
  - Critical: System down, data loss, security breach
  - High: Major feature broken, impacts many users, workaround difficult
  - Medium: Feature partially broken, impacts some users, workaround available
  - Low: Minor issue, cosmetic, edge case, easy workaround
- Impact: [How many users affected? What functionality broken?]
- Priority: [Urgent/High/Normal/Low - when does this need to be fixed?]
- First occurrence: [When was bug first observed? Recent deploy? Always existed?]

BUG REPRODUCTION:
- Reproduction steps (detailed, numbered):
  1. [Step 1 - be specific: "Navigate to /dashboard"]
  2. [Step 2 - include data: "Click 'New Report' with user role = 'viewer'"]
  3. [Step 3 - describe action and expected result]
  - Include: URLs, button names, field values, user roles, data states
  - Avoid: Vague steps like "use the feature", "do the thing"
- Reproduction rate: [100% / Intermittent (X%) / One-time]
  - If intermittent: What conditions make it more/less likely?
- Can you reproduce locally? [Yes/No - if no, why not?]
  - If no: Production-only issue (investigate env differences)

EXPECTED vs ACTUAL BEHAVIOR:
- Expected behavior: [What SHOULD happen - be specific and observable]
  - Example: "API returns 200 with {status: 'success', id: 123}"
  - Avoid: "It should work correctly"
- Actual behavior: [What DOES happen - exact error message, wrong data, etc.]
  - Example: "API returns 500 with error: 'division by zero in calculateDiscount()'"
  - Include: Error messages (full text), wrong values (actual vs expected)
- Evidence (attach/reference):
  - Error logs (full stack trace, not just first line)
  - Screenshots (if UI bug)
  - Network traces (if API/integration bug)
  - Database state (if data bug)
  - Console output (browser console for frontend, server logs for backend)

ENVIRONMENT DETAILS:
- Affected environments: [Production/Staging/Local/All]
- Browser/Client: [Chrome 120, Safari 17, Mobile app v2.3, etc.]
- OS: [Windows 11, macOS 14, Ubuntu 22.04, iOS 17, etc.]
- App version/commit: [v2.1.3, commit abc123f, deployed 2024-01-15]
- Database version: [PostgreSQL 15.2, MongoDB 6.0, etc.]
- Relevant configuration: [Feature flags, environment variables that might affect bug]
- Recent changes: [Any deploys/changes before bug appeared?]

IMPACT ANALYSIS:
- User impact: [How does this affect users? Can they work around it?]
- Data impact: [Any data corruption/loss? Need data migration after fix?]
- Security impact: [Any security implications? Data exposure? Auth bypass?]
- Business impact: [Revenue loss? SLA breach? Regulatory compliance issue?]
- Workaround available: [Yes/No - if yes, describe temporary workaround]

PHASE 2: SIGNAL FLOW ANALYSIS (Trace from symptom to root cause)

SIGNAL FLOW: Trace the bug from where user observes it DOWN through the stack to find root cause.

The bug manifests at the TOP (user-facing layer) but root cause is often DEEP in the stack.
Don't fix the symptom - trace the signal flow to find and fix the root cause.

SIGNAL FLOW LAYERS (trace top-down):

LAYER 1: USER INTERFACE (where user sees the bug)
- What the user sees/experiences:
  - Error message displayed: [exact text]
  - Broken UI element: [what doesn't work]
  - Wrong data shown: [what vs expected]
  - Missing functionality: [what should be there]
- Where in the UI: [specific page, component, URL]
- User action that triggers: [button click, page load, form submit, etc.]

TRACE DOWN ↓

LAYER 2: FRONTEND/CLIENT (if applicable)
- Which component/page handles user action:
  - Use Grep to find: Component name, event handler
  - Read component code: What does it do when user acts?
- Client-side errors (browser console):
  - JavaScript errors: [stack trace]
  - Network errors: [failed API calls, status codes]
  - State management errors: [Redux, Context, etc.]
- What API is being called:
  - Endpoint: [POST /api/users, GET /api/reports, etc.]
  - Request payload: [what data is sent]
  - Response: [what comes back - status, body]
- Is the bug in frontend or does it come from backend?
  - If frontend bug: Fix at this layer
  - If backend bug: Continue tracing down ↓

LAYER 3: API/BACKEND SERVICE (where business logic lives)
- Which endpoint handler processes request:
  - Use Task (Explore): "Where is [endpoint] implemented?"
  - Read handler code: What does it do?
- What does the handler call:
  - Business logic functions: [service methods, helpers]
  - Database queries: [what data is fetched/written]
  - External API calls: [third-party services]
- Stack trace analysis:
  - Read FULL stack trace (not just first line)
  - Identify exact function and line where error occurs
  - Trace call chain: Handler → Service → Helper → Error
- Error propagation:
  - Where is error thrown: [file:line]
  - How is error caught/handled: [try/catch, error middleware]
  - Is error message helpful or obscured?

TRACE DOWN ↓

LAYER 4: DATA ACCESS (database, cache, file system)
- What data operation is failing:
  - Query that errors: [SELECT, INSERT, UPDATE, DELETE]
  - Data that triggers error: [specific values, edge cases]
  - Constraint violations: [unique, foreign key, not null, check]
- Database state inspection:
  - Use Bash to query database (if safe):
    - Check data that triggers bug
    - Check missing data (should exist but doesn't)
    - Check data integrity (orphaned records, bad references)
- Migration issues:
  - Recent schema changes: git log -- migrations/
  - Migration applied in all environments?
  - Data migration needed but not run?
- Data validation gaps:
  - Is bad data in database (validation missing)?
  - Is query wrong (logic error)?

TRACE DOWN ↓

LAYER 5: EXTERNAL DEPENDENCIES (third-party APIs, services)
- Which external service is involved:
  - API name: [Stripe, SendGrid, OpenAI, AWS, etc.]
  - Operation: [charge card, send email, generate text, store file]
- External service errors:
  - Status code: [400, 401, 403, 429, 500, 503]
  - Error message: [from service response]
  - Rate limiting: [quota exceeded, too many requests]
  - Authentication: [expired token, wrong credentials]
- Integration contract mismatch:
  - API version changed: [breaking change in service]
  - Request format wrong: [missing field, wrong type]
  - Response format changed: [field renamed, structure changed]
- Environment differences:
  - Works in staging but not production: [different credentials, different config]
  - API keys/secrets: [correct? expired?]

TRACE DOWN ↓

LAYER 6: INFRASTRUCTURE (servers, network, platform)
- Infrastructure issues:
  - Server resource exhaustion: [CPU, memory, disk, connections]
  - Network issues: [timeouts, connection refused, DNS]
  - Platform issues: [cloud provider outage, k8s pod crash]
- Configuration issues:
  - Environment variables: [missing, wrong value]
  - Feature flags: [wrong state for environment]
  - Deployment issues: [wrong version deployed, rollback needed]
- Monitoring/observability:
  - Check logs: [application logs, platform logs, infrastructure logs]
  - Check metrics: [error rates, latency, resource usage]
  - Check traces: [distributed tracing if available]

ROOT CAUSE IDENTIFICATION:

After tracing signal flow, identify the DEEPEST layer where the problem originates:

ROOT CAUSE LAYER: [UI / Frontend / API / Data / External / Infrastructure]
ROOT CAUSE COMPONENT: [Specific file, function, query, service, or config]
ROOT CAUSE EXPLANATION: [Why the bug occurs - be specific]
- Example: "Empty cart (total=0) passed to calculateDiscount() which divides by total"
- Example: "Migration adds 'role' column but doesn't set default, existing records have NULL"
- Example: "External API changed response format from {user: {...}} to {data: {user: {...}}}"

CONTRIBUTING FACTORS (things that made bug possible):
- Missing validation: [where validation should have caught bad input]
- Missing error handling: [where error should have been caught and handled gracefully]
- Missing tests: [what test would have caught this]
- Missing monitoring: [what alert would have detected this]
- Unclear error messages: [error message doesn't explain what's wrong]

SYSTEMIC ISSUES (patterns that enable this class of bugs):
- Is this a recurring pattern? [Similar bugs in other parts of codebase?]
- Is there a systemic fix needed? [Framework-level validation, middleware, etc.]
- Should architecture change? [Better error handling, better validation layer, etc.]

INVESTIGATION APPROACH:
Use systematic investigation (not random code changes):

1. Code Analysis:
   - Use Task tool (Explore agent) to find relevant code:
     - "Where is [failing feature] implemented?"
     - "What code handles [error scenario]?"
     - "What changed recently in [affected area]?"
   - Read error stack trace (if available) to identify:
     - Exact line where error occurs
     - Call stack leading to error
     - Variable values at error point
   - Use Grep to search for:
     - Error message text (find where error is thrown)
     - Function names from stack trace
     - Related code patterns

2. Git History Analysis:
   - Use Bash: git log --since="[date-before-bug]" --until="[date-after-bug]" -- [affected-files]
   - Identify recent changes to affected code
   - Use git blame to see who last modified problematic lines
   - Review commit messages and PRs for context
   - Ask: "Did this work before? When did it break?"

3. Dependency Analysis:
   - Check if bug related to external dependency
   - Review recent dependency updates (package.json, requirements.txt changes)
   - Check dependency changelogs for breaking changes
   - Verify dependency versions match across environments

4. Data Analysis (if data-related bug):
   - Inspect database state (what data triggers bug?)
   - Check for data migration issues
   - Look for data validation gaps
   - Identify edge cases in data

5. Integration Analysis (if integration bug):
   - Check API contracts (request/response formats)
   - Verify authentication/authorization
   - Check network issues (timeouts, rate limits)
   - Review logs from both sides of integration

SIGNAL FLOW TRACE SUMMARY:

Document the complete signal flow from symptom to root cause:

```
USER SYMPTOM (Layer 1):
  [What user sees - error message, broken feature, wrong data]
    ↓
FRONTEND/CLIENT (Layer 2):
  [Component/page where symptom appears]
    ↓ [API call or client-side error]
API/BACKEND (Layer 3):
  [Endpoint handler or service function]
    ↓ [Database query or external API call]
DATA/EXTERNAL (Layer 4/5):
  [Database query, external service call]
    ↓ [What fails]
ROOT CAUSE (Layer X):
  [Deepest layer where problem originates]
  [Specific line of code, query, config, or external issue]
```

ROOT CAUSE STATEMENT:
[One clear sentence explaining WHY the bug occurs at the deepest layer]

CONTRIBUTING FACTORS:
- [Factor 1: e.g., Missing validation in Layer 3]
- [Factor 2: e.g., No error handling in Layer 2]
- [Factor 3: e.g., Unclear error message from Layer 5]

FIX STRATEGY:
Based on signal flow analysis, fix should address:
1. ROOT CAUSE (Layer X): [Primary fix - prevent bug at source]
2. CONTRIBUTING FACTORS (Layers Y, Z): [Secondary fixes - defense in depth]
   - Add validation at Layer 3 (prevent bad data from reaching Layer 4)
   - Add error handling at Layer 2 (graceful degradation if Layer 3 fails)
   - Improve error messages (better debugging for future)
3. SYSTEMIC IMPROVEMENTS: [Prevent similar bugs]
   - [e.g., Add framework-level validation for all discount calculations]
   - [e.g., Add middleware to catch and log all division-by-zero errors]

MULTI-LAYER FIX JUSTIFICATION:
- Why fix at multiple layers? [Defense in depth - prevent bug AND handle gracefully if it recurs]
- Example: Fix division-by-zero at source (Layer 3) AND add validation at entry point (Layer 2) AND improve error message (Layer 1)
- This prevents bug from happening AND makes it easier to debug if similar bug occurs

ASK USER:
- "Signal flow traced from [Layer 1] down to root cause at [Layer X]. Does this match your understanding?"
- "Should we fix ONLY root cause or also add defensive validation at higher layers?"
- "Do you have additional context (recent changes, similar bugs, monitoring data)?"
- "Is there production data I should analyze (with proper access/privacy)?"

PHASE 3: TEST-DRIVEN BUG FIX (MANDATORY)

TDD REQUIREMENTS (bugs ALWAYS use TDD):
1. Write regression test FIRST (test must fail, proving bug exists)
2. Run test → verify it fails with the bug symptom
3. Fix the bug
4. Run test → verify it now passes
5. Run ALL tests → verify no regressions introduced

REGRESSION TEST REQUIREMENTS:
- Test name: test_bug_<id>_<short_description>
  - Example: test_bug_123_division_by_zero_in_discount_calculation
- Test structure:
  ```
  def test_bug_123_division_by_zero_in_discount_calculation():
      # Given: Reproduce exact conditions that trigger bug
      cart = Cart(items=[], total=0)  # Empty cart
      discount_code = "SAVE20"

      # When: Execute the operation that fails
      result = apply_discount(cart, discount_code)

      # Then: Assert expected behavior (not the buggy behavior)
      assert result.status == "success"  # Should not crash
      assert result.discount == 0  # No discount on empty cart
  ```
- Test must:
  - Reproduce exact bug scenario (same inputs, same state)
  - Fail with bug present (proves bug exists)
  - Pass with bug fixed (validates fix)
  - Be deterministic (not flaky)
  - Run quickly (<1 second if possible)
- Document in test:
  - Bug ID in test name and docstring
  - Link to bug report (if external tracker)
  - Date bug was fixed

COVERAGE REQUIREMENTS:
- Bug fix area must have adequate coverage (>80% of modified code)
- Overall coverage must not decrease
- Add tests for related edge cases discovered during investigation

PHASE 4: FIX VALIDATION

VALIDATION CHECKLIST:
- [ ] Regression test added BEFORE fix
- [ ] Regression test fails with bug (proves bug exists)
- [ ] Bug fix implemented (minimal change, addresses root cause)
- [ ] Regression test passes after fix
- [ ] ALL existing tests pass (no regressions introduced)
- [ ] Coverage maintained or improved
- [ ] Manual testing in affected environment:
  - [ ] Reproduce original bug scenario → verify fixed
  - [ ] Test related scenarios → verify no side effects
  - [ ] Test edge cases → verify robust fix
- [ ] Code review (if team process):
  - Explain root cause
  - Justify fix approach
  - Highlight any risks
- [ ] Documentation updated (if bug revealed documentation gap):
  - Update API docs if behavior clarified
  - Update README if setup issue
  - Add comment in code if tricky logic

BROWNFIELD CONSIDERATIONS (if existing codebase):
- [ ] All existing tests still pass (critical for bug fixes)
- [ ] Bug fix follows existing error handling patterns
- [ ] Fix doesn't introduce new dependencies
- [ ] Fix doesn't break backwards compatibility (unless bug is security issue)
- [ ] Check if bug exists in multiple places (search for similar code patterns)

PHASE 5: ROOT CAUSE DOCUMENTATION AND "FIX IT ONCE AND FOR ALL"

ROOT CAUSE ANALYSIS (using Signal Flow):

SIGNAL FLOW DIAGRAM:
Document the complete flow from user symptom to root cause:
```
[Layer 1: UI] User sees: "Error applying discount"
    ↓
[Layer 2: Frontend] Component: CheckoutPage calls POST /api/cart/apply-discount
    ↓
[Layer 3: API] Handler: applyDiscountHandler calls DiscountService.calculate()
    ↓
[Layer 4: Service] DiscountService.calculate() divides discount amount by cart.total
    ↓
[Layer 5: Root Cause] cart.total = 0 (empty cart) → division by zero error
```

ROOT CAUSE STATEMENT:
[One clear sentence]: "The bug occurs because empty carts have total=0, and DiscountService.calculate() divides by cart.total without checking for zero, causing division-by-zero error."

WHY IT WASN'T CAUGHT:
- Missing test coverage: [No tests for empty cart with discount code]
- Edge case not considered: [Assumed cart always has items]
- Validation gap: [No validation at API layer to reject empty carts]
- Unclear error message: [Generic "500 Internal Server Error" doesn't explain problem]

FIX IT ONCE AND FOR ALL - Multi-Layer Defense:

PRIMARY FIX (Root Cause - Layer 5):
- Location: DiscountService.calculate() in services/discount.py:42
- Fix: Add zero-check before division
- Code change:
  ```python
  def calculate(self, cart, discount_code):
      if cart.total == 0:
          return Discount(amount=0, message="No discount on empty cart")
      # ... rest of calculation
  ```
- This prevents the bug at its source

SECONDARY FIXES (Contributing Factors - Defense in Depth):

Layer 3 - API Validation:
- Location: applyDiscountHandler in routes/cart.py:89
- Fix: Validate cart is not empty before processing
- Code change:
  ```python
  def apply_discount_handler(request):
      if request.cart.items.length == 0:
          return error_response(400, "Cannot apply discount to empty cart")
      # ... rest of handler
  ```
- This catches bad input before it reaches service layer
- Benefit: Clearer error message, prevents wasted processing

Layer 2 - Frontend Validation:
- Location: CheckoutPage component
- Fix: Disable discount button if cart is empty
- Code change: Check cart.items.length > 0 before showing discount input
- Benefit: Better UX, prevents user from even attempting invalid action

Layer 1 - Error Message Improvement:
- Location: Error handling middleware
- Fix: Catch DivisionByZeroError and return clear message
- Benefit: If bug recurs (or similar bug), error message helps debugging

SYSTEMIC IMPROVEMENTS (Prevent Similar Bugs):

1. Framework-level validation:
   - Add decorator: @requires_non_empty_cart to all cart-related endpoints
   - Prevents similar bugs in other cart operations
   - Location: Create middleware/decorators/cart_validation.py

2. Add monitoring:
   - Alert on division-by-zero errors in production
   - Track empty cart + discount code attempts
   - Location: Add to monitoring/alerts.yml

3. Improve test coverage:
   - Add test suite for edge cases: empty cart, zero prices, negative discounts
   - Location: tests/services/test_discount_edge_cases.py

4. Code review checklist item:
   - "All division operations check for zero denominator"
   - Add to CONTRIBUTING.md or code review template

RELATED CODE TO REVIEW (Find Similar Bugs):
- Use Grep to search for similar patterns:
  - Search: "/ cart\.total" (other divisions by cart.total)
  - Search: "/ .*\.total" (divisions by any total field)
  - Search: "DiscountService" (other discount calculations)
- Review found code for similar edge case vulnerabilities
- Add preventive tests if found

PATTERN IDENTIFICATION:
- Is this a recurring pattern? [Search git history for "division" bugs]
- Similar bugs found: [List any similar bugs from git log]
- If yes: Systemic fix needed (not just one-off patch)
- Document architectural change in 09_DECISIONS.md:
  - Decision: "Add validation layer for all math operations on user inputs"
  - Rationale: "Multiple division-by-zero bugs indicate missing validation pattern"
  - Implementation: [Describe framework-level solution]

"FIX IT ONCE AND FOR ALL" CHECKLIST:
- [ ] Root cause fixed (prevents bug at source)
- [ ] Contributing factors addressed (defense in depth)
- [ ] Systemic improvements implemented (prevents similar bugs)
- [ ] Related code reviewed (no duplicate bugs elsewhere)
- [ ] Tests added at all layers (regression + edge cases)
- [ ] Monitoring added (detect if recurs)
- [ ] Documentation updated (prevent future developers from making same mistake)
- [ ] Pattern identified and addressed (architectural fix if needed)
- [ ] Bug learnings captured in LEARNINGS.md:
  Quick capture for project summary (detailed analysis already in BUG_<id>.md):
  - Signal flow: [Layer where symptom → Layer where root cause]
  - Root cause: [One sentence why bug occurred]
  - Prevention: [What systemic improvement was added]
  - Process gap: [If workflow issue identified]

PHASE 6: DEPLOYMENT AND MONITORING

DEPLOYMENT PLAN:
- Deployment strategy: [Immediate hotfix / Next release / Staged rollout]
  - Critical/High severity: Hotfix (out-of-band deployment)
  - Medium severity: Next scheduled release
  - Low severity: Batched with other fixes
- Rollback plan: [How to revert if fix causes issues]
- Monitoring after deploy:
  - Watch error rates in affected area
  - Monitor related functionality
  - Check user reports/support tickets
  - Verify fix in production environment

STAKEHOLDER COMMUNICATION:
- Notify affected users (if customer-facing bug)
- Update bug tracker (if external: JIRA, Linear, GitHub Issues)
- Document in CHANGELOG (if release notes maintained)
- Post-mortem (if critical bug):
  - Timeline of events
  - Impact analysis
  - Root cause
  - Fix implemented
  - Prevention measures

PHASE 7: COMMIT AND TRACK

QUALITY GATE ENFORCEMENT:
- Regression test added BEFORE fix (test file in git)
- All tests pass after fix (including new regression test)
- Coverage maintained or improved (no decrease allowed)
- Linter passes (no new violations)
- Manual validation complete (tested in affected environment)

COMMIT REQUIREMENTS:
- Update tracking docs:
  - Mark bug as resolved in BUILD_MAP.md (if part of milestone)
  - Update BUG_<id>.md with resolution summary
  - Update external tracker (if imported from JIRA/Linear/GitHub)
- Commit message format (with Signal Flow):
  ```
  fix: <short description of bug fix>

  Bug ID: <id>
  Ticket: <external-ticket-id if applicable>

  SIGNAL FLOW:
  [Layer 1: UI] <user symptom>
    ↓
  [Layer X: Component] <where error occurs>
    ↓
  [Root Cause] <deepest layer where problem originates>

  ROOT CAUSE: <why bug occurs - 1 sentence>

  FIX STRATEGY:
  - Primary: <fix at root cause layer>
  - Secondary: <defense in depth at other layers>
  - Systemic: <prevent similar bugs>

  CHANGES:
  - <file1>: <what changed>
  - <file2>: <what changed>

  TESTS:
  - Regression test: <test file name>
  - Related edge cases: <additional tests added>
  - Results: X/Y passed
  - Coverage: Z% (previous: W%, +D%)

  Fixes #<bug-id>
  ```
- Commit after all quality gates pass (not before)
- Include bug ID in commit message (for traceability)
- Include signal flow diagram in commit (for future reference)

FINAL VALIDATION:
Ask user:
1. "Regression test fails before fix, passes after fix - confirmed?"
2. "All existing tests still pass - confirmed?"
3. "Bug verified fixed in affected environment - confirmed?"
4. "Ready to commit and deploy, or need additional validation?"

IMPORTANT PRINCIPLES FOR BUG FIXES:

1. **Signal Flow First**: Trace from user symptom down through all layers to find root cause (not just where error surfaces)
2. **Fix Once and For All**: Address root cause + contributing factors + systemic issues (defense in depth)
3. **Test First, Always**: Write regression test before fix (proves bug exists, validates fix)
4. **Multi-Layer Defense**: Fix at root cause AND add validation at higher layers (prevent + gracefully handle)
5. **No Regressions**: All existing tests must pass (bug fix should not break other features)
6. **Root Cause, Not Symptom**: Fix deepest layer where problem originates, not just surface symptom
7. **Find Similar Bugs**: Search codebase for similar patterns (fix them all, not just one instance)
8. **Prevent Recurrence**: Add monitoring, improve error messages, update coding standards
9. **Document Signal Flow**: Include complete trace in commit and docs (helps future debugging)
10. **Brownfield Caution**: Bug fixes in existing code require extra care (high-risk changes)

SIGNAL FLOW METHODOLOGY SUMMARY:

Why Signal Flow?
- Bugs manifest at TOP (user-facing) but root cause is often DEEP in stack
- Fixing symptom (Layer 1) doesn't prevent recurrence
- Fixing root cause (Layer X) prevents bug from happening
- Multi-layer fixes provide defense in depth

How to use Signal Flow:
1. Start at Layer 1 (where user sees bug)
2. Trace DOWN through layers (UI → Frontend → API → Data → External → Infrastructure)
3. Identify ROOT CAUSE (deepest layer where problem originates)
4. Fix at root cause + add defensive validation at higher layers
5. Search for similar bugs in codebase (same pattern elsewhere?)
6. Add systemic improvements (prevent entire class of similar bugs)

Example Signal Flow:
```
[Layer 1: UI] User sees "Error 500"
    ↓ (error comes from API call)
[Layer 2: Frontend] Component calls POST /api/checkout
    ↓ (API returns 500)
[Layer 3: API] Handler calls PaymentService.charge()
    ↓ (service throws exception)
[Layer 4: Service] PaymentService divides total by quantity
    ↓ (quantity = 0)
[ROOT CAUSE] No validation for zero quantity before division

FIX STRATEGY:
- Layer 4 (Root): Add zero-check before division
- Layer 3 (API): Validate quantity > 0 before calling service
- Layer 2 (Frontend): Disable checkout if quantity = 0
- Layer 1 (UI): Show clear error if quantity invalid
- Systemic: Add @validate_positive decorator for all math operations
```

This approach ensures:
✓ Bug is fixed at source (root cause)
✓ Bug is prevented from happening (validation at entry points)
✓ Bug is handled gracefully if it recurs (better error messages)
✓ Similar bugs are prevented (systemic improvements)
✓ Future developers understand why bug occurred (signal flow documentation)

Review OFFICIAL docs if framework behavior is involved.
Ask ONE blocking question.
No code changes yet.
