# Nested /specs Folder Structure

## Overview

The full-app workflow now supports **nested documentation structure** to enable multiple engineers to work on different tickets/features in parallel without merge conflicts.

---

## How It Works

### Step 1: Choose Structure (in full-app-00-start-here-tdd.md)

The AI will ask: **"Do you want to organize specs in a nested folder structure (for parallel work) or flat structure?"**

**Option A - Nested Structure** (RECOMMENDED for team/parallel work):
- Path: `/specs/[ticket-id]/[doc-name].md`
- Example: `/specs/PROJ-123/00_START_HERE.md`
- Benefits: Multiple engineers can work on different tickets without conflicts
- Use case: Team environments, multiple features in parallel

**Option B - Flat Structure** (traditional):
- Path: `/specs/[doc-name].md`
- Example: `/specs/00_START_HERE.md`
- Benefits: Simpler structure for single developer
- Use case: Solo developer, single feature at a time

### Step 2: Set SPEC_PATH

**If Nested** (Option A):
- User provides ticket ID or feature name (e.g., "PROJ-123" or "user-authentication")
- AI sets: `SPEC_PATH = /specs/PROJ-123/`
- All docs go in: `/specs/PROJ-123/00_START_HERE.md`, `/specs/PROJ-123/01_PRD.md`, etc.

**If Flat** (Option B):
- AI sets: `SPEC_PATH = /specs/`
- All docs go in: `/specs/00_START_HERE.md`, `/specs/01_PRD.md`, etc.

### Step 3: Document in 00_START_HERE.md

The AI will document the chosen SPEC_PATH at the top of 00_START_HERE.md:

```markdown
SPEC_PATH: /specs/PROJ-123/
```

### Step 4: All Subsequent Steps Use SPEC_PATH

Every command file now reads SPEC_PATH from 00_START_HERE.md and uses it for all file paths:
- `${SPEC_PATH}01_PRD.md`
- `${SPEC_PATH}02_ARCHITECTURE.md`
- `${SPEC_PATH}BUILD_MAP.md`
- `${SPEC_PATH}LEARNINGS.md`
- etc.

---

## Example: Team Scenario

### Scenario: Three engineers working in parallel

**Engineer 1 - User Authentication Feature**:
```
/specs/AUTH-101/
├── 00_START_HERE.md (SPEC_PATH: /specs/AUTH-101/)
├── 01_PRD.md
├── 02_ARCHITECTURE.md
├── 03_DATA_MODEL.md
├── 09_DECISIONS.md
├── BUILD_MAP.md
├── LEARNINGS.md
└── [all other planning docs]
```

**Engineer 2 - Payment Integration Feature**:
```
/specs/PAY-202/
├── 00_START_HERE.md (SPEC_PATH: /specs/PAY-202/)
├── 01_PRD.md
├── 02_ARCHITECTURE.md
├── 04_API_CONTRACT.md
├── 09_DECISIONS.md
├── BUILD_MAP.md
├── LEARNINGS.md
└── [all other planning docs]
```

**Engineer 3 - Dashboard UI Feature**:
```
/specs/UI-303/
├── 00_START_HERE.md (SPEC_PATH: /specs/UI-303/)
├── 01_PRD.md
├── 05_UI_UX.md
├── 06_DESIGN_SYSTEM.md
├── 09_DECISIONS.md
├── BUILD_MAP.md
├── LEARNINGS.md
└── [all other planning docs]
```

**Result**: No merge conflicts! Each engineer has their own isolated /specs subfolder.

---

## Benefits

### For Teams
✅ **No merge conflicts** - Each ticket/feature has its own folder
✅ **Parallel development** - Multiple engineers can work simultaneously
✅ **Clear ownership** - Easy to see which engineer owns which feature
✅ **Easy code review** - Changes scoped to single ticket folder

### For Solo Developers
✅ **Work on multiple features** - Context switch between features without losing planning docs
✅ **Better organization** - Related docs grouped by feature/ticket
✅ **Historical tracking** - Old feature specs preserved in their own folders

---

## Files Updated

All full-app command files now support SPEC_PATH:
- ✅ full-app-00-start-here-tdd.md (asks for structure choice)
- ✅ full-app-01-prd-tdd.md
- ✅ full-app-02-architecture-tdd.md
- ✅ full-app-03-data-model-tdd.md
- ✅ full-app-04-api-contract-tdd.md
- ✅ full-app-05-ui-ux-tdd.md
- ✅ full-app-06-design-system-tdd.md
- ✅ full-app-07-security-nfr-tdd.md
- ✅ full-app-08-testing-release-tdd.md
- ✅ full-app-09-decisions-tdd.md
- ✅ full-app-10-build-map-tdd.md
- ✅ full-app-11-implement-milestone-n.md
- ✅ full-app-11a-milestone-demo-tdd.md

---

## Migration from Flat to Nested

If you have existing flat structure docs, you can organize them:

**Before** (Flat):
```
/specs/
├── 00_START_HERE.md
├── 01_PRD.md
├── BUILD_MAP.md
└── LEARNINGS.md
```

**After** (Nested):
```
/specs/
└── TICKET-123/
    ├── 00_START_HERE.md
    ├── 01_PRD.md
    ├── BUILD_MAP.md
    └── LEARNINGS.md
```

**How to migrate**:
1. Create ticket folder: `mkdir /specs/TICKET-123`
2. Move files: `mv /specs/*.md /specs/TICKET-123/`
3. Update SPEC_PATH in 00_START_HERE.md: `SPEC_PATH: /specs/TICKET-123/`

---

## Best Practices

### Ticket ID Naming

**Good**:
- `PROJ-123` - Jira/Linear ticket ID
- `user-auth` - Descriptive feature name (kebab-case)
- `bug-login-fix` - Bug fix with context
- `sprint-5-dashboard` - Sprint-based organization

**Avoid**:
- `feature1` - Not descriptive
- `my feature` - Spaces cause issues
- `temp` - Not meaningful
- Just numbers like `123` - No context

### Shared Documentation

Some docs might be repo-wide (not ticket-specific):
- `/docs/CONTRIBUTING.md` - Repo-level
- `/docs/ARCHITECTURE.md` - Overall system architecture
- `/specs/GLOBAL/` - Shared decisions that affect all tickets

### Archiving Completed Work

After a ticket is merged and deployed:
```
/specs/
├── active/
│   ├── AUTH-101/  (in progress)
│   └── PAY-202/   (in progress)
└── archived/
    └── UI-303/    (completed & deployed)
```

---

## Backwards Compatibility

**Existing flat structure still works!**

If you choose Option B (Flat Structure), the AI will use:
- `SPEC_PATH = /specs/`
- All docs in `/specs/*.md` (original behavior)

No breaking changes for existing users.

---

## Implementation Date

**2026-01-01**: Nested /specs folder structure support added to all full-app commands
