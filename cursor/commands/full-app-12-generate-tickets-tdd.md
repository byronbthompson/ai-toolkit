# Generate Tickets from Specs (Agile Gold Path)

**PHILOSOPHY**: Fast validation through action, not questions. Create → Review → Refine.

**IMPORTANT**: This is an on-demand command that generates tickets/issues from your planning specs. Run this AFTER BUILD_MAP is complete.

---

## AGILE GOLD PATH APPROACH

**Traditional Approach** (slow validation):
```
Ask 10 questions → Generate preview → Ask more questions → Create all tickets → Hope it's right
```

**Agile Gold Path** (fast validation):
```
Quick setup → Create 1 test ticket → Validate integration → Create remaining tickets → Refine as needed
```

**Benefits**:
- ✅ See real ticket in < 2 minutes (not 10+ minutes of questions)
- ✅ Validate integration works BEFORE bulk creation
- ✅ Fail-fast if credentials/platform issues
- ✅ Iterative refinement (create, review, adjust)
- ✅ Action-oriented prompts (not endless questions)

---

## STEP 1: DETECT SPEC_PATH

Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

If SPEC_PATH not found:
- Ask user: "What is your SPEC_PATH? (e.g., /specs/ or /specs/TICKET-ID/)"
- Set SPEC_PATH to user's answer

---

## STEP 2: LOAD BUILD_MAP

Read ${SPEC_PATH}10_BUILD_MAP.md

If BUILD_MAP.md missing:
- Error: "Cannot generate tickets without BUILD_MAP.md. Please complete planning first (run full-app-10-build-map-tdd.md)."
- Exit

Parse from BUILD_MAP:
- Milestone count
- Milestone titles and descriptions
- Time estimates (AI-assisted)
- Dependencies between milestones

---

## STEP 3: QUICK SETUP (Action-Oriented)

**Present user with action choices** (not questions):

```
📋 TICKET GENERATION SETUP

I found [N] milestones in your BUILD_MAP.

What would you like to do?

A. 🚀 FAST START (Recommended for first-time)
   → Create 1 test ticket in Linear to validate setup
   → Takes < 2 minutes
   → Then create remaining tickets if successful

B. 💪 FULL CREATE (Skip validation)
   → Create all [N] milestones + stories immediately
   → Assumes integration already validated
   → Use if you've done this before

C. 📤 EXPORT ONLY (No integration)
   → Generate markdown + JSON files only
   → No API calls, no ticket creation
   → Use for manual import or documentation

D. ❌ CANCEL
   → Exit without creating tickets
```

🛑 **WAIT FOR CHOICE** → Store as WORKFLOW_MODE

**IF D (Cancel)**: Exit

**IF C (Export Only)**: Skip to STEP 8 (Export)

**IF B (Full Create)**: Go to STEP 5 (Full Create)

**IF A (Fast Start)**: Continue to STEP 4

---

## STEP 4: FAST START - Create Test Ticket

**Purpose**: Validate integration works BEFORE creating all tickets (fail-fast)

### 4.1: Choose Platform (Quick)

**Ask**: "Which platform for tickets?"

```
1. Linear (via Linear MCP)
2. GitHub Issues (via gh CLI)
3. Jira (via Jira MCP)
```

🛑 **WAIT** → Store as PLATFORM

### 4.2: Create Test Ticket

**Say**: "Creating test ticket for Milestone 1 to validate [PLATFORM] integration..."

**Create minimal test ticket**:
- Title: `[TEST] Milestone 1: [Title from BUILD_MAP]`
- Description: `Test ticket from AI agent - validating integration`
- Labels: `test`, `ai-generated`
- No assignment, no sprint, minimal metadata

**Attempt creation via MCP/API**

**IF SUCCESS**:
```
✅ Test ticket created successfully!

Ticket URL: [direct link to ticket]
Ticket ID: [platform ticket ID]

Integration validated! Ready to create remaining tickets.

What would you like to do?

A. ✅ CREATE ALL TICKETS
   → Create tickets for all [N] milestones
   → Use same platform ([PLATFORM])
   → Minimal metadata (can refine later in platform)

B. ⚙️ CREATE WITH METADATA
   → Set tags, sprint, assignee before creating
   → 3-4 quick questions, then create all

C. 🗑️ DELETE TEST & CANCEL
   → Delete test ticket and exit
   → Use if you want to adjust BUILD_MAP first
```

🛑 **WAIT FOR CHOICE** → Store as NEXT_ACTION

**IF C (Delete Test & Cancel)**:
- Delete test ticket via API
- Say: "Test ticket deleted. Exiting."
- Exit

**IF A (Create All Tickets)**:
- Delete test ticket
- Set METADATA_MODE = "minimal"
- Go to STEP 6 (Generate Tickets)

**IF B (Create With Metadata)**:
- Delete test ticket
- Set METADATA_MODE = "enhanced"
- Go to STEP 5 (Metadata Setup)

