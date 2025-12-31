Do not change code.

Based on /specs/BUILD_MAP.md or testing strategy docs, run ALL quality gate verifications:

1. Test Execution:
   - Run all tests (unit, integration, e2e as applicable)
   - Report: pass/fail counts, execution time

2. Code Coverage:
   - Generate coverage report
   - Compare to baseline/previous milestone
   - Report: current %, previous %, delta
   - Flag if coverage decreased

3. Lint/Security:
   - Run linter (must be configured: pylint/flake8/ruff for Python, eslint for Node.js)
   - Run security checks (if configured)
   - Report: error count, warning count
   - Verify linter config exists and is being used

4. Build Verification:
   - Run build command
   - Verify no compilation/bundling errors

5. Acceptance Criteria:
   - Cross-reference with milestone acceptance criteria
   - List which criteria are validated by passing tests

Output summary table:
- Quality Gate | Status | Details
- Tests        | ✓/✗    | X/Y passed
- Coverage     | ✓/✗    | X% (baseline: Y%)
- Lint         | ✓/✗    | N errors
- Security     | ✓/✗    | N issues
- Build        | ✓/✗    | Success/Failed
- Criteria     | ✓/✗    | N/M validated

If any gate fails, highlight blocking issues and stop.
