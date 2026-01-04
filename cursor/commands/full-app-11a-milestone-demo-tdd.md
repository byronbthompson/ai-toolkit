MILESTONE VALIDATION CHECKPOINT

Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

After ${SPEC_PATH}BUILD_MAP.md update and commit for Milestone N, PAUSE for user validation.

DO NOT proceed to Milestone N+1 until user approves this milestone.

GENERATE "WHAT'S NEW" SUMMARY:

MILESTONE N COMPLETED: [Milestone name]

Features Added:
- [Feature 1]: Brief description of what was implemented
- [Feature 2]: Brief description of what was implemented
- [Feature 3]: Brief description of what was implemented

Files Changed:
- Created: [list new files]
- Modified: [list modified files]
- Total files touched: N

Quality Gate Results:
- Tests: X/Y passed (100% required)
- Coverage: Z% (previous: W%, change: +/-D%)
- Linter: [PASS/FAIL] - [N issues if any]
- Build: [PASS/FAIL]

HOW TO VALIDATE THIS MILESTONE:

Provide clear, executable instructions:

1. Setup (if first milestone):
   ```
   [exact commands to set up environment]
   ```

2. Run the application:
   ```
   [exact commands to start the app]
   ```

3. Test the new functionality:
   ```
   Step 1: [specific action to take]
   Expected: [what should happen]

   Step 2: [specific action to take]
   Expected: [what should happen]

   [Continue for each key feature]
   ```

4. Example test cases to try:
   - Happy path: [specific example with expected result]
   - Edge case: [specific example with expected result]
   - Error case: [specific example with expected result]

Screenshots or Example Outputs (if applicable):
[If UI changes or CLI output, show example]
```
[example output or describe what user should see]
```

Known Limitations (for this milestone):
- [Feature X not yet implemented - coming in Milestone Y]
- [Edge case Z not yet handled - coming in Milestone W]
- [Any temporary workarounds or hardcoded values]

MILESTONE VALIDATION QUESTIONS:

Ask user for feedback:

1. Does this milestone match your expectations?
   - If NO: What needs to change?

2. Have you tested the new functionality?
   - If YES: Any issues or unexpected behavior?
   - If NO: Would you like to test before proceeding?

3. Should we adjust remaining milestones based on what we learned?
   - Any features to add/remove/reprioritize?
   - Any technical insights that change the approach?

4. Ready to proceed to Milestone N+1?
   - If YES: Continue to next milestone
   - If NO: What needs to happen first?

AGENT PAUSE: Wait for user response before continuing.

HANDLING USER FEEDBACK:

If user requests changes:
- Document feedback in ${SPEC_PATH}09_DECISIONS.md
- Update ${SPEC_PATH}BUILD_MAP.md if milestone plan changes
- Update relevant spec docs if requirements changed
- Revise current milestone code if needed
- Re-run quality gates after changes
- Re-present this validation checkpoint

If user approves:
- Document approval in ${SPEC_PATH}BUILD_MAP.md: "Milestone N approved by user on [date]"
- **IF tickets were created** (check if ${SPEC_PATH}TICKETS_CREATED.md exists):
  - Update ticket status in ticketing system (Linear/GitHub/Jira)
  - Mark milestone story as "Complete" or move to "Done" column
  - Add demo notes/video URL to ticket comments (if applicable)
  - Update related tickets if dependencies unblocked
- Proceed to Milestone N+1 using full-app-11-implement-milestone-n.md

If user requests pivot:
- Return to architecture/design phase for affected documents
- Regenerate ${SPEC_PATH}BUILD_MAP.md with revised plan
- Use full-app-10a-plan-approval-gate-tdd.md for new plan approval
- **IF tickets exist**: Update affected tickets or create new ones with full-app-12-generate-tickets-tdd.md
