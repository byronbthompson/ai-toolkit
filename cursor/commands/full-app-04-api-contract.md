Open /specs/04_API_CONTRACT.md and draft:

- Auth model (how requests are authenticated)
- RBAC rules (authorization checks)
- Endpoints list
- Request/response shapes (examples)
- Error format standard
- Pagination/filtering rules
- Versioning strategy

If API framework not yet chosen, ask:
- REST, GraphQL, or gRPC? (consider client needs and team expertise)
- Sync vs async patterns needed?
- Python/FastAPI fits well for: rapid development, type safety, async support, OpenAPI auto-gen
- Consider alternatives if: extreme performance needs, existing team expertise elsewhere

Review OFFICIAL API framework + auth provider docs for best practices.
Present 2–3 API design options and recommend one.
Record decisions in 09_DECISIONS.md.
Ask ONE blocking question.
No code changes.
