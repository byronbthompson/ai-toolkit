Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}03_DATA_MODEL.md and draft:

- Entities/tables with key fields
- Relationships
- Constraints
- Index strategy
- Migration strategy + rollback
- Seed data strategy

Review OFFICIAL DB/ORM docs relevant to migrations and constraints.
Present 2–3 schema options (normalized vs pragmatic vs performance) and recommend one.
Record decisions in ${SPEC_PATH}09_DECISIONS.md.
Ask ONE blocking question.
No code changes.

---
**NEXT STEP**: When 03_DATA_MODEL.md is complete and approved, run `full-app-04-api-contract-tdd.md` to design the API contract.
