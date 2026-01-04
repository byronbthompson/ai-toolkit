Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Review ${SPEC_PATH}01_PRD.md and ${SPEC_PATH}02_ARCHITECTURE.md for context on:
- Entities/domain objects mentioned in PRD
- Technology stack chosen (database, ORM)
- Multi-tenancy decisions (if applicable)
- Performance requirements

---

## PHASE 1: DISCOVER DATA REQUIREMENTS (Query First)

### Question 1: Expected Data Volume

"What data volume do you expect?"

**Options to consider**:
- Small (< 100k records total, < 10GB database)
- Medium (100k-10M records, 10GB-100GB database)
- Large (10M-1B records, 100GB-1TB database)
- Very Large (> 1B records, > 1TB database, needs sharding)

🛑 WAIT FOR ANSWER - Record answer before proceeding

### Question 2: Read vs Write Patterns

"What are your expected read vs write patterns?"

**Options to consider**:
- Read-heavy (90% reads, 10% writes) - typical SaaS apps, blogs, e-commerce
- Balanced (50/50 reads/writes) - collaborative tools, messaging
- Write-heavy (10% reads, 90% writes) - analytics, logging, IoT sensors
- Real-time writes (sub-second latency required) - chat, gaming, trading

🛑 WAIT FOR ANSWER - Record answer before proceeding

### Question 3: Data Access Patterns

"How will data primarily be accessed?"

**Options to consider**:
- Simple lookups (by ID, single-table queries)
- Complex joins (multi-table queries, reporting)
- Full-text search (search across text fields)
- Time-series data (ordered by timestamp, range queries)
- Aggregations (COUNT, SUM, AVG queries)

🛑 WAIT FOR ANSWER - Record answer before proceeding

---

## PHASE 2: SCHEMA DESIGN APPROACH (Verbalized Sampling)

Based on your answers:
- Data volume: [their answer]
- Read/Write patterns: [their answer]
- Access patterns: [their answer]

### DECISION REQUIRED: Schema Design Philosophy

**Context**: Schema design is a "point of difficult return" - changing schema philosophy mid-project requires extensive migrations and refactoring.

**Generate Diverse Options**:
Think deeply about diversity - ensure options represent different design philosophies (purity vs pragmatism vs performance).

For EACH option, provide:

**Option A - Fully Normalized (3NF/BCNF)** (Confidence: **High** for complex domains, **Low** for performance-critical apps)

**Best for**: Complex business domains, data integrity critical, CRUD apps, long-term maintainability

**Pros**:
- Eliminates data redundancy (DRY principle for data)
- Strong data integrity (updates in one place only)
- Easier to maintain consistency (no sync issues)
- Flexible for future queries (can join any relationship)
- Industry best practice for relational data

**Cons**:
- Query performance suffers (many JOINs required)
- Complex queries harder to write (4-5 table joins common)
- ORM can generate N+1 queries (performance pitfall)
- Harder to scale horizontally (joins don't shard well)
- May need materialized views for reporting

**Avoid If**:
- Performance is critical (< 50ms query latency required)
- Primarily read-heavy workload (denormalization helps reads)
- You expect massive scale requiring sharding

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their domain complexity]" and "[reference data integrity needs]". Normalized schemas excel when data consistency is paramount. However, confidence drops to **Low** if you stated "[high performance requirements]" as JOINs become bottleneck at scale.

**Example (Normalized)**:
```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMP
);

-- User profiles (separate table, 1:1 relationship)
CREATE TABLE user_profiles (
  user_id UUID PRIMARY KEY REFERENCES users(id),
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  bio TEXT
);

-- User addresses (separate table, 1:many relationship)
CREATE TABLE user_addresses (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  street VARCHAR(255),
  city VARCHAR(100),
  country VARCHAR(100)
);

-- Query requires JOIN
SELECT u.email, p.first_name, a.city
FROM users u
JOIN user_profiles p ON u.id = p.user_id
JOIN user_addresses a ON u.id = a.user_id
WHERE u.id = $1;
```

**Implementation effort**: Medium (more tables, more migrations)

**Reference**: https://en.wikipedia.org/wiki/Third_normal_form

---

**Option B - Pragmatic Denormalization** (Confidence: **High** for most SaaS apps, **Medium** for data-intensive apps)

**Best for**: Typical SaaS apps, read-heavy workloads, performance matters, pragmatic teams

**Pros**:
- Balanced performance and maintainability
- Fewer JOINs (denormalize common access patterns)
- Faster queries for common operations
- Still reasonably normalized (avoid extreme duplication)
- Easier ORM mapping (objects map to tables naturally)

