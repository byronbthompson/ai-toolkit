# Verbalized Sampling Guide

## What is Verbalized Sampling?

**Verbalized Sampling** is a prompt engineering technique from Stanford research that significantly improves AI output quality by asking models to:
1. Generate multiple diverse options
2. Provide confidence scores for each option
3. Explain reasoning behind recommendations
4. Acknowledge uncertainty explicitly

**Key Insight**: "Models should learn to interpret intent, not prompts" (Stanford 8-word principle)

**Benefits**:
- 2× more creative output diversity
- Better decision quality (confidence scores reveal uncertainty)
- More thoughtful recommendations (reasoning forces deeper analysis)
- Explicit uncertainty (helps users make informed decisions)

## When to Use Verbalized Sampling

Use verbalized sampling for:
- ✅ **High-impact decisions**: Architecture choices, technology selection, deployment platforms
- ✅ **Multiple valid approaches**: Schema design, implementation patterns, monitoring strategies
- ✅ **"Points of no easy return"**: Decisions that are expensive to change later
- ✅ **Tradeoff-heavy choices**: Performance vs maintainability, cost vs features, speed vs quality

Do NOT use verbalized sampling for:
- ❌ **Simple yes/no questions**: "Should I use TypeScript?"  (just ask directly)
- ❌ **Obvious decisions**: When one option is clearly superior
- ❌ **Low-impact choices**: Naming variables, code formatting
- ❌ **Already decided**: When user has already chosen an approach

## The Verbalized Sampling Pattern

### Template Structure

```markdown
## [DECISION NAME] (Verbalized Sampling)

**Context**: [Why this decision matters, what it affects, points of no return]

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different [philosophies/approaches/tradeoffs], not just minor variations.

For EACH option, provide:

**Option A - [Name]** (Confidence: **[High/Medium/Low]** for [specific condition])

**Best for**: [Ideal use cases, when this shines]

**Pros**:
- [3-5 specific advantages]

**Cons**:
- [3-5 honest tradeoffs]

**Avoid If**:
- [Failure conditions, when NOT to use this]

**Reasoning**: I rate this **[confidence]** because you mentioned "[reference user's stated requirements]". [Explain why confidence is high/medium/low]. However, confidence [changes to X] if "[condition that affects confidence]".

**[Additional context]**: Cost, time, references, etc.

---

[Repeat for Option B, C, etc.]

---

**Recommendation**:
Based on your answers:
- [Requirement 1]: [their answer]
- [Requirement 2]: [their answer]

I recommend **Option [X]** because [specific reasoning tying their requirements to option strengths].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [explain reasoning]. Factors that could change my recommendation:
- If [condition X], consider [alternative option]
- If [requirement Y changes], [different recommendation]

**Question**: "Which option do you prefer? (A/B/C)"

🛑 WAIT FOR CONFIRMATION
```

## Key Components Explained

### 1. Context
Explain WHY this decision matters:
- What does it affect?
- How expensive is it to change later?
- What are the consequences of a poor choice?

**Example**:
```markdown
**Context**: Technology stack is a "point of no easy return" - changing languages/frameworks mid-project requires significant rewrite effort.
```

### 2. Generate Diverse Options
Instruct the AI to create truly different options, not just minor variations:

**Good diversity** (different philosophies):
- Option A: Fully normalized database (purity)
- Option B: Pragmatic denormalization (balance)
- Option C: Performance-optimized denormalization (speed)

**Bad diversity** (minor variations):
- Option A: PostgreSQL 15.1
- Option B: PostgreSQL 15.2
- Option C: PostgreSQL 16.0

### 3. Confidence Scores

Use **Low/Medium/High** format (user preference from our implementation):

**High confidence**: Aligns strongly with user's stated requirements
**Medium confidence**: Works for some scenarios, concerns for others
**Low confidence**: Probably not the right fit, but included for completeness

**Conditional confidence**:
```markdown
(Confidence: **High** for startups, **Low** for enterprise)
```

This shows how confidence changes based on context.

### 4. Best For / Avoid If

**Best For**: Positive framing of ideal use cases
**Avoid If**: Negative framing of failure conditions

Both are essential - they bracket the option's applicability.

**Example**:
```markdown
**Best for**: APIs, event-driven workloads, variable traffic

**Avoid If**:
- You need WebSockets/persistent connections
- Long-running processes (video encoding, batch jobs >15min)
```

### 5. Pros/Cons

Be honest about tradeoffs. Users trust AI more when it acknowledges downsides.

**Pros**: 3-5 specific advantages (not generic praise)
**Cons**: 3-5 honest tradeoffs (not minor quibbles)

**Good**:
```markdown
**Pros**:
- Zero config deployment (connect GitHub, automatic deploys)
- Generous free tier (hobby projects free forever)

**Cons**:
- Vendor lock-in to Vercel platform features
- Serverless functions have cold starts (100-300ms)
```

**Bad** (too generic):
```markdown
**Pros**:
- Easy to use
- Good performance

**Cons**:
- Costs money
- Requires learning
```

### 6. Reasoning (Inline)

Explain WHY you assigned that confidence score, referencing user's requirements:

```markdown
**Reasoning**: I rate this **High confidence** because you mentioned "[reference their app type if frontend]" which aligns perfectly with Vercel's strengths. However, confidence is **Low** if your "[reference app type]" includes backend services, as Vercel's serverless model has limitations.
```

