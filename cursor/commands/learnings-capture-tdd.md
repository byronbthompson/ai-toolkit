LEARNINGS CAPTURE

Capture learnings continuously throughout development to improve future work.

WHEN TO CAPTURE LEARNINGS:

Capture learnings at these key moments:
1. After each milestone completion (what worked, what didn't)
2. After bug fixes (what prevented detection, how to prevent similar bugs)
3. After encountering unexpected challenges (what was surprising, how resolved)
4. After making architectural decisions (what alternatives considered, why chosen)
5. After completing project (retrospective on entire project)
6. Whenever you discover a better way to do something

WHERE TO CAPTURE:

Create /specs/LEARNINGS.md

Update this file continuously throughout the project (living document).

LEARNINGS DOCUMENT STRUCTURE:

# Learnings - [Project Name]

Project: [ticket-id - short-name]
Started: [date]
Last Updated: [date]

---

## Technical Learnings

### What Worked Well

#### Architectural Decisions
- **[Decision name]**: [What decision was made]
  - Context: [Why decision was needed]
  - Outcome: [How it worked out]
  - Would use again: [Yes/No - when?]
  - Example: "Used FastAPI with async handlers for all API endpoints"
    - Context: Needed high throughput for real-time updates
    - Outcome: Handled 1000 req/sec with minimal resources
    - Would use again: Yes, for any high-performance API

#### Technology Choices
- **[Library/Framework name]**: [What was used]
  - Purpose: [Why chosen]
  - Experience: [How it performed]
  - Gotchas: [Any surprises or issues]
  - Example: "Pydantic for request/response validation"
    - Purpose: Type-safe API contracts with auto-generated docs
    - Experience: Caught many bugs at validation layer, excellent DX
    - Gotchas: Nested model validation errors can be cryptic

#### Patterns and Practices
- **[Pattern name]**: [What pattern was used]
  - Use case: [Where applied]
  - Benefits: [What it enabled]
  - Example: "Repository pattern for all database access"
    - Use case: Abstracted SQLAlchemy behind clean interface
    - Benefits: Easy to test (mock repository), easy to swap DB later

### What Didn't Work Well

#### Challenges Encountered
- **[Challenge name]**: [What problem occurred]
  - Signal flow: [How bug/issue manifested through layers]
  - Root cause: [Deepest layer where problem originated]
  - Resolution: [How fixed]
  - Prevention: [How to avoid in future]
  - Example: "Race condition in async background tasks"
    - Signal flow: UI showed stale data → API returned cached result → Cache updated after response sent
    - Root cause: Cache invalidation happened async, no ordering guarantee
    - Resolution: Added queue with ordering guarantee, cache invalidation synchronous
    - Prevention: Always consider ordering in distributed systems, test concurrent scenarios

#### Technology Issues
- **[Tool/Library name]**: [What didn't work as expected]
  - Issue: [What went wrong]
  - Workaround: [How solved]
  - Better alternative: [What would work better next time]
  - Example: "Redis for session storage"
    - Issue: Connection pool exhaustion under load
    - Workaround: Increased pool size, added connection timeout
    - Better alternative: Use connection pooling library (redis-py with proper config)

#### Anti-Patterns Discovered
- **[Anti-pattern name]**: [What was done that shouldn't have been]
  - Why it was bad: [Negative consequences]
  - How it was fixed: [Refactoring done]
  - Lesson: [What to do instead]
  - Example: "Put business logic in API handlers"
    - Why it was bad: Hard to test, hard to reuse, violates SoC
    - How it was fixed: Extracted to service layer, handlers became thin
    - Lesson: Keep handlers thin, logic in services

### Technical Debt Identified

Document debt that was intentionally created or discovered:

- **[Debt item]**: [Description]
  - Why exists: [Tradeoff made or legacy code]
  - Impact: [How it affects development/maintenance]
  - Recommended fix: [How to address]
  - Priority: [High/Medium/Low]
  - Example: "No caching layer for external API calls"
    - Why exists: MVP shipped without caching to save time
    - Impact: Slow response times, higher external API costs
    - Recommended fix: Add Redis cache with TTL, cache-aside pattern
    - Priority: High (affects user experience and costs)

### Dependencies and Tools

#### Recommended Dependencies
- **[Dependency name] (version)**: [Brief description]
  - Purpose: [What it does]
  - Pros: [Why it's good]
  - Cons: [Any limitations]
  - Would recommend: [Yes/No - for what use cases]

#### Dependencies to Avoid
- **[Dependency name]**: [Why not recommended]
  - Issue: [What problem it caused]
  - Better alternative: [What to use instead]

### Performance Insights

- **[Performance finding]**: [What was learned]
  - Measurement: [Actual metrics - before/after]
  - Cause: [Why performance issue existed]
  - Solution: [How optimized]
  - Example: "Database N+1 queries in user list endpoint"
    - Measurement: 500ms → 50ms (10x improvement)
    - Cause: Loading users then loading roles in loop
    - Solution: Added JOIN to load roles with users in single query

### Security Insights

- **[Security finding]**: [What was discovered]
  - Vulnerability: [What security issue]
  - Fix: [How addressed]
  - Prevention: [How to avoid in future]
  - Example: "SQL injection risk in search endpoint"
    - Vulnerability: String concatenation for WHERE clause
    - Fix: Switched to parameterized queries
    - Prevention: Always use ORM or parameterized queries, never string concat

---

## Workflow Learnings

### Process Improvements Needed

#### Planning Phase Improvements
- **[Improvement suggestion]**: [What should change]
  - Current problem: [What doesn't work well]
  - Suggested solution: [How to improve]
  - Expected benefit: [What would improve]
  - Example: "Add database schema review checkpoint"
    - Current problem: Schema issues discovered during implementation
    - Suggested solution: Add review step after 03_DATA_MODEL.md before implementation
    - Expected benefit: Catch schema issues early, avoid migration headaches

#### Implementation Phase Improvements
- **[Improvement suggestion]**: [What should change]
  - Observation: [What was noticed during implementation]
  - Suggestion: [What could be better]
  - Example: "Milestone size should be smaller"
    - Observation: Milestones taking 6-8 hours made feedback loop too long
    - Suggestion: Break into 2-4 hour milestones for faster validation

#### Testing Phase Improvements
- **[Improvement suggestion]**: [What should change]
  - Gap identified: [What testing was missing]
  - Recommendation: [What to add]
  - Example: "Add load testing requirement for APIs"
    - Gap: Performance issues only found in production
    - Recommendation: Add load test requirement to 08_TESTING_RELEASE.md

#### Documentation Phase Improvements
- **[Improvement suggestion]**: [What should change]
  - Issue: [What documentation was unclear or missing]
  - Fix: [How to improve docs]
  - Example: "Architecture diagrams should include data flow"
    - Issue: Diagrams showed components but not how data moves
    - Fix: Add sequence diagrams or data flow arrows

### Command File Improvements

Document improvements needed to command files:

- **[command-file-name.md]**: [What needs improvement]
  - Current issue: [What's unclear or missing]
  - Suggested addition: [What to add]
  - Rationale: [Why needed]
  - Example: "bug-workflow-builder-tdd.md"
    - Current issue: No guidance on production data analysis
    - Suggested addition: Add section on safe production data querying
    - Rationale: Many bugs require inspecting production data to debug

### Agent Behavior Observations

Document how the agent performed:

#### Effective Agent Behaviors
- **[Behavior]**: [What agent did well]
  - Example: "Agent used Signal Flow analysis to find root cause"
  - Impact: [How it helped]
  - Frequency: [How often did this occur]

#### Agent Improvement Areas
- **[Behavior]**: [What agent struggled with]
  - Example: "Agent didn't ask clarifying questions when requirements ambiguous"
  - Impact: [How it hurt]
  - Suggestion: [How to improve - command file change, prompt change, etc.]

### Time Estimation Accuracy

Track how accurate time estimates were:

- **Milestone [N]**: [Milestone name]
  - Estimated: [X hours]
  - Actual: [Y hours]
  - Variance: [+/- Z hours]
  - Reason for variance: [Why different]
  - Learning: [How to estimate better next time]

### Quality Gate Effectiveness

Track which quality gates caught issues:

- **[Quality gate]**: [Which gate]
  - Issues caught: [How many, what types]
  - False positives: [Any issues flagged incorrectly]
  - Missed issues: [What got through that shouldn't have]
  - Recommendation: [How to improve gate]
  - Example: "Code coverage gate (80% minimum)"
    - Issues caught: 3 untested edge cases in validation logic
    - False positives: None
    - Missed issues: Integration test gaps (unit tests passed but integration failed)
    - Recommendation: Add integration coverage requirement, not just unit

---

## Brownfield-Specific Learnings

(If working with existing codebase)

### Existing Codebase Insights

- **Patterns discovered**: [What patterns exist in codebase]
  - Consistency: [How consistently applied]
  - Quality: [Good/Bad patterns]
  - Recommendation: [Continue using / Refactor away]

- **Integration challenges**: [Issues integrating with existing code]
  - Challenge: [What was difficult]
  - Solution: [How overcame]
  - Prevention: [How to avoid next time]

- **Technical debt encountered**: [Existing debt that affected work]
  - Debt item: [What debt exists]
  - Impact on feature: [How it affected this work]
  - Recommendation: [Should be addressed / can work around]

---

## User Feedback Integration

(Capture feedback received during milestone demos)

### Milestone [N] Feedback
- **Date**: [YYYY-MM-DD]
- **Feedback received**: [What user said]
- **Actions taken**: [How feedback was incorporated]
- **Learning**: [What was learned from feedback]

---

## Metrics and Outcomes

### Quality Metrics

- **Test coverage**: [Final coverage %]
  - Starting coverage: [X%]
  - Ending coverage: [Y%]
  - Change: [+/- Z%]

- **Defects found**: [Number by phase]
  - Planning: [N bugs caught in review]
  - Implementation: [N bugs caught by tests]
  - Manual testing: [N bugs caught by user]
  - Production: [N bugs found in production]

- **Code quality**: [Metrics]
  - Linter violations: [N at start, M at end]
  - Complexity: [Cyclomatic complexity average]

### Velocity Metrics

- **Milestones completed**: [N out of M planned]
- **Average milestone time**: [X hours]
- **Total project time**: [Y hours]
- **Estimated vs actual**: [Z% variance]

### Cost Metrics

(If applicable)

- **Estimated monthly cost**: $X/month
- **Actual monthly cost**: $Y/month (if deployed)
- **One-time costs**: $Z
- **Cost per feature**: $A (total cost / number of features)

---

## Reusable Patterns

Document patterns worth reusing in future projects:

### Code Patterns

```python
# Pattern name: [Name]
# Use case: [When to use]
# Example:

# Repository pattern for database access
class UserRepository:
    def get_by_id(self, user_id: int) -> User:
        # Abstract database access
        pass
```

### Testing Patterns

```python
# Pattern name: [Name]
# Use case: [When to use]
# Example:

# Fixture pattern for test data
@pytest.fixture
def sample_user():
    return User(id=1, email="test@example.com")
```

### Configuration Patterns

```yaml
# Pattern name: [Name]
# Use case: [When to use]
# Example:

# Environment-based configuration
database:
  url: ${DATABASE_URL}
  pool_size: ${DB_POOL_SIZE:-10}
```

---

## Recommendations for Next Project

Based on this project's learnings:

### Do Again
1. [Thing that worked well]
2. [Another thing to repeat]

### Do Differently
1. [Thing to change]
2. [Another improvement]

### Add to Workflow
1. [New practice to adopt]
2. [New checkpoint to add]

### Remove from Workflow
1. [Practice that didn't add value]
2. [Checkpoint that was overkill]

---

## Action Items

Concrete improvements to implement:

- [ ] **[Action item]**: [What to do]
  - Owner: [Who should do it]
  - Priority: [High/Medium/Low]
  - Estimated effort: [Time estimate]
  - Context: [Why needed]

---

## Questions for Future Investigation

Document unknowns or areas for future research:

- **[Question]**: [What to explore]
  - Context: [Why it came up]
  - Potential impact: [Why it matters]
  - Resources: [Where to research]

---

## Appendix: Decisions Made

Reference to 09_DECISIONS.md for architectural decisions.

Quick summary of major decisions:
- [Decision 1]: [Outcome]
- [Decision 2]: [Outcome]

---

**IMPORTANT CAPTURE PRINCIPLES**:

1. **Capture in the moment**: Write learnings when fresh, not at end of project
2. **Be specific**: Vague learnings aren't actionable ("X was good" vs "X reduced bugs by Y%")
3. **Include context**: Future you won't remember why something mattered
4. **Both positive and negative**: Success and failures both teach
5. **Actionable**: Every learning should suggest concrete action or decision
6. **Searchable**: Use consistent terms so learnings can be found later
7. **Link to evidence**: Reference commits, PRs, metrics, conversations
8. **Review periodically**: Re-read learnings at milestone checkpoints

**WHEN TO UPDATE**:
- After each milestone completion (mandatory)
- After bug fixes (capture Signal Flow insights)
- When discovering better approach (capture improvement)
- When user provides feedback (capture and act on)
- At project completion (final retrospective)

**HOW TO USE LEARNINGS**:
- Before starting new project: Review similar past project learnings
- During planning: Apply lessons learned to avoid past mistakes
- After project: Share learnings with team or future self
- Quarterly: Review all learnings to identify systemic improvements