**Cons**:
- Some data duplication (must keep in sync)
- Update anomalies possible (if denormalization done poorly)
- Requires discipline (know when to denormalize)
- Migration complexity (changing denormalized data harder)

**Avoid If**:
- Absolute data integrity required (financial transactions, healthcare records)
- Domain is extremely complex (100+ entities with intricate relationships)
- You lack database expertise (easy to denormalize incorrectly)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference their app type]" which is a typical "[SaaS/web app]" with "[read-heavy patterns]". Pragmatic denormalization gives you 80% of normalization's benefits with better performance. Confidence is **Medium** if data volume is "[very large]" where you may need full denormalization.

**Example (Pragmatic)**:
```sql
-- Users table with embedded common profile fields
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  first_name VARCHAR(100),  -- Denormalized (was in user_profiles)
  last_name VARCHAR(100),   -- Denormalized
  bio TEXT,                 -- Denormalized
  primary_address JSONB,    -- Denormalized (store most-used address)
  created_at TIMESTAMP
);

-- Addresses table only if user has multiple addresses
CREATE TABLE user_addresses (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  street VARCHAR(255),
  city VARCHAR(100),
  country VARCHAR(100),
  is_primary BOOLEAN DEFAULT FALSE
);

-- Query is simpler (no JOIN for common case)
SELECT email, first_name, primary_address
FROM users
WHERE id = $1;
```

**Implementation effort**: Low-Medium (fewer tables than fully normalized)

**Reference**: https://www.postgresql.org/docs/current/datatype-json.html

---

**Option C - Performance-Optimized Denormalization** (Confidence: **High** for high-scale/analytics, **Low** for complex writes)

**Best for**: High-scale reads, analytics, reporting, performance-critical apps, time-series data

**Pros**:
- Maximum query performance (minimal/no JOINs)
- Scales horizontally (easier to shard)
- Fast reads (everything in one table or few tables)
- Great for analytics (denormalized = fast aggregations)
- Supports caching well (less data to invalidate)

**Cons**:
- Significant data duplication (same data in multiple tables)
- Complex writes (must update multiple locations)
- Update anomalies (data can get out of sync)
- Larger database size (duplication increases storage)
- Harder to maintain consistency

**Avoid If**:
- Frequent updates to denormalized data (sync overhead kills performance)
- Strong ACID requirements (denormalization makes transactions complex)
- Limited storage budget (duplication increases costs)

**Reasoning**: I rate this **High confidence** because you mentioned "[reference if high scale/analytics mentioned]" and "[reference read-heavy patterns]". Performance-optimized schemas trade storage/complexity for speed. However, confidence is **Low** if you have "[frequent writes to shared data]" where sync overhead becomes bottleneck.

**Example (Performance-Optimized)**:
```sql
-- Denormalized user view (everything in one table for fast reads)
CREATE TABLE user_view (
  id UUID PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  bio TEXT,
  primary_street VARCHAR(255),  -- Fully denormalized
  primary_city VARCHAR(100),
  primary_country VARCHAR(100),
  total_orders INT,             -- Denormalized aggregate
  last_order_date TIMESTAMP,    -- Denormalized
  created_at TIMESTAMP
);

-- Materialized view for analytics (pre-computed aggregations)
CREATE MATERIALIZED VIEW user_stats AS
SELECT
  date_trunc('day', created_at) as signup_date,
  COUNT(*) as signups,
  COUNT(CASE WHEN total_orders > 0 THEN 1 END) as active_users
FROM user_view
GROUP BY date_trunc('day', created_at);

-- Query is extremely fast (single table, no JOIN)
SELECT * FROM user_view WHERE id = $1;

-- Analytics query is fast (pre-computed)
SELECT * FROM user_stats WHERE signup_date >= '2024-01-01';
```

**Implementation effort**: High (must maintain denormalized data, triggers/cron to sync)

**Reference**: https://www.postgresql.org/docs/current/rules-materializedviews.html

---

**Recommendation**:
Based on your answers:
- Data volume: [their answer]
- Read/Write patterns: [their answer]
- Access patterns: [their answer]
- Performance requirements: [reference from architecture doc]

I recommend **Option [A/B/C]** because [specific reasoning tying requirements to option].

**Uncertainty acknowledgment**: I'm [confident/moderately confident/uncertain] because [explain reasoning]. Factors that could change my recommendation:
- If data volume exceeds [X records], consider [more denormalized option]
- If write patterns become heavier, consider [more normalized option]
- If performance requirements tighten to [X ms], must use [Option C]

**Question**: "Which schema design approach do you prefer? (A/B/C)"

