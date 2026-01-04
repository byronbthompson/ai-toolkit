Read ${SPEC_PATH} from 00_START_HERE.md (look for "SPEC_PATH:" at the top).

Open ${SPEC_PATH}05_UI_UX.md and draft:

- Screen inventory
- Route map
- Navigation flows
- Component responsibilities
- Empty/loading/error states
- Accessibility Requirements (MANDATORY, not optional):
  BASELINE REQUIREMENTS (all projects must meet):
  - Semantic HTML:
    - Proper heading hierarchy (h1 → h2 → h3, no skipping levels)
    - Landmarks (header, nav, main, aside, footer)
    - Lists for list content (ul, ol, not div soup)
    - Buttons for actions, links for navigation
  - Keyboard Navigation:
    - All interactive elements accessible via Tab key
    - Logical tab order (follows visual flow)
    - Skip links for main content (skip navigation)
    - Escape key closes modals/dropdowns
    - Arrow keys for custom components (if applicable)
  - Visual Accessibility:
    - Color contrast ratios (WCAG AA minimum):
      - Normal text: 4.5:1 contrast ratio
      - Large text (18pt+): 3:1 contrast ratio
      - Interactive elements: 3:1 for boundaries/states
    - Focus indicators visible and clear (don't remove :focus outlines)
    - Don't rely on color alone (use icons, text, patterns)
    - Text resizable to 200% without breaking layout
  - Content Accessibility:
    - Alt text for all images (or empty alt="" for decorative)
    - aria-label for icon-only buttons
    - Form labels properly associated with inputs (for/id or aria-labelledby)
    - Error messages announced to screen readers (aria-live, role="alert")
    - Required fields clearly marked (* plus aria-required)
  - Internationalization Support (if required):
    - Text externalized to translation files (not hardcoded)
    - Support for RTL languages (Arabic, Hebrew) if global audience
    - Date/time formatting locale-aware
    - Number formatting locale-aware (currency, decimals)
    - Character encoding UTF-8 throughout
    - Language selection mechanism (if multi-language)
    - Translation file structure: [e.g., /locales/en.json, /locales/es.json]
    - Default language: [e.g., English]
    - Supported languages: [list if known, or "TBD based on user base"]
  TESTING CHECKLIST (must complete before milestone approval):
  - [ ] Tab through entire app (can reach all interactive elements)
  - [ ] Screen reader test (NVDA on Windows / VoiceOver on Mac):
    - Can complete primary user flow using only screen reader
    - All interactive elements properly announced
    - Form validation errors are announced
  - [ ] Color contrast check:
    - Use browser DevTools (Lighthouse) or online tool (WebAIM)
    - All text meets WCAG AA standards (4.5:1 or 3:1 for large)
  - [ ] Zoom to 200% (browser zoom):
    - Layout doesn't break or cause horizontal scroll
    - All content remains readable and functional
  - [ ] Keyboard-only navigation (no mouse):
    - Can complete all critical tasks using only keyboard
    - Focus indicator always visible
  TOOLING AND AUTOMATION:
  - Automated testing: [axe, Lighthouse, pa11y - choose based on stack]
  - Run accessibility tests in CI/CD pipeline
  - Target WCAG level: [A (baseline), AA (recommended), AAA (advanced)]
  - Document known limitations: [if any features can't meet standards, explain why]
  RATIONALE:
  - Accessibility is not a feature, it's a requirement
  - Fixing accessibility later is 10x harder than building it in
  - Many developers want to build accessible apps but forget
  - Legal requirements in many jurisdictions (ADA, Section 508, EN 301 549)
  - Better UX for everyone (keyboard shortcuts, clear focus, good contrast)

Review OFFICIAL UI framework guidance + accessibility standards (briefly).
Present 2–3 UX approaches (minimal screens vs guided flow vs power-user) and recommend one.
Record decisions in ${SPEC_PATH}09_DECISIONS.md.
Ask ONE blocking question.
No code changes.

---
**NEXT STEP**: When 05_UI_UX.md is complete and approved, run `full-app-06-design-system-tdd.md` to define the design system.
