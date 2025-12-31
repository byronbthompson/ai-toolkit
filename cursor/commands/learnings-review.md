LEARNINGS REVIEW

Periodic review of learnings to drive continuous improvement.

WHEN TO REVIEW LEARNINGS:

1. **After Project Completion** (mandatory)
   - Review all learnings from LEARNINGS.md
   - Identify patterns and systemic issues
   - Create action items for workflow improvements

2. **Quarterly Review** (recommended)
   - Review learnings from all projects in past 3 months
   - Identify recurring themes across projects
   - Update command files based on learnings

3. **Before Starting Similar Project** (recommended)
   - Review learnings from similar past projects
   - Apply lessons learned to planning
   - Avoid repeating past mistakes

---

## POST-PROJECT LEARNINGS REVIEW

After completing a project, conduct comprehensive review:

### 1. REVIEW LEARNINGS DOCUMENT

Read /specs/LEARNINGS.md in full.

Ask these questions:

**Technical Quality**:
- What technical decisions worked well? (repeat these)
- What technical decisions didn't work? (avoid these)
- What technical debt was created? (address or accept)
- What dependencies were problematic? (avoid or prepare for issues)

**Process Quality**:
- Which workflow steps added value? (keep/enhance)
- Which workflow steps were overhead? (streamline/remove)
- Where did agent struggle? (improve command files)
- Where did agent excel? (document and reinforce)

**Time & Effort**:
- Were time estimates accurate? (improve estimation)
- What caused delays? (address root causes)
- What went faster than expected? (leverage in future)

**Quality Gates**:
- Which gates caught issues? (maintain)
- Which gates had false positives? (tune thresholds)
- What got through gates? (add new gates)

### 2. IDENTIFY PATTERNS

Look for recurring themes across milestones and bug fixes:

**Technical Patterns**:
- Same type of bugs occurring multiple times?
  - Example: "3 division-by-zero bugs → need validation framework"
- Same technology causing issues repeatedly?
  - Example: "Redis connection issues in 3 milestones → need better config"
- Same architecture decision paying off?
  - Example: "Repository pattern made testing easy → use always"

**Process Patterns**:
- Same planning step repeatedly unclear?
  - Example: "Unclear how to handle multi-tenancy → add explicit section"
- Same quality gate repeatedly triggering?
  - Example: "Coverage dropping each milestone → set baseline correctly"
- Same type of user feedback?
  - Example: "UI always needs revision → add UI mockup review step"

### 3. CREATE ACTION ITEMS

Generate concrete improvements:

**Command File Updates**:
- [ ] Update [command-file.md]: [what to change and why]
  - Example: "Update bug-workflow-builder.md: Add production data analysis section (gap identified in 3 bug fixes)"

**Workflow Improvements**:
- [ ] Add new step: [what and where]
  - Example: "Add database schema review checkpoint after 03_DATA_MODEL.md (caught issues late 2 times)"

**Template Additions**:
- [ ] Create template: [what template]
  - Example: "Create API endpoint template (repeated similar endpoints 5 times)"

**Documentation Updates**:
- [ ] Update documentation: [what to document]
  - Example: "Document Redis connection pool configuration (caused issues 3 times)"

**Quality Gate Adjustments**:
- [ ] Modify quality gate: [what change]
  - Example: "Increase coverage threshold to 85% (current 80% too low, gaps found)"

### 4. SHARE LEARNINGS

If working with team or multiple projects:

**Create Summary Document**:

```markdown
# Project Learnings Summary - [Project Name]

**Project**: [ticket-id - name]
**Completed**: [date]
**Duration**: [X hours over Y days]

## Top 5 Successes
1. [Success 1 - with metric if possible]
2. [Success 2]
3. [Success 3]
4. [Success 4]
5. [Success 5]

## Top 5 Lessons Learned
1. [Lesson 1 - with recommended action]
2. [Lesson 2]
3. [Lesson 3]
4. [Lesson 4]
5. [Lesson 5]

## Workflow Improvements Implemented
- [Improvement 1]
- [Improvement 2]

## Recommendations for Future Projects
- **Do**: [Thing to repeat]
- **Don't**: [Thing to avoid]
- **Consider**: [Thing to evaluate case-by-case]
```