**IF FAILURE** (test ticket creation failed):
```
❌ Test ticket creation failed!

Error: [error message from platform]

Common issues:
- Linear MCP not configured: Run `mcp install linear`
- GitHub CLI not authenticated: Run `gh auth login`
- Jira MCP not installed: Check MCP configuration
- Invalid credentials or permissions

What would you like to do?

A. 🔄 TRY DIFFERENT PLATFORM
   → Try GitHub/Jira/Linear instead

B. 📤 EXPORT ONLY
   → Generate markdown/JSON files instead
   → No ticket creation

C. ❌ CANCEL
   → Exit and fix integration first
```

🛑 **WAIT** → Handle choice

---

## STEP 5: METADATA SETUP (Enhanced Mode Only)

**Only runs if user chose "Create With Metadata" in Step 4**

**Say**: "Quick metadata setup (3-4 questions)..."

### 5.1: Tags/Labels

**Ask**: "Additional tags for all tickets? (comma-separated or 'none')"

**Context**: Auto-generated tags based on tech stack. Add custom like `sprint-5`, `team-alpha`.

**Example**: `sprint-5, high-priority`

🛑 **WAIT** → Store as CUSTOM_TAGS

### 5.2: Sprint/Cycle

**Ask**: "Assign to sprint/cycle?"

```
A. Specific sprint/cycle (enter name/ID)
B. Backlog (no sprint)
```

🛑 **WAIT** → Store as SPRINT

### 5.3: Assignment

**Ask**: "Assign tickets?"

```
A. Assign all to me
B. Assign to specific user (enter username)
C. Leave unassigned
```

🛑 **WAIT** → Store as ASSIGNMENT

**Say**: "Metadata setup complete. Creating tickets..."

Continue to STEP 6

---

## STEP 6: GENERATE TICKETS

### 6.1: Parse Milestones & Generate Stories

**For each milestone in BUILD_MAP**:

**Auto-detect breakdown strategy** based on time estimate:

- **Small milestone** (< 1 hour): Create 1 story = 1 milestone
- **Medium milestone** (1-3 hours): Create 2-4 stories
- **Large milestone** (> 3 hours): Create 3-6 stories with subtasks

**Generate story content**:
- Title: `[M{num}.{story}] {Feature Name}`
- Description: As a... I want... So that... (from PRD + BUILD_MAP)
- Acceptance Criteria: From BUILD_MAP + PRD
- Estimate: AI-assisted time (from BUILD_MAP)
- Labels: Auto-generated + custom tags
- Dependencies: Auto-detected from BUILD_MAP

### 6.2: Show Progress

**As tickets are created, show real-time progress**:

```
Creating tickets...

✅ Milestone 1: Project Setup (1 story created)
✅ Milestone 2: Database Setup (2 stories created)
⏳ Milestone 3: User Auth (creating...)
```

**This provides fast feedback, not waiting until end**

---

## STEP 7: CREATE TICKETS VIA PLATFORM

### For Linear (via Linear MCP):

**Process**:
1. Create milestone (project or parent issue)
2. Create stories linked to milestone
3. Set metadata (labels, sprint, assignee)
4. Link dependencies (blocks/blocked by)

**Error Handling**:
- If ticket fails, log error and continue
- Track failed tickets for retry
- Show summary at end

### For GitHub Issues (via gh CLI):

**Process**:
1. Create milestone: `gh issue milestone create`
2. Create issues linked to milestone
3. Add labels, assignee
4. Link dependencies via issue references

### For Jira (via Jira MCP):

**Process**:
1. Create epic (milestone)
2. Create stories under epic
3. Set sprint, assignee
4. Link dependencies

---

## STEP 8: EXPORT (if Export Only mode)

**Generate files**:

1. **${SPEC_PATH}TICKETS.md** - Full ticket details in markdown
2. **${SPEC_PATH}TICKETS.json** - Machine-readable export
3. **${SPEC_PATH}TICKETS.csv** - Spreadsheet import format

**Say**:
```
✅ Export complete!

Files created:
- ${SPEC_PATH}TICKETS.md (human-readable)
- ${SPEC_PATH}TICKETS.json (machine-readable)
- ${SPEC_PATH}TICKETS.csv (spreadsheet import)

You can now manually import these into your ticketing system.
```

Exit

---

## STEP 9: SAVE REFERENCES & CONFIRM

**Create ${SPEC_PATH}TICKETS_CREATED.md**:

```markdown
# Created Tickets

**Platform**: [Linear/GitHub/Jira]
**Created**: [Date/Time]
**Workflow**: [Fast Start/Full Create/Export]

## Milestone 1: [Title]

**Milestone ID**: [ID]
**URL**: [Link]

### Story 1.1: [Title]
**Story ID**: [ID]
**URL**: [Link]
**Status**: Open
**Assigned**: [User or Unassigned]

---

[Repeat for all tickets]

## Summary

- Total Milestones: [N]
- Total Stories: [M]
- Failed: [X] (see errors below)

## Errors

[List any failed tickets]
```

