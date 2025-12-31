Use /specs/BUILD_MAP.md.

Implement Milestone <N> ONLY.
- Do not start Milestone <N+1>.
- If anything is ambiguous, STOP and ask ONE question.
- Keep changes minimal and aligned to the contracts.
- Follow testing approach from 08_TESTING_RELEASE.md:
  - If TDD: Generate tests from acceptance criteria first, then implement
  - If implementation-first: Implement, then generate comprehensive tests
  - If hybrid: Use TDD for complex features, implementation-first for simple
  - Bug fixes: ALWAYS use TDD (regression test first)

After implementation, verify ALL quality gates:
1. Run all tests - must pass 100%
2. Generate coverage report - threshold must be met
3. Compare coverage to previous milestone - must not decrease
4. Run lint/security checks - no blocking issues
   - Verify linter is configured (config file exists)
   - Run linter command and report results
5. Validate acceptance criteria

Provide:
- List of files changed
- Verification commands executed
- Test results (pass/fail counts)
- Coverage report (percentage + comparison to previous)
- Any threshold violations or warnings
- Behavior changes summary
- Update specs if behavior changed (record ADR if needed)

After ALL quality gates pass:
- Capture milestone learnings in LEARNINGS.md:
  Quick capture of key insights (detailed summary happens at project completion):
  - What worked well: [1-2 highlights]
  - What didn't work well: [1-2 challenges]
  - Process notes: [Any workflow observations]
  - Time: Estimated [X hours] vs Actual [Y hours], Variance [+/- Z hours because...]
- Update BUILD_MAP.md to mark Milestone <N> as completed:
  - Add completion date
  - Mark milestone status as ✓ completed
  - Document actual vs planned (files, commands, coverage achieved)
  - Note any deviations or learnings
- Commit changes with descriptive message
- Include test results and coverage in commit message or body
- Commit message format: "<type>: <summary>\n\n- Details\n- Test results: X/Y passed\n- Coverage: X%\n- Milestone <N> completed"

AFTER COMMIT, PAUSE FOR MILESTONE VALIDATION:
- Use full-app-11a-milestone-demo.md to present milestone completion to user
- Generate "What's New" summary with features, files changed, quality results
- Provide validation instructions (how to run and test the new functionality)
- Ask milestone validation questions
- WAIT for user approval before proceeding to Milestone <N+1>
- If user requests changes, revise milestone and re-run quality gates

If any quality gate fails:
- STOP immediately
- Document the failure
- Do NOT proceed to next milestone
- Do NOT commit failing code
- Fix issues or adjust thresholds (with justification)