**Key elements**:
- Reference user's stated requirements: `you mentioned "[X]"`
- Explain alignment: `which aligns with...`
- Conditional confidence shift: `However, confidence drops to [Y] if [condition]`

### 7. Recommendation Section

Synthesize user's answers and make a clear recommendation:

```markdown
**Recommendation**:
Based on your answers:
- Performance: [their answer]
- Team expertise: [their answer]
- Budget: [their answer]

I recommend **Option B** because [specific reasoning].
```

### 8. Uncertainty Acknowledgment

Explicitly state what could change the recommendation:

```markdown
**Uncertainty acknowledgment**: I'm moderately confident because [reasoning]. Factors that could change my recommendation:
- If traffic exceeds 100k users, consider migrating to cloud platform
- If budget increases to $500/month, enterprise features become viable
```

This helps users understand the decision is contextual, not absolute.

## Examples from Our Workflows

### Example 1: Technology Stack (full-app-02-architecture-tdd.md)

```markdown
**Option A - Python with FastAPI** (Confidence: **High** if data/ML/API focus, **Medium** for real-time apps)

**Best for**: API-heavy backends, data processing pipelines, ML/AI integration

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their performance answer]" and "[reference their team expertise answer]". FastAPI is battle-tested for [specific use case from their answers]. However, confidence drops to **Medium** if you need WebSocket-heavy real-time features, where Node.js ecosystem is more mature.
```

**Why this works**:
- Conditional confidence (High/Medium based on context)
- References user's actual answers
- Specific reasoning for confidence level
- Clear condition that changes recommendation

### Example 2: Bug Fix Approach (bug-workflow-builder-tdd.md)

```markdown
**Option A - Root Cause Analysis** (Confidence: **High** for most bugs, **Medium** for time-critical fixes)

**Avoid If**:
- Production is down RIGHT NOW (use Option B, then follow up with A)
- Bug affects deprecated feature being removed soon

**Reasoning**: I rate this **High confidence** because you mentioned "[reference bug severity: medium]" and proper root cause fixes prevent recurrence. However, confidence drops to **Medium** if "[time-critical production issue]" where Option B is faster.
```

**Why this works**:
- Clear failure conditions (Avoid If)
- Acknowledges when alternative is better
- Confidence changes with urgency

### Example 3: Data Isolation (full-app-03-data-model-tdd.md)

```markdown
**Option A - Shared schema with tenant_id** (Confidence: **High** for SMB SaaS, **Low** for regulated industries)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their tenant type/size from earlier answers]". Shared schema works well for [X tenants] with [Y data sensitivity]. However, confidence drops to **Low** if you're targeting "[enterprise/regulated industry]" where compliance auditors will scrutinize this choice.
```

**Why this works**:
- Conditional confidence based on industry
- Specific reference to compliance concerns
- Clear conditions for confidence shift

## Implementation Checklist

When implementing verbalized sampling, ensure:

- [ ] **Context explains** why decision matters (points of no return)
- [ ] **Options are diverse** (different philosophies, not variations)
- [ ] **Confidence scores** are conditional (High for X, Low for Y)
- [ ] **Best for / Avoid If** clearly bracket applicability
- [ ] **Pros/Cons** are specific (3-5 each, honest tradeoffs)
- [ ] **Reasoning is inline** (explains WHY that confidence)
- [ ] **References user's answers** explicitly (`you mentioned "[X]"`)
- [ ] **Recommendation synthesizes** user's requirements
- [ ] **Uncertainty is acknowledged** (factors that could change recommendation)

## Common Mistakes to Avoid

### ❌ Mistake 1: Generic Reasoning

**Bad**:
```markdown
**Reasoning**: This is a good option for most use cases.
```

**Good**:
```markdown
**Reasoning**: I rate this **High confidence** because you mentioned "Python backend with ML workloads" and FastAPI excels at async Python APIs. However, confidence drops to **Medium** if real-time WebSockets are critical.
```

### ❌ Mistake 2: False Diversity

**Bad** (minor variations):
- Option A: AWS us-east-1
- Option B: AWS us-west-2
- Option C: AWS eu-west-1

**Good** (different philosophies):
- Option A: PaaS (Vercel/Render) - simplicity over control
- Option B: Cloud (AWS/GCP) - control over simplicity
- Option C: Serverless (Lambda) - pay-per-use over fixed cost

### ❌ Mistake 3: Absolute Confidence

**Bad**:
```markdown
(Confidence: **High**)
```

**Good**:
```markdown
(Confidence: **High** for MVPs, **Medium** for enterprise scale)
```

Confidence is always contextual!

### ❌ Mistake 4: No Uncertainty Acknowledgment

**Bad**: Just recommend option, move on

**Good**:
```markdown
**Uncertainty acknowledgment**: I'm moderately confident because while Option B fits your current requirements, if traffic exceeds 100k users or budget increases, cloud platforms become more viable.
```

## Benefits in Practice

Based on Stanford research and our implementation:

1. **Better decisions**: Confidence scores reveal when AI is uncertain
2. **More creative output**: 2× more diverse options generated
3. **User trust**: Explicit reasoning and uncertainty build credibility
4. **Context awareness**: References to user's answers show understanding
5. **Actionable recommendations**: Clear conditions for when to choose alternatives

## References

- Stanford Research: "Verbalized Sampling" technique (8-word principle)
- Medium Article: [Shared by user in conversation]
- Our Implementation: 7 high-impact workflows enhanced with this pattern

---

**When in doubt**: Use verbalized sampling for high-impact decisions. The extra clarity is worth the additional tokens.