🛑 WAIT FOR CONFIRMATION - Do not proceed until explicit choice is made

Once confirmed, record decision in ${SPEC_PATH}09_DECISIONS.md with:
- Schema philosophy chosen (Normalized/Pragmatic/Performance)
- Reasoning (tying to data volume, access patterns, performance needs)
- Tradeoffs acknowledged
- Confidence score: [Low/Medium/High]

---

## PHASE 3: DESIGN DATA MODEL

Now that schema philosophy is chosen, design the actual data model:

### Deliverables for ${SPEC_PATH}03_DATA_MODEL.md

1. **Entity-Relationship Diagram**
   - All entities/tables identified from PRD
   - Relationships (1:1, 1:many, many:many)
   - Cardinality and optionality

2. **Table Definitions**
   - For EACH table:
     - Table name (plural, snake_case convention)
     - All columns with types
     - Primary key
     - Foreign keys
     - Constraints (NOT NULL, UNIQUE, CHECK)
     - Indexes (single-column, composite, partial)

3. **Multi-Tenancy Integration** (if applicable from architecture doc)
   - If shared schema with tenant_id:
     - Add tenant_id column to all tenant-scoped tables
     - Add composite indexes: (tenant_id, other_columns)
     - Add Row-Level Security (RLS) policies
   - If schema-per-tenant:
     - Document schema switching strategy
   - If database-per-tenant:
     - Document tenant database provisioning

4. **Index Strategy**
   - Primary indexes (for lookups by ID)
   - Foreign key indexes (for JOIN performance)
   - Query-specific indexes (based on access patterns from Phase 1)
   - Partial indexes (for filtered queries)
   - Avoid over-indexing (every index slows writes)

5. **Migration Strategy**
   - Migration tool: [Alembic (Python), Prisma, TypeORM, Flyway, Liquibase]
   - Migration file naming convention
   - Rollback strategy (every migration must be reversible)
   - Data migration approach (for schema changes with existing data)
   - Zero-downtime migration approach (for production)

6. **Seed Data Strategy**
   - What data is seeded (default roles, admin user, lookup tables)
   - Seed script location
   - Idempotent seeds (can run multiple times safely)

7. **Review Official Database/ORM Documentation**
   - PostgreSQL docs on data types, constraints, indexes: https://www.postgresql.org/docs/current/
   - ORM migration docs (Alembic, Prisma, TypeORM, etc.)
   - Best practices for chosen database

### Example Table Definition

```sql
-- Example: Users table (pragmatic denormalization)
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id UUID NOT NULL REFERENCES tenants(id),  -- Multi-tenancy
  email VARCHAR(255) NOT NULL,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  password_hash VARCHAR(255) NOT NULL,
  email_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),

  -- Constraints
  CONSTRAINT unique_email_per_tenant UNIQUE (tenant_id, email),
  CONSTRAINT email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$')
);

-- Indexes
CREATE INDEX idx_users_tenant_id ON users(tenant_id);
CREATE INDEX idx_users_email ON users(tenant_id, email);  -- Composite for tenant-scoped lookups
CREATE INDEX idx_users_created_at ON users(created_at DESC);  -- For sorting by recent

-- Row-Level Security (RLS) for multi-tenancy
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
CREATE POLICY tenant_isolation ON users
  USING (tenant_id = current_setting('app.current_tenant')::UUID);
```

---

## PHASE 4: VALIDATION & APPROVAL

After drafting complete data model in ${SPEC_PATH}03_DATA_MODEL.md:

### Ask ONE Blocking Question

If any critical decision is ambiguous, ask ONE question before finalizing:

**Examples**:
- "I noticed [entity X] could be modeled as [option A] or [option B]. Which approach do you prefer?"
- "Should [relationship] be hard-delete or soft-delete (deleted_at timestamp)?"
- "Do you need audit logging (who created/modified each record)?"

🛑 WAIT FOR ANSWER if question asked

### Present Summary for Approval

**Data Model Summary**:
- Total tables: [X]
- Total relationships: [Y]
- Schema approach: [Normalized/Pragmatic/Performance]
- Multi-tenancy: [Yes/No, model if yes]
- Migration tool: [Tool chosen]
- Estimated database size at [scale from Phase 1]: [XGB]

**Question**: "Does this data model align with your requirements? Approve or request changes?"

🛑 WAIT FOR FINAL APPROVAL

Record final approval in ${SPEC_PATH}09_DECISIONS.md.

**No code changes at this stage** - only documentation and design.

---
**NEXT STEP**: When 03_DATA_MODEL.md is complete and approved, run `full-app-04-api-contract-tdd.md` to design the API contract.
