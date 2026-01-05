Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Add a new entry to ${SPEC_PATH}09_DECISIONS.md using the Architecture Decision Record (ADR) format with confidence scores:

## Decision Template

Use this format for each decision:

```markdown
## [Decision Number]. [Decision Title]

**Date**: YYYY-MM-DD

**Status**: [Proposed | Accepted | Deprecated | Superseded]

**Confidence**: [Low | Medium | High]

### Context

[Describe the issue or problem that prompted this decision. Include relevant background, constraints, and requirements that influenced the decision.]

### Options Considered

#### Option A: [Name] (Confidence: [High/Medium/Low])

**Pros**:
- [Advantage 1]
- [Advantage 2]
- [Advantage 3]

**Cons**:
- [Tradeoff 1]
- [Tradeoff 2]
- [Tradeoff 3]

**Reasoning**: [Why this confidence level based on context]

#### Option B: [Name] (Confidence: [High/Medium/Low])

[Same structure as Option A]

#### Option C: [Name] (Confidence: [High/Medium/Low])

[Same structure as Option A, if applicable]

### Decision

**Chosen**: Option [A/B/C] - [Name]

**Rationale**: [Explain WHY this option was chosen over alternatives. Reference specific requirements, constraints, or priorities that made this the best choice.]

### Consequences

**Positive**:
- [Benefit 1]
- [Benefit 2]

**Negative**:
- [Tradeoff 1 we're accepting]
- [Tradeoff 2 we're accepting]

**Risks**:
- [Risk 1 and mitigation]
- [Risk 2 and mitigation]

### Implementation Notes

- [Key implementation detail 1]
- [Key implementation detail 2]
- [Migration path if changing from previous decision]

### References

- [Link to official documentation]
- [Link to relevant architecture doc section]
- [Link to research/article that informed decision]

### Uncertainty & Future Review

**Factors that could change this decision**:
- [Condition 1 that would invalidate this choice]
- [Condition 2 that would require reconsideration]

**Review by**: [Date or milestone when this should be reviewed]
```

## Example Decision Record

```markdown
## 1. Technology Stack - Backend Framework

**Date**: 2024-01-15

**Status**: Accepted

**Confidence**: High

### Context

We need to choose a backend framework for our SaaS application. Requirements include:
- REST API with 50+ endpoints
- Real-time features (WebSocket support)
- Team expertise: 3 developers with JavaScript experience, 1 with Python
- Expected scale: 10k users initially, 100k within 2 years
- Performance requirement: <200ms API response time (p95)
- Budget: $100-500/month infrastructure

### Options Considered

#### Option A: FastAPI (Python) (Confidence: Medium)

**Pros**:
- Excellent automatic OpenAPI documentation
- Type hints with Pydantic for validation
- Async support built-in
- Fast development velocity

**Cons**:
- Only 1 team member knows Python well
- Learning curve for JavaScript-focused team
- Fewer WebSocket libraries than Node.js

**Reasoning**: Medium confidence because while FastAPI matches performance requirements and provides excellent DX, team expertise is primarily JavaScript which introduces learning overhead.

#### Option B: Express.js with TypeScript (Confidence: High)

**Pros**:
- Team already knows JavaScript (3/4 developers)
- Massive npm ecosystem (Socket.io for WebSockets)
- TypeScript adds type safety
- Proven at scale (used by Netflix, Uber)

**Cons**:
- Callback complexity (requires discipline)
- Need to add OpenAPI documentation manually
- npm dependency management overhead

**Reasoning**: High confidence because aligns with team's existing expertise, has mature WebSocket support (Socket.io), and meets all performance requirements. Risk is mitigated by using TypeScript for type safety.

#### Option C: Go with Gin (Confidence: Low)

**Pros**:
- Excellent performance (10x faster than Node.js for CPU tasks)
- Built-in concurrency
- Compile-time type checking

**Cons**:
- No team expertise in Go (learning curve: 3-4 weeks)
- Delays project start by 1 month minimum
- Smaller ecosystem than JavaScript/Python

**Reasoning**: Low confidence because while Go matches performance requirements, zero team expertise introduces significant risk and delays. Would reconsider if performance becomes critical bottleneck.

### Decision

**Chosen**: Option B - Express.js with TypeScript

**Rationale**: Aligns with team's existing JavaScript expertise (75% of team), has mature ecosystem for our requirements (Socket.io for WebSockets, extensive npm packages), and meets performance requirements. TypeScript mitigates dynamic typing risks. Starting with team's strengths allows faster delivery while maintaining quality.

### Consequences

**Positive**:
- Team productive from day 1 (no ramp-up time)
- Rich ecosystem for rapid feature development
- TypeScript provides type safety and better IDE support
- Socket.io provides battle-tested WebSocket support

**Negative**:
- Need to manually add OpenAPI documentation (not auto-generated)
- npm dependency management requires discipline
- Slightly slower than Go for CPU-bound tasks (acceptable for our workload)

**Risks**:
- Callback hell: Mitigated by using async/await consistently and ESLint rules
- Type safety gaps: Mitigated by strict TypeScript config and runtime validation with Zod
- npm supply chain: Mitigated by using npm audit and Snyk for security scanning

### Implementation Notes

- Use Express.js 4.x with TypeScript 5.x
- Socket.io 4.x for WebSocket support
- Zod for runtime validation (complements TypeScript)
- ESLint + Prettier for code quality
- Set up OpenAPI spec manually using swagger-jsdoc

### References

- Express.js: https://expressjs.com/
- TypeScript: https://www.typescriptlang.org/
- Socket.io: https://socket.io/
- Team expertise survey: [internal doc]

### Uncertainty & Future Review

**Factors that could change this decision**:
- If performance degrades below 200ms p95, consider migrating hot paths to Go microservices
- If team grows to include Go experts, reconsider for new services
- If CPU-bound workloads increase, may need Go for those specific services

**Review by**: After Milestone 3 (when we have real production metrics)
```

## Key Principles for Decision Records

1. **Confidence scores are contextual**: Always explain WHY that confidence level
2. **Reference user requirements**: Tie decisions to actual stated needs
3. **Acknowledge uncertainty**: List conditions that would change the decision
4. **Include all options**: Even rejected options (with reasoning)
5. **Update over time**: Mark decisions as Deprecated/Superseded when changed
6. **Keep it real**: Honest about tradeoffs, not just selling the chosen option

## When to Create a Decision Record

Create an ADR for:
- ✅ Technology choices (languages, frameworks, databases)
- ✅ Architecture patterns (monolith vs microservices, event-driven, etc.)
- ✅ Infrastructure decisions (cloud providers, deployment strategies)
- ✅ Security approaches (authentication methods, encryption strategies)
- ✅ "Points of no easy return" (expensive to change later)

Skip ADRs for:
- ❌ Code formatting preferences (use linter config)
- ❌ Naming conventions (use style guide)
- ❌ Obvious choices with no alternatives