**Show confirmation**:

```
✅ Ticket generation complete!

Summary:
- ✅ [M] stories created across [N] milestones
- 📊 Platform: [Linear/GitHub/Jira]
- ⏱️ Total estimate: [X-Y hours] AI-assisted
- ❌ [F] failed (see TICKETS_CREATED.md)

📋 View tickets:
- ${SPEC_PATH}TICKETS_CREATED.md (IDs and URLs)

🔗 Direct links:
[List milestone URLs]

💡 Next steps:
1. Review tickets in [platform]
2. Refine in platform (priorities, estimates, assignments)
3. Start implementation: full-app-11-implement-milestone-n.md

[IF failures]:
⚠️ Some tickets failed to create. See TICKETS_CREATED.md for details.
Retry? (yes/no)
```

🛑 **WAIT** (if failures) → Offer retry

---

## STEP 10: ITERATIVE REFINEMENT (Optional)

**After tickets are created, user can**:

A. **Refine in platform** (recommended)
   - Adjust priorities, estimates, assignments directly in Linear/GitHub/Jira
   - More efficient than regenerating

B. **Update from specs**
   - If BUILD_MAP changes, re-run this command
   - Will detect existing tickets (from TICKETS_CREATED.md)
   - Offer to update existing or create new

C. **Export for documentation**
   - Even if tickets already in Linear, can export markdown/JSON
   - Useful for documentation or backup

---

## AGILE GOLD PATH SUMMARY

**Traditional Flow** (10-15 minutes):
```
Load specs → Answer 10 questions → Generate preview → Review → Answer more questions → Create all → Hope it works
```

**Agile Gold Path** (2-5 minutes):
```
Load specs → Choose Fast Start → Create test ticket (validate) → Create all → Refine in platform
```

**Key Differences**:
1. **Fast validation**: Test ticket created in < 2 min
2. **Fail-fast**: Integration validated before bulk creation
3. **Action-oriented**: User chooses actions, not answering questions
4. **Progressive**: Minimal metadata first, refine later in platform
5. **Iterative**: Create → Review → Adjust cycle

---

## COMPARISON

### Before (Question-Heavy)
```
Step 1: Load specs
Step 2: Parse milestones
Step 3: Generate preview
Step 4: Wait for approval
Step 5: Ask platform (1 question)
Step 6: Ask tags (1 question)
Step 7: Ask sprint (1 question)
Step 8: Ask assignment (1 question)
Step 9: Ask priority (1 question)
Step 10: Ask team (1 question)
Step 11: Ask custom fields (1 question)
Step 12: Create all tickets
Step 13: Hope integration works
Step 14: Handle failures retroactively

Total: 7 questions, 10-15 min wait time
Risk: Integration fails after all questions answered
```

### After (Action-Oriented, Agile Gold Path)
```
Step 1: Load specs
Step 2: Quick setup (choose action: Fast Start/Full/Export)
Step 3: [If Fast Start] Create test ticket → validate integration
Step 4: [If success] Create all tickets with minimal metadata
Step 5: Refine in platform (more efficient than questions)

Total: 1-2 actions, 2-5 min to first ticket
Risk: Validated in Step 3 (fail-fast)
```

---

## BEST PRACTICES

### When to Use Fast Start

**Use Fast Start if**:
- ✅ First time using ticket generation
- ✅ New platform or changed credentials
- ✅ Want to validate before bulk creation
- ✅ Unsure if integration works

### When to Use Full Create

**Use Full Create if**:
- ✅ Previously validated integration
- ✅ Same platform, same project
- ✅ Confident setup is correct
- ✅ Want fastest path (skip test)

### When to Use Export Only

**Use Export Only if**:
- ✅ No MCP/integration available
- ✅ Want documentation backup
- ✅ Manual import to ticketing system
- ✅ Review before creating

---

## TROUBLESHOOTING

### Test Ticket Fails

**If test ticket creation fails**:
1. Check error message for cause
2. Verify MCP server running (`mcp list`)
3. Verify credentials configured
4. Try different platform
5. Fall back to Export Only

### Partial Failures

**If some tickets create, some fail**:
1. Review TICKETS_CREATED.md for errors
2. Retry failed tickets
3. Create manually in platform
4. Update TICKETS_CREATED.md with manual IDs

### Duplicate Tickets

**If tickets already exist**:
1. Check if TICKETS_CREATED.md exists
2. Offer to update existing tickets
3. Or create with different titles (append date)

---

## NOTES

- **No code changes**: Only creates tickets, no application code modified
- **Fast validation**: See real ticket in < 2 minutes
- **Fail-fast**: Integration validated before bulk creation
- **Iterative**: Can refine tickets in platform after creation
- **Flexible**: Fast Start, Full Create, or Export Only modes
- **Action-oriented**: User chooses actions, not endless questions

---

**NEXT STEP**: After tickets created, implement milestones with `full-app-11-implement-milestone-n.md`
