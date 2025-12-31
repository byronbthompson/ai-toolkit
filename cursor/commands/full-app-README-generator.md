Generate README.md after final milestone completion.

This should be called automatically or when user requests project documentation.

GENERATE COMPREHENSIVE README.md:

Structure the README with the following sections:

# [Project Name]

[1-2 sentence description of what the project does]

## Quick Start

Get up and running in 60 seconds:

```bash
# Clone and setup (if applicable)
git clone [repo-url]
cd [project-dir]

# Environment setup
[exact commands for conda/nvm/virtualenv]

# Install dependencies
[exact commands: pip install -r requirements.txt, npm install, etc.]

# Configure environment
cp .env.example .env
# Edit .env with your values: [list required variables]

# Run database migrations (if applicable)
[exact commands]

# Start the application
[exact command to run]

# Verify it works
[command to test or URL to visit]
```

Expected output:
```
[show what success looks like]
```

## Features

- [Feature 1]: Brief description
- [Feature 2]: Brief description
- [Feature 3]: Brief description

## Architecture

[1 paragraph overview of architecture from 02_ARCHITECTURE.md]

Technology Stack:
- Language: [Python 3.x, Node.js 18.x, etc.]
- Framework: [FastAPI, Express, Flask, etc.]
- Database: [PostgreSQL, MongoDB, etc.]
- Key libraries: [list major dependencies]

## Project Structure

```
project-root/
├── src/              # [description]
├── tests/            # [description]
├── docs/             # [description]
├── config/           # [description]
└── [other dirs]      # [description]
```

## Development

### Prerequisites

- [Python 3.x / Node.js 18.x] (managed with conda/nvm)
- [Database] (version)
- [Other requirements]

### Environment Setup

Detailed setup instructions:

1. Install conda/nvm:
   ```bash
   [exact installation commands or link to official docs]
   ```

2. Create environment:
   ```bash
   [conda create -n project-name python=3.x]
   [nvm install 18]
   ```

3. Activate environment:
   ```bash
   [conda activate project-name]
   [nvm use 18]
   ```

### Running Tests

```bash
# Run all tests
[exact command]

# Run with coverage
[exact command]

# Run specific test file
[exact command]
```

Coverage requirements: 80% minimum (current: X%)

### Code Quality

Linting:
```bash
# Run linter
[pylint src/, npm run lint, etc.]

# Run markdown linter
npx markdownlint "**/*.md"
```

Code style: 4-space indentation, single quotes (where applicable)

## API Documentation

[If API exists]

Base URL: `http://localhost:8000` (development)

### Endpoints

#### [Endpoint 1]
- Method: `GET/POST/etc`
- Path: `/api/v1/resource`
- Description: [what it does]
- Request body (if applicable):
  ```json
  {
    "field": "value"
  }
  ```
- Response:
  ```json
  {
    "result": "value"
  }
  ```

[Repeat for key endpoints, or link to OpenAPI/Swagger docs]

## Database

### Migrations

```bash
# Create new migration
[exact command]

# Run migrations
[exact command]

# Rollback migration
[exact command]
```

### Schema

[Brief description or link to 03_DATA_MODEL.md]

Key tables:
- `users`: [description]
- `items`: [description]

## Configuration

Environment variables (`.env` file):

```bash
# Required
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname
SECRET_KEY=your-secret-key-here

# Optional
LOG_LEVEL=INFO
DEBUG=false
```

## Deployment

[If deployment strategy defined]

### Production Checklist

- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] SSL certificates configured
- [ ] Secrets properly managed
- [ ] Monitoring/logging configured

### Deployment Commands

```bash
[deployment commands or link to deployment guide]
```

## Troubleshooting

Common issues and solutions:

### Issue: [Common error 1]
```
[error message]
```
Solution: [how to fix]

### Issue: [Common error 2]
```
[error message]
```
Solution: [how to fix]

## Contributing

[If open source or team project]

1. Follow code style guidelines (4-space indentation, single quotes)
2. Write tests for new features (TDD approach recommended)
3. Ensure all quality gates pass (tests, coverage, linting)
4. Update documentation for API/feature changes

## License

[License type - ask user, default to MIT for personal projects]

## Architecture Decisions

Key decisions made during development (see `/specs/09_DECISIONS.md` for details):

- [Decision 1]: [Brief rationale]
- [Decision 2]: [Brief rationale]

## Maintenance Guide

### Adding New Features

Follow this pattern:
1. Create feature spec in `/specs/FEATURE_[name].md`
2. Write tests first (TDD) or implement then test
3. Run quality gates (tests, coverage, lint)
4. Update this README if user-facing changes

### Common Tasks

#### Adding a new API endpoint
[Step-by-step guide with code examples]

#### Adding a new database table
[Step-by-step guide with code examples]

#### Modifying existing feature
[Step-by-step guide]

## Performance

[If performance targets defined in 07_SECURITY_NFR.md]

Performance targets:
- API response time: p95 < [X]ms
- Page load time: < [X] seconds
- Database queries: < [X]ms

Current benchmarks: [link to performance tests or results]

## Security

[If security requirements defined]

- Authentication: [method used]
- Authorization: [RBAC model or approach]
- Secrets management: [approach]
- Rate limiting: [if implemented]

Security audits: [when last run, any known issues]

## Support

- Documentation: `/specs/`
- Issues: [GitHub issues or other tracker]
- Contact: [maintainer email or channel]

---

Generated by AI agent on [date]. Last updated: [date].

---

AFTER GENERATING README:

Ask user:
1. "I've generated a comprehensive README.md. Would you like me to create it in the project root?"
2. "Any sections you'd like me to add, remove, or modify?"
3. "Should I also generate additional docs (CONTRIBUTING.md, CHANGELOG.md, etc.)?"

If user approves:
- Write README.md to project root
- Add to git and commit with message: "docs: add comprehensive README"

If user requests changes:
- Update README content
- Re-present for approval
