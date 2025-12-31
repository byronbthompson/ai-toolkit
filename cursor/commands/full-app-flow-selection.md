full-app-00-flow-selection

you are my planning partner for a complete, greenfield app build.

context:

- i just picked up a new ticket/project and will build all planning docs with you first.
- this workflow must be executable by a single developer.
- all work is agent-assisted and planning-first.

tasks:

1) confirm this is a "full app build" (not feature/story/bug/devops). if ambiguous, ask one blocking question and stop.
2) ask clarifying questions to determine the best technology stack:
   - what is the primary use case and performance requirements?
   - any existing infrastructure or team expertise constraints?
   - python is preferred when it makes sense (data processing, apis, ml/ai, rapid prototyping)
   - environment management: conda for python projects, nvm for node.js projects
   - present 2-3 stack options with tradeoffs based on answers
3) propose the minimal required and optional docs for a full app build (do not compress docs).
4) generate the folder skeleton and empty files under:
   /specs/
   include all required docs and the optional docs as empty files ONLY if you explain why they're needed and i approve them.
5) initialize git repository if not already initialized (git init, create .gitignore)
6) after creating files, output a checklist of what exists and what remains to be authored.

rules (must follow):

- ask blocking clarifying questions one at a time.
- review official documentation when proposing options later in the process.
- present 2–4 options with tradeoffs before decisions.
- record decisions in /specs/09_DECISIONS.md.
- do not write any application code yet. docs and scaffolding only.

required files for full app build (create empty files now):

- 00_START_HERE.md
- 01_PRD.md
- 02_ARCHITECTURE.md
- 03_DATA_MODEL.md
- 04_API_CONTRACT.md
- 05_UI_UX.md
- 07_SECURITY_NFR.md
- 08_TESTING_RELEASE.md
- 09_DECISIONS.md
- BUILD_MAP.md

environment files (create based on tech stack):
- Python: environment.yml or requirements.txt (conda preferred)
- Node.js: .nvmrc

version control files (always create):
- .gitignore (appropriate for tech stack)
- initialize git repository (git init) if not already present

linter configuration (always create):
- Python: .pylintrc, .flake8, pyproject.toml (ruff), or similar
- Node.js: .eslintrc.json or .eslintrc.js
- Markdown: .markdownlint.json (always create with base config)
- Configure style preferences: 4-space indentation, single quotes (where appropriate)
- Document linter choice in 09_DECISIONS.md

markdown linter config (always create .markdownlint.json):
{
    "default": true,
    "MD013": false,
    "MD024": false,
    "MD032": false,
    "MD033": {
        "allowed_elements": ["br"]
    },
    "MD036": false,
    "MD040": false,
    "MD060": false
}

optional files (do not create unless approved):

- 06_DESIGN_SYSTEM.md
- 06A_DESIGN_TOKENS.json
- README.md

output format:

- first: the exact mkdir/touch commands to create the folder + required empty files
- second: a file tree of /specs/
- third: one blocking clarifying question (if needed), otherwise ask for the ticket text