---

## QUARTERLY LEARNINGS REVIEW

Every 3 months, review learnings across all projects:

### 1. AGGREGATE LEARNINGS

Collect learnings from all projects in quarter:
- Read LEARNINGS.md from each project
- Create master list of learnings by category
- Count frequency of each learning (how many projects saw same issue)

### 2. IDENTIFY SYSTEMIC ISSUES

Look for issues affecting multiple projects:

**High-Frequency Issues** (appeared in 3+ projects):
- [Issue]: [How many projects affected]
  - Pattern: [Common thread across projects]
  - Root cause: [Why this keeps happening]
  - Systemic fix: [How to prevent across all future projects]
  - Example: "Division-by-zero bugs (4 projects) → Create @validate_positive decorator template"

**Technology Issues**:
- [Technology]: [Issues seen]
  - Frequency: [How many projects]
  - Recommendation: [Continue using / Stop using / Use with caution]
  - Example: "Redis connection pooling (3 projects had issues) → Create standard config template"

**Process Gaps**:
- [Gap]: [What's missing]
  - Impact: [How it affected projects]
  - Solution: [What to add to workflow]
  - Example: "No UI mockup review (2 projects had UI revisions) → Add mockup review to 05_UI_UX.md"

### 3. UPDATE COMMAND FILES

Based on quarterly review, update command files:

**Prioritization**:
1. High-frequency issues (affected 3+ projects) → HIGH priority
2. High-impact issues (caused significant delays) → HIGH priority
3. Medium-frequency issues (affected 2 projects) → MEDIUM priority
4. Low-frequency but easy fixes → LOW priority

**Implementation**:
- Create branch for command file updates
- Update relevant command files
- Test updates on sample project
- Commit with clear rationale from learnings

**Example Quarterly Updates**:
```
commit: workflow: add validation framework template

Based on Q1 2024 learnings review:
- 4 projects encountered division-by-zero bugs
- 3 projects had null reference errors
- Pattern: Missing validation for numeric operations

Changes:
- Add validation template to full-app-02-architecture.md
- Add validation requirement to code review checklist
- Add validation decorator examples

Learnings refs:
- Project A: LEARNINGS.md line 45
- Project B: LEARNINGS.md line 23
- Project C: LEARNINGS.md line 67
```

### 4. TRACK IMPROVEMENT IMPACT

After implementing quarterly improvements, track impact:

**Metrics to Track**:
- Issues of type X: [Before: N occurrences] → [After: M occurrences]
- Average project duration: [Before: X hours] → [After: Y hours]
- Quality gate failures: [Before: N failures] → [After: M failures]
- User revisions: [Before: N revisions] → [After: M revisions]

**Review Next Quarter**:
- Did improvements reduce issues?
- New issues emerged?
- Further refinement needed?

---

## BEFORE-PROJECT LEARNINGS REVIEW

Before starting new project, review relevant past learnings:

### 1. IDENTIFY SIMILAR PROJECTS

Find projects with similar characteristics:
- Similar technology stack
- Similar architecture (API, UI, data processing, etc.)
- Similar complexity level
- Similar domain (e-commerce, data analytics, etc.)

### 2. REVIEW RELEVANT LEARNINGS

Read LEARNINGS.md from similar projects, focusing on:

**Technology Choices**:
- What worked well? (consider using)
- What didn't work? (avoid or prepare for issues)
- What alternatives were recommended?

**Architecture Patterns**:
- What patterns were effective? (reuse)
- What patterns were problematic? (avoid)
- What would be done differently?

**Common Pitfalls**:
- What bugs were common? (watch for)
- What was estimated incorrectly? (improve estimates)
- What dependencies had issues? (prepare mitigations)

**Process Insights**:
- What workflow steps were valuable? (ensure included)
- What milestones were too large/small? (size appropriately)
- What quality gates were effective? (configure similarly)

### 3. APPLY LEARNINGS TO PLANNING

Incorporate learnings into project planning:

**In 00_START_HERE.md**:
- Note relevant learnings from similar projects
- Document known risks from past experience
- Set realistic complexity estimate based on similar projects

**In 02_ARCHITECTURE.md**:
- Reference architecture patterns that worked well
- Avoid patterns that were problematic
- Document why choices differ from past if applicable

**In 08_TESTING_RELEASE.md**:
- Include test types that caught bugs in similar projects
- Set coverage thresholds based on what was effective
- Add edge case test categories from past bugs

**In 10_BUILD_MAP.md**:
- Size milestones based on past velocity
- Include checkpoints that caught issues previously
- Allocate buffer for areas that were underestimated before

### 4. DOCUMENT PREEMPTIVE ACTIONS

In LEARNINGS.md for new project, document:

```markdown
## Preemptive Actions (from past project learnings)

Based on learnings from [Similar Project Name]:

**Applied**:
- [Action 1]: [What was done proactively]
  - Rationale: [Past project had issue X, preventing with Y]
- [Action 2]: [What was done proactively]

**Watching For**:
- [Risk 1]: [What to monitor]
  - Past occurrence: [What happened in similar project]
  - Mitigation: [What's in place to catch early]
```

---

## LEARNINGS DATABASE (Optional)

For teams or individuals working on many projects, maintain searchable learnings:

### Structure

```
learnings-database/
├── by-technology/
│   ├── fastapi.md           # All FastAPI learnings
│   ├── react.md             # All React learnings
│   └── postgresql.md        # All PostgreSQL learnings
├── by-pattern/
│   ├── api-design.md        # All API design learnings
│   ├── testing.md           # All testing learnings
│   └── database-schema.md   # All schema design learnings
├── by-issue-type/
│   ├── performance.md       # All performance learnings
│   ├── security.md          # All security learnings
│   └── bugs.md              # All bug pattern learnings
└── index.md                 # Master index with search tips
```

### Maintenance

**After Each Project**:
- Extract key learnings from LEARNINGS.md
- Add to appropriate category files
- Tag with project reference
- Update index

**Quarterly**:
- Review for duplicate/conflicting learnings
- Consolidate similar learnings
- Archive outdated learnings (tech no longer used)
- Update index

**Search and Retrieve**:
- Before projects: "What do we know about PostgreSQL performance?"
- During bugs: "Have we seen this pattern before?"
- During planning: "What worked well for API-heavy projects?"

---

## ACTION ITEM TRACKING

Track implementation of learning-based improvements:

### Action Item Template

```markdown
- [ ] **[Improvement]**: [Description]
  - Source: [Which project(s) learning]
  - Priority: [High/Medium/Low]
  - Effort: [Hours estimated]
  - Owner: [Who implements]
  - Target date: [When to complete]
  - Success metric: [How to measure impact]
  - Status: [Not started / In progress / Done]
```

### Review Cadence

- Weekly: Review high-priority action items
- Monthly: Review all action items, reprioritize
- Quarterly: Review impact of completed items

---

## CONTINUOUS IMPROVEMENT MINDSET

Learnings are only valuable if acted upon:

**Capture**: Write learnings when fresh (during/after work)
**Review**: Regularly revisit learnings (quarterly minimum)
**Act**: Implement improvements based on learnings
**Measure**: Track impact of improvements
**Iterate**: Refine based on what works

**Goal**: Each project should be better than the last due to applied learnings.

---

**REMEMBER**:
- Learnings without action are just notes
- Small improvements compound over time
- Systemic fixes prevent classes of issues
- Sharing learnings multiplies their value
- Past mistakes are future wisdom if captured and applied
