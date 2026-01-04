UI PATTERN DISCOVERY & DESIGN GUIDANCE

Discover existing UI patterns in brownfield projects or provide definitive UI design specifications with zero ambiguity.

**CRITICAL**: This workflow integrates into any workflow that involves UI elements (web apps, mobile apps, dashboards, admin panels, etc.)

**PHILOSOPHY**:
- **Zero ambiguity** - UI specifications must be concrete and unambiguous
- **Component libraries first** - Strongly favor established component libraries (Material-UI, Ant Design, Chakra UI, shadcn/ui)
- **Production-ready defaults** - Use battle-tested components with accessibility built-in
- **Brownfield consistency** - Match existing patterns exactly when they exist

---

## WHEN TO USE THIS WORKFLOW

Use this workflow when:
- Adding new UI components to existing applications
- Creating new pages/screens in existing projects
- Implementing UI for new features
- Building admin interfaces, dashboards, or portals
- Any task involving user-facing UI elements

**Integrate into**: full-app workflows, feature builders, bug fixes with UI changes

---

## PHASE 1: BROWNFIELD UI PATTERN DISCOVERY

### Step 1A: Automatically Discover Existing UI Files

**FIRST**: Search for existing UI framework and component files

```bash
# React / Next.js / Vue
find . -name "*.jsx" -o -name "*.tsx" -o -name "*.vue" 2>/dev/null | head -20

# Angular
find . -name "*.component.ts" -o -name "*.component.html" 2>/dev/null | head -20

# Svelte
find . -name "*.svelte" 2>/dev/null | head -20

# HTML templates (Django, Flask, Rails)
find . -name "*.html" -o -name "*.erb" -o -name "*.jinja" -o -name "*.hbs" 2>/dev/null | head -20

# UI component libraries
find . -name "package.json" -exec grep -l "react\|vue\|angular\|svelte\|@mui\|antd\|chakra\|tailwind" {} \; 2>/dev/null

# CSS/Styling approach
find . -name "*.css" -o -name "*.scss" -o -name "*.less" -o -name "*.styled.*" 2>/dev/null | head -10

# Design system files
find . -name "*design-system*" -o -name "*theme*" -o -name "*styles*" -o -name "*tokens*" 2>/dev/null

# Storybook (component documentation)
find . -name "*.stories.*" 2>/dev/null | head -10
```

**Report findings**:
```
Found UI framework: <React/Vue/Angular/Svelte/Other>
Component approach: <Functional components / Class components / SFC>
Styling method: <CSS Modules / Styled Components / Tailwind / SCSS / CSS-in-JS>
UI library: <Material-UI / Ant Design / Chakra UI / Bootstrap / Custom>
Design system: <Yes - found at path / No>
Storybook: <Yes - found at path / No>
```

### Step 1B: Analyze Existing Component Patterns

**Read representative component files** to understand patterns:

```bash
# Find common components (Button, Input, Card, Modal)
find . -name "*Button*" -o -name "*button*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3
find . -name "*Input*" -o -name "*input*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3
find . -name "*Card*" -o -name "*card*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3
find . -name "*Modal*" -o -name "*modal*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3

# Find layout components
find . -name "*Layout*" -o -name "*layout*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3

# Find form components
find . -name "*Form*" -o -name "*form*" | grep -E "\.(jsx|tsx|vue|svelte)$" | head -3
```

**For each common component, extract**:
- Component structure and props pattern
- Styling approach (inline, CSS module, styled-component)
- State management pattern (useState, Redux, Context, etc.)
- Accessibility patterns (ARIA attributes, keyboard navigation)
- Error handling patterns
- Validation patterns (for forms)

### Step 1C: Discover Design Tokens / Theme

```bash
# Theme configuration
find . -name "theme.js" -o -name "theme.ts" -o -name "tokens.js" -o -name "variables.scss" 2>/dev/null

# Tailwind config
find . -name "tailwind.config.*" 2>/dev/null

# CSS variables
grep -r ":root\|--color\|--spacing\|--font" . --include="*.css" --include="*.scss" | head -20
```

**Extract design tokens**:
- Colors (primary, secondary, success, error, warning, neutral shades)
- Typography (font families, sizes, weights, line heights)
- Spacing scale (margins, paddings)
- Border radius values
- Shadow definitions
- Breakpoints (mobile, tablet, desktop)

### Step 1D: Document Discovered UI Patterns

Create `/docs/ui-patterns/UI_PATTERN_ANALYSIS.md`:

```markdown
# UI Pattern Analysis

**Project**: <project-name>
**Analysis Date**: <date>
**UI Framework**: <React/Vue/Angular/Svelte/etc>

---

## Framework & Libraries

**Frontend Framework**: <framework and version>
**UI Component Library**: <Material-UI / Ant Design / Custom / None>
**Styling Approach**: <CSS Modules / Styled Components / Tailwind / SCSS>
**State Management**: <Redux / Context API / Zustand / Pinia / NgRx / None>
**Form Library**: <React Hook Form / Formik / None>
**Design System**: <Yes - location / No>

---

## Component Patterns

### Button Component

**Location**: `<path to button component>`

**Pattern**:
```tsx
// Example from codebase
<Button
  variant="primary"
  size="md"
  onClick={handleClick}
  disabled={isLoading}
>
  Click Me
</Button>
```

**Variants found**: primary, secondary, outline, ghost, link
**Sizes found**: sm, md, lg
**States**: default, hover, active, disabled, loading

### Input Component

**Location**: `<path to input component>`

**Pattern**:
```tsx
// Example from codebase
<Input
  label="Email"
  type="email"
  value={email}
  onChange={handleChange}
  error={errors.email}
  required
/>
```

**Props pattern**: label, type, value, onChange, error, disabled, required, placeholder

### Form Pattern

**Location**: `<path to form examples>`

**Pattern**:
```tsx
// Example from codebase
<Form onSubmit={handleSubmit}>
  <Input label="Name" name="name" />
  <Input label="Email" name="email" type="email" />
  <Button type="submit">Submit</Button>
  {errors && <ErrorMessage>{errors.message}</ErrorMessage>}
</Form>
```

**Validation**: <Client-side / Server-side / Both>
**Error display**: <Inline / Toast / Alert banner>

### Layout Pattern

**Location**: `<path to layout components>`

**Pattern**:
```tsx
// Example from codebase
<Layout>
  <Header />
  <Sidebar />
  <MainContent>
    {children}
  </MainContent>
  <Footer />
</Layout>
```

**Grid system**: <12-column / Flexbox / CSS Grid / Tailwind grid>
**Responsive breakpoints**: <breakpoint values>

---

## Design Tokens

### Colors

**Primary**: `<hex value>` - Used for primary actions, links
**Secondary**: `<hex value>` - Used for secondary actions
**Success**: `<hex value>` - Success states, confirmations
**Error**: `<hex value>` - Error states, validation errors
**Warning**: `<hex value>` - Warning states, alerts
**Info**: `<hex value>` - Informational messages

**Neutral shades**:
- Gray-50: `<hex>` (lightest)
- Gray-100: `<hex>`
- Gray-200: `<hex>`
- ...
- Gray-900: `<hex>` (darkest)

### Typography

**Font families**:
- Heading: `<font-family>`
- Body: `<font-family>`
- Monospace: `<font-family>` (for code)

**Font sizes**:
- xs: `<size>` - Small text, labels
- sm: `<size>` - Body small
- base: `<size>` - Default body
- lg: `<size>` - Large text
- xl, 2xl, 3xl, 4xl: `<sizes>` - Headings

**Font weights**:
- Light: 300
- Regular: 400
- Medium: 500
- Semibold: 600
- Bold: 700

### Spacing

**Scale**: `<4px / 8px / tailwind / custom>`

Common values:
- 1: `<value>` - Extra small spacing
- 2: `<value>` - Small spacing
- 3: `<value>` - Medium spacing
- 4: `<value>` - Large spacing
- 6: `<value>` - Extra large spacing
- 8: `<value>` - XXL spacing

### Border & Shadows

**Border radius**:
- sm: `<value>` - Subtle rounding
- md: `<value>` - Default rounding
- lg: `<value>` - Large rounding
- full: `<value>` - Fully rounded (pills, circles)

**Box shadows**:
- sm: `<shadow value>` - Subtle elevation
- md: `<shadow value>` - Default elevation
- lg: `<shadow value>` - High elevation
- xl: `<shadow value>` - Highest elevation

### Breakpoints

- Mobile: `<value>` - Up to 640px
- Tablet: `<value>` - 641px to 1024px
- Desktop: `<value>` - 1025px and above
- Wide: `<value>` - Extra wide screens

---

## Accessibility Patterns

**ARIA attributes**: <consistently used / inconsistent / missing>
**Keyboard navigation**: <fully supported / partial / missing>
**Focus management**: <visible focus states / needs improvement>
**Color contrast**: <WCAG AA compliant / needs review>
**Screen reader support**: <labels present / needs improvement>

---

## State Management Patterns

**Loading states**: <Spinner component / Skeleton screens / Loading text>
**Empty states**: <Custom empty state component / Simple message>
**Error states**: <Error boundary / Toast notifications / Inline errors>

---

## Animation & Transitions

**Transition library**: <Framer Motion / React Spring / CSS transitions / None>
**Common animations**:
- Page transitions: <fade / slide / none>
- Modal animations: <fade in / slide up>
- Button hover: <scale / color change / shadow>

---

## Recommendations

**Strengths**:
- <Identified strong patterns>
- <Consistent design token usage>
- <Good accessibility practices>

**Areas for Improvement**:
- <Inconsistent component APIs>
- <Missing accessibility attributes>
- <Lack of design system documentation>

**For New UI Work**:
- Follow pattern: `<specific pattern to follow>`
- Use components from: `<component directory>`
- Match styling approach: `<CSS Modules / Tailwind / etc>`
- Reference existing: `<specific file to reference>`
```

---

## PHASE 2: DEFINITIVE UI SPECIFICATION (When No Brownfield Patterns Exist)

**CRITICAL**: This phase generates a concrete, unambiguous UI specification. Users will receive:
- Exact component library recommendation with specific version
- Complete design token specification (colors, typography, spacing)
- Specific component implementations with exact props
- Visual mockups showing exactly what the UI will look like
- No open-ended questions - only clarifications needed for requirements

### Step 2A: Determine UI Type and Requirements

**Question 1: What type of UI are you building?**

Present options with definitive examples:

**A) Dashboard / Admin Panel**
```
Example visual:
┌─────────────────────────────────────┐
│ [Logo]  Dashboard    [User Menu ▼] │
├─────────┬───────────────────────────┤
│ Home    │  ┌──────┐  ┌──────┐      │
│ Users   │  │ 1,234│  │  567 │      │
│ Orders  │  │Users │  │Orders│      │
│ Reports │  └──────┘  └──────┘      │
│ Settings│                           │
│         │  Recent Activity          │
│         │  ┌──────────────────────┐ │
│         │  │ Order #123 - $50    │ │
│         │  │ Order #124 - $75    │ │
│         │  └──────────────────────┘ │
└─────────┴───────────────────────────┘
```

**B) Marketing Landing Page**
```
Example visual:
┌─────────────────────────────────────┐
│      [Logo]    Home  Features  CTA │
├─────────────────────────────────────┤
│                                     │
│      Your Product Name              │
│      Tagline goes here              │
│      [Get Started →]                │
│                                     │
│  ┌──────┐  ┌──────┐  ┌──────┐     │
│  │Feature│  │Feature│  │Feature│   │
│  │   1   │  │   2   │  │   3   │   │
│  └──────┘  └──────┘  └──────┘     │
└─────────────────────────────────────┘
```

**C) Form / Data Entry**
```
Example visual:
┌─────────────────────────────────────┐
│  Create New Account                 │
├─────────────────────────────────────┤
│  Full Name:                         │
│  [________________]                 │
│                                     │
│  Email:                             │
│  [________________]                 │
│                                     │
│  Password:                          │
│  [________________]                 │
│                                     │
│  [ ] I agree to terms               │
│                                     │
│  [Cancel]  [Create Account →]      │
└─────────────────────────────────────┘
```

**D) E-commerce / Product Listing**
```
Example visual:
┌─────────────────────────────────────┐
│  [Search_______________] [Cart: 3]  │
├─────────────────────────────────────┤
│  ┌──────┐ ┌──────┐ ┌──────┐        │
│  │ IMG  │ │ IMG  │ │ IMG  │        │
│  │      │ │      │ │      │        │
│  │$49.99│ │$79.99│ │$29.99│        │
│  │[Buy] │ │[Buy] │ │[Buy] │        │
│  └──────┘ └──────┘ └──────┘        │
└─────────────────────────────────────┘
```

**Your choice**: <A/B/C/D>

---

**Question 2: What frontend framework are you using (or prefer)?**

Based on answer, **RECOMMEND SPECIFIC COMPONENT LIBRARY** (not just framework):

**If React**:
  - **RECOMMENDED**: shadcn/ui (Radix UI + Tailwind) - Modern, accessible, customizable
  - Alternative 1: Material-UI (MUI) - Enterprise standard, comprehensive
  - Alternative 2: Ant Design - Enterprise-focused, rich components
  - Alternative 3: Chakra UI - Accessibility-first, great DX

**If Vue**:
  - **RECOMMENDED**: PrimeVue - Comprehensive, enterprise-ready
  - Alternative 1: Vuetify - Material Design standard
  - Alternative 2: Naive UI - Modern, TypeScript-first

**If Angular**:
  - **RECOMMENDED**: Angular Material - Official, well-integrated
  - Alternative 1: PrimeNG - Enterprise components

**If Svelte**:
  - **RECOMMENDED**: Skeleton UI - Modern, Tailwind-based
  - Alternative 1: Carbon Components Svelte

**DEFAULT RECOMMENDATION** (if no preference):
- **React + shadcn/ui + Tailwind CSS**
- Reasoning: Most flexible, modern, accessible by default, widely adopted, easy to customize

**Your choice**: <framework + component library>

---

**Question 3: What's your preferred color scheme?**

Present options visually:

**A) Professional Blue**
```
Primary:   #2563EB (blue-600)
Secondary: #64748B (slate-500)
Success:   #10B981 (green-500)
Error:     #EF4444 (red-500)
Use case: Business apps, SaaS, corporate
```

**B) Modern Purple**
```
Primary:   #8B5CF6 (purple-500)
Secondary: #EC4899 (pink-500)
Success:   #10B981 (green-500)
Error:     #EF4444 (red-500)
Use case: Creative apps, modern startups
```

**C) Trustworthy Green**
```
Primary:   #059669 (emerald-600)
Secondary: #0891B2 (cyan-600)
Success:   #10B981 (green-500)
Error:     #EF4444 (red-500)
Use case: Finance, health, sustainability
```

**D) Bold Orange**
```
Primary:   #EA580C (orange-600)
Secondary: #0EA5E9 (sky-500)
Success:   #10B981 (green-500)
Error:     #EF4444 (red-500)
Use case: E-commerce, food delivery, energetic brands
```

**E) Custom**
- Provide: Primary hex, Secondary hex

**Your choice**: <A/B/C/D/E>

---

**Question 4: What component style do you prefer?**

Present examples:

**A) Sharp / Minimal**
```
┌──────────────┐
│   Button     │  ← Square corners, flat
└──────────────┘

Example: GitHub, Linear
```

**B) Rounded / Friendly**
```
╭──────────────╮
│   Button     │  ← Rounded corners (8px)
╰──────────────╯

Example: Stripe, Notion
```

**C) Soft / Neumorphic**
```
╭──────────────╮
│   Button     │  ← Soft shadows, subtle depth
╰──────────────╯

Example: Dribbble designs, modern apps
```

**D) Bold / High Contrast**
```
┌──────────────┐
│   Button     │  ← Bold borders, high contrast
└──────────────┘

Example: Brutalist designs, bold brands
```

**Your choice**: <A/B/C/D>

---

**Question 5: Form style preference?**

**A) Outlined Inputs**
```
Name:
┌─────────────────┐
│                 │
└─────────────────┘
```

**B) Filled Inputs**
```
Name:
▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
│                 │
▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔
```

**C) Underline Inputs**
```
Name:

─────────────────
```

**Your choice**: <A/B/C>

---

### Step 2B: Generate Definitive UI Specification Document

**CRITICAL**: Generate a complete, unambiguous specification that includes:

1. **Exact Component Library Setup** (dependencies list, installation commands, config instructions)
2. **Complete Design System Specification** (all color/font/spacing values defined)
3. **Component Usage Guide** (which components to use for what, with prop references)
4. **Visual Specifications** (exact pixel values, no "approximately")
5. **Page Layout Specifications** (grid definitions, responsive breakpoints)
6. **No ambiguity** - every decision is made and documented

Create file: `/docs/ui-specification/UI_SPECIFICATION.md`

---

**Specification Template** (React + shadcn/ui + Professional Blue + Rounded):

`/docs/ui-specification/UI_SPECIFICATION.md`:
```markdown
# UI Specification Document

**Project**: <project-name>
**Created**: <date>
**Component Library**: shadcn/ui v0.8.0 + Radix UI + Tailwind CSS v3.4
**Design Language**: Professional, Rounded, Accessible

---

## 1. INSTALLATION & SETUP

### Dependencies Required
- tailwindcss, postcss, autoprefixer
- shadcn/ui (install via npx shadcn-ui@latest init)
- tailwindcss-animate plugin

### Tailwind Configuration
- Enable darkMode with class strategy
- Extend colors to use CSS variables (var(--color-primary-600), etc.)
- Content paths: all .ts/.tsx files in pages, components, app, src

### Components to Install
Install via `npx shadcn-ui@latest add <component>`:
- button, input, card, dialog, form, table, select, toast
- (Add others as needed based on UI type)

---

## 2. COMPLETE DESIGN SYSTEM SPECIFICATION

### Colors (Exact Hex Values)

**Primary Color - Professional Blue**
```
primary-50:  #EFF6FF
primary-100: #DBEAFE
primary-200: #BFDBFE
primary-300: #93C5FD
primary-400: #60A5FA
primary-500: #3B82F6
primary-600: #2563EB  ← PRIMARY (main brand color)
primary-700: #1D4ED8
primary-800: #1E40AF
primary-900: #1E3A8A
```

**Secondary Color - Slate**
```
secondary-50:  #F8FAFC
secondary-100: #F1F5F9
secondary-200: #E2E8F0
secondary-300: #CBD5E1
secondary-400: #94A3B8
secondary-500: #64748B  ← SECONDARY
secondary-600: #475569
secondary-700: #334155
secondary-800: #1E293B
secondary-900: #0F172A
```

**Semantic Colors**
```
success: #10B981  (green-500)  - Success states, confirmations
error:   #EF4444  (red-500)    - Error states, validation errors
warning: #F59E0B  (amber-500)  - Warning states, alerts
info:    #3B82F6  (blue-500)   - Informational messages
```

**Neutral Grays**
```
gray-50:  #F9FAFB  - Lightest background
gray-100: #F3F4F6  - Light background
gray-200: #E5E7EB  - Borders, dividers
gray-300: #D1D5DB  - Disabled states
gray-400: #9CA3AF  - Placeholder text
gray-500: #6B7280  - Secondary text
gray-600: #4B5563  - Body text
gray-700: #374151  - Headings
gray-800: #1F2937  - Dark headings
gray-900: #111827  - Darkest text
```

### Typography

**Font Families**
```css
--font-sans: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
--font-mono: 'JetBrains Mono', 'Fira Code', monospace;
```

**Font Sizes (rem and px)**
```
xs:   0.75rem  (12px)  - Small labels, captions
sm:   0.875rem (14px)  - Secondary text, help text
base: 1rem     (16px)  - Body text (DEFAULT)
lg:   1.125rem (18px)  - Emphasized text
xl:   1.25rem  (20px)  - Small headings
2xl:  1.5rem   (24px)  - Subheadings (h3)
3xl:  1.875rem (30px)  - Section headings (h2)
4xl:  2.25rem  (36px)  - Page headings (h1)
5xl:  3rem     (48px)  - Hero headings
```

**Font Weights**
```
light:    300  - Rarely used
regular:  400  - Body text
medium:   500  - Emphasized text, labels
semibold: 600  - Headings, buttons
bold:     700  - Strong emphasis
```

**Line Heights**
```
tight:   1.25  - Headings
normal:  1.5   - Body text (DEFAULT)
relaxed: 1.75  - Long-form content
```

### Spacing Scale

**Base unit: 4px** (0.25rem)

```
0:  0rem      (0px)    - No spacing
1:  0.25rem   (4px)    - Extra tight
2:  0.5rem    (8px)    - Tight
3:  0.75rem   (12px)   - Compact
4:  1rem      (16px)   - DEFAULT spacing
5:  1.25rem   (20px)   - Comfortable
6:  1.5rem    (24px)   - Relaxed
8:  2rem      (32px)   - Loose
10: 2.5rem    (40px)   - Extra loose
12: 3rem      (48px)   - Section spacing
16: 4rem      (64px)   - Large section spacing
20: 5rem      (80px)   - Hero spacing
```

### Border Radius

```
none: 0px       - Sharp corners
sm:   0.125rem  (2px)   - Subtle rounding
md:   0.375rem  (6px)   - DEFAULT (moderate rounding)
lg:   0.5rem    (8px)   - Rounded (cards, buttons)
xl:   0.75rem   (12px)  - Very rounded
2xl:  1rem      (16px)  - Extra rounded
full: 9999px    - Fully rounded (pills, avatars)
```

### Shadows (Elevation)

```
sm: 0 1px 2px 0 rgb(0 0 0 / 0.05)
    - Subtle elevation (hover states)

md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1)
    - DEFAULT card elevation

lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)
    - Elevated cards, dropdowns

xl: 0 20px 25px -5px rgb(0 0 0 / 0.1), 0 8px 10px -6px rgb(0 0 0 / 0.1)
    - Modals, popovers

2xl: 0 25px 50px -12px rgb(0 0 0 / 0.25)
     - Highest elevation (overlays)
```

### Breakpoints

```
sm:  640px   - Mobile landscape
md:  768px   - Tablet portrait
lg:  1024px  - Tablet landscape / small desktop
xl:  1280px  - Desktop
2xl: 1536px  - Large desktop
```

---

## 3. COMPONENT SPECIFICATIONS

### Button Component
**Import**: `@/components/ui/button`

**Variants Available**:
- `default` (primary): Blue bg (#2563EB), white text, 8px radius, 12px/24px padding
- `secondary`: Light gray bg (#F1F5F9), dark text
- `outline`: Transparent bg, blue 2px border
- `destructive`: Red bg (#EF4444), white text
- `ghost`: Transparent, hover shows light bg
- `link`: Text-only link style

**Sizes**: sm (8px/16px), default (12px/24px), lg (16px/32px), icon (square)

**States**: disabled (0.5 opacity), loading (shows spinner from lucide-react)

**Props Interface**:
- variant, size, disabled, loading, asChild, children, onClick, type

---

### Input Component
**Import**: `@/components/ui/input`, `@/components/ui/label`

**Default Styling**:
- White bg, gray border (#E2E8F0), 6px radius, 8px/12px padding
- Focus: Blue border (#2563EB) with ring
- Error: Red border (#EF4444), error text below in red
- Disabled: Gray bg (#F3F4F6), not-allowed cursor

**Pattern**: Always pair with Label component, show error messages below input

---

### Card Component
**Import**: `@/components/ui/card` (Card, CardHeader, CardTitle, CardDescription, CardContent, CardFooter)

**Styling**: White bg, border (#E2E8F0), 12px radius, md shadow, 24px padding

**Structure**: Header → Title + Description → Content → Footer (optional)

---

### Form Component
**Import**:
- `react-hook-form` (useForm)
- `@hookform/resolvers/zod` (zodResolver)
- `zod` (validation schemas)
- `@/components/ui/form` (Form, FormField, FormItem, FormLabel, FormControl, FormMessage)

**Pattern**:
1. Define zod schema for validation
2. Use useForm with zodResolver
3. Wrap in Form component
4. Use FormField for each input
5. FormMessage shows validation errors automatically

**Implementation**: Developer will implement based on specific form requirements

---

## 4. PAGE TEMPLATES

### Dashboard Page Template

**Layout Structure**:
- Container: max-w-1280px, centered, 24px padding
- Page header: 36px bold heading, 16px subtext, 32px margin-bottom
- Stats grid: 4-col desktop, 2-col tablet, 1-col mobile, 24px gap
- Main content: 3-col grid (2-col main + 1-col sidebar on desktop)

**Components Used**:
- Card for stat display (pb-2 on header, 30px title font)
- Card for main content area (lg:col-span-2)
- Card for sidebar (full-width buttons, 12px vertical spacing)

**Responsive Breakpoints**: md:768px, lg:1024px

**Implementation**: Developer will build based on this structure

---

### Form Page Template

**Layout Structure**:
- Container: max-w-768px, centered, 24px padding
- Page header: Same as dashboard
- Form card: Full width within container
- Field spacing: 24px vertical gap (space-y-6)
- Button group: Right-aligned (justify-end), 16px gap

**Components Used**:
- Card wrapper with CardHeader + CardContent
- Form component with react-hook-form
- Input + Label pairs for each field
- Button group at bottom (outline for cancel, default for submit)

**Implementation**: Developer will build based on requirements

---

## 5. RESPONSIVE BEHAVIOR

**Mobile (<640px)**:
- Single column layouts
- Full-width buttons
- Smaller font sizes (scale down by 10%)
- Reduced padding (16px instead of 24px)
- Stack navigation vertically

**Tablet (640px - 1024px)**:
- 2-column grids where appropriate
- Sidebar can be toggleable
- Standard font sizes
- Standard padding

**Desktop (>1024px)**:
- Multi-column layouts as specified
- Persistent sidebar
- Full navigation visible
- Standard specifications apply

---

## 6. ACCESSIBILITY REQUIREMENTS

**All components MUST**:
- [ ] Have proper ARIA labels
- [ ] Support keyboard navigation (Tab, Enter, Escape, Arrow keys)
- [ ] Have visible focus states (blue ring, 2px offset)
- [ ] Meet WCAG AA color contrast (4.5:1 for normal text, 3:1 for large text)
- [ ] Include alt text for images
- [ ] Have semantic HTML (header, nav, main, footer, article, section)
- [ ] Support screen readers (announce state changes)

**Color Contrast Verification**:
- Primary blue (#2563EB) on white: ✅ 7.2:1 (Pass)
- Gray-600 (#4B5563) on white: ✅ 7.8:1 (Pass)
- Gray-500 (#6B7280) on white: ✅ 4.6:1 (Pass)

---

## 7. IMPLEMENTATION CHECKLIST

**For every new UI component**:
- [ ] Use shadcn/ui component from library (do not build custom if library has it)
- [ ] Apply exact color values from design system
- [ ] Use spacing scale (multiples of 4px)
- [ ] Apply rounded corners (8px default)
- [ ] Add proper TypeScript types
- [ ] Include accessibility attributes
- [ ] Test keyboard navigation
- [ ] Test on mobile, tablet, desktop
- [ ] Verify color contrast
- [ ] Add loading and error states

---

## 8. NO AMBIGUITY - DECISION LOG

This document eliminates ambiguity by making the following decisions:

| Decision Area | Choice Made | Reasoning |
|--------------|-------------|-----------|
| Component Library | shadcn/ui + Radix UI | Modern, accessible, flexible, great DX |
| Styling | Tailwind CSS | Utility-first, consistent, fast iteration |
| Primary Color | #2563EB (blue-600) | Professional, trustworthy, accessible |
| Border Radius | 8px (lg) | Modern rounded aesthetic without being overly playful |
| Font Family | Inter | Clean, professional, excellent readability |
| Spacing Scale | 4px base | Standard, predictable, flexible |
| Form Validation | React Hook Form + Zod | Type-safe, performant, great DX |
| Icon Library | Lucide React | Consistent, well-designed, tree-shakeable |
| State Management | (TBD based on app complexity) | Will be specified when needed |

**EVERY UI element will use these specifications** - no custom components unless absolutely necessary.

---

## 9. THEMING & WHITE-LABEL SUPPORT

**CRITICAL**: All UI must use centralized theme system for brand customization and white-label deployments.

### Theme Architecture

**File Structure**:
```
src/design-system/theme/
├── base-theme.ts          # TypeScript interface for Theme
├── themes/
│   ├── default.ts         # Default brand theme
│   ├── customer-a.ts      # Customer A white-label
│   ├── customer-b.ts      # Customer B white-label
├── ThemeProvider.tsx      # React Context provider
└── useTheme.ts            # Hook for accessing theme
```

### Theme Interface Definition

**base-theme.ts** must define TypeScript interface including:
- `brand`: name, logo paths (primary, secondary, favicon)
- `colors`: primary (50-900 shades), secondary, semantic (success/error/warning/info), gray (50-900)
- `typography`: fontFamily (sans, mono), fontSize (xs-5xl), fontWeight, lineHeight
- `spacing`: 0, 1-20 (based on 4px scale)
- `borderRadius`: none, sm, md, lg, xl, 2xl, full
- `shadows`: sm, md, lg, xl, 2xl
- `breakpoints`: sm, md, lg, xl, 2xl

### Default Theme Values

**themes/default.ts** sets specific values:
- Primary color: #2563EB (blue-600)
- Font: Inter (sans), JetBrains Mono (mono)
- All spacing, shadows, etc. use standard Tailwind values

### White-Label Theme Example

**themes/customer-a.ts** overrides:
- Brand name, logos
- Primary color (e.g., purple #C026D3 instead of blue)
- Optional: Different font family, gray tones

### Theme Provider Implementation

**ThemeProvider.tsx** must:
1. Create React Context for theme
2. Load theme from: env variable, subdomain, database/API, or props
3. Inject CSS variables into document root (`--color-primary-600`, etc.)
4. Update document.title and favicon dynamically
5. Provide `useTheme()` hook for components

### Tailwind Integration

**tailwind.config.js** must use CSS variables:
- Colors reference `var(--color-primary-600)` not hardcoded hex
- Fonts reference `var(--font-sans)` not hardcoded families
- This enables dynamic theme switching

### White-Label Deployment Strategies

**Strategy 1: Environment Variables**
- Set `NEXT_PUBLIC_THEME=customer-a` in .env
- Deploy separate instances per customer subdomain

**Strategy 2: Subdomain Detection**
- Parse `window.location.hostname` to determine theme
- Map subdomain → theme name

**Strategy 3: Database/API**
- Fetch theme from API based on authenticated user's organization
- Load theme data dynamically into Theme interface

### Theme Usage Rules

**MANDATORY**:
1. NEVER hardcode colors - use `bg-primary-600` not `bg-blue-600`
2. NEVER hardcode fonts - use `font-sans` not `font-['Inter']`
3. NEVER hardcode brand names - use `theme.brand.name`
4. NEVER hardcode logos - use `theme.brand.logo.primary`
5. ALL spacing uses theme scale - no arbitrary values
6. ALL border radius uses theme values - no arbitrary values

### White-Label Deployment Checklist

- [ ] Logo files uploaded
- [ ] Brand colors defined with full shade palette
- [ ] Theme file created
- [ ] Env variable or subdomain mapping configured
- [ ] Color contrast verified (WCAG AA)
- [ ] Meta tags updated
- [ ] Email templates using brand theme
- [ ] PDF generation using brand theme

```

---

`/src/design-system/theme.ts` (simplified for non-white-label):
```typescript
export const theme = {
  colors: {
    primary: {
      50: '#EFF6FF',
      500: '#2563EB',
      600: '#1D4ED8',
      700: '#1E40AF',
    },
    gray: {
      50: '#F9FAFB',
      100: '#F3F4F6',
      500: '#6B7280',
      900: '#111827',
    },
    success: '#10B981',
    error: '#EF4444',
    warning: '#F59E0B',
  },
  spacing: {
    xs: '0.25rem',  // 4px
    sm: '0.5rem',   // 8px
    md: '1rem',     // 16px
    lg: '1.5rem',   // 24px
    xl: '2rem',     // 32px
  },
  borderRadius: {
    sm: '0.25rem',  // 4px
    md: '0.5rem',   // 8px
    lg: '0.75rem',  // 12px
    full: '9999px',
  },
  fontSize: {
    xs: '0.75rem',   // 12px
    sm: '0.875rem',  // 14px
    base: '1rem',    // 16px
    lg: '1.125rem',  // 18px
    xl: '1.25rem',   // 20px
    '2xl': '1.5rem', // 24px
  },
};
```

`/src/components/ui/Button.tsx`:
```typescript
import React from 'react';

type ButtonProps = {
  variant?: 'primary' | 'secondary' | 'outline';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
  type?: 'button' | 'submit' | 'reset';
};

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  children,
  onClick,
  disabled = false,
  type = 'button',
}) => {
  const baseClasses = 'rounded-lg font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2';

  const variantClasses = {
    primary: 'bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500',
    secondary: 'bg-gray-200 text-gray-900 hover:bg-gray-300 focus:ring-gray-500',
    outline: 'border-2 border-blue-600 text-blue-600 hover:bg-blue-50 focus:ring-blue-500',
  };

  const sizeClasses = {
    sm: 'px-3 py-1.5 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-6 py-3 text-lg',
  };

  return (
    <button
      type={type}
      onClick={onClick}
      disabled={disabled}
      className={`
        ${baseClasses}
        ${variantClasses[variant]}
        ${sizeClasses[size]}
        ${disabled ? 'opacity-50 cursor-not-allowed' : ''}
      `}
    >
      {children}
    </button>
  );
};
```

`/src/components/ui/Input.tsx`:
```typescript
import React from 'react';

type InputProps = {
  label: string;
  type?: string;
  value: string;
  onChange: (value: string) => void;
  error?: string;
  placeholder?: string;
  required?: boolean;
  disabled?: boolean;
};

export const Input: React.FC<InputProps> = ({
  label,
  type = 'text',
  value,
  onChange,
  error,
  placeholder,
  required = false,
  disabled = false,
}) => {
  return (
    <div className="mb-4">
      <label className="block text-sm font-medium text-gray-700 mb-1">
        {label}
        {required && <span className="text-red-500 ml-1">*</span>}
      </label>
      <input
        type={type}
        value={value}
        onChange={(e) => onChange(e.target.value)}
        placeholder={placeholder}
        required={required}
        disabled={disabled}
        className={`
          w-full px-4 py-2 rounded-lg border transition-colors
          focus:outline-none focus:ring-2 focus:ring-blue-500
          ${error ? 'border-red-500' : 'border-gray-300'}
          ${disabled ? 'bg-gray-100 cursor-not-allowed' : 'bg-white'}
        `}
      />
      {error && (
        <p className="mt-1 text-sm text-red-500">{error}</p>
      )}
    </div>
  );
};
```

---

## INTEGRATION WITH EXISTING WORKFLOWS

### In full-app-XX-xxx.md workflows:

**When UI work is needed**, inject this section:

```markdown
## UI PATTERN CONSISTENCY

**BEFORE implementing UI components**:

1. **Run UI Pattern Discovery**:
   - Check if brownfield project has existing UI patterns
   - If YES: Document patterns and follow them
   - If NO: Use guided UI design workflow

2. **Reference existing or generate new**:
   - Brownfield: Match existing component APIs, styling, tokens
   - Greenfield: Generate starter kit based on user preferences

3. **Document UI decisions** in `/docs/ui-patterns/`
```

### Integration snippet for ANY workflow:

```markdown
### UI Component Implementation

**Step 1: Discover or Define UI Patterns**
- [ ] Run automated UI pattern discovery (brownfield projects)
- [ ] If patterns found: Document and reference in implementation
- [ ] If no patterns: Ask user clarifying questions with visual examples
- [ ] Generate design tokens and base components

**Step 2: Implement Following Patterns**
- [ ] Use discovered component patterns
- [ ] Match color scheme from design tokens
- [ ] Follow accessibility patterns from existing codebase
- [ ] Ensure responsive design matches existing breakpoints

**Step 3: Document New Components**
- [ ] Add component to design system documentation
- [ ] Create Storybook story (if Storybook exists)
- [ ] Update UI pattern analysis document
```

---

## KEY PRINCIPLES

**Component Libraries First** (MANDATORY):
- **ALWAYS use established component libraries** (shadcn/ui, Material-UI, Ant Design, Chakra UI, etc.)
- NEVER build custom UI components if a library provides them
- Component libraries provide accessibility, testing, and production-readiness out of the box
- Only build custom components for unique business logic not covered by libraries
- If choosing between custom solution and library: CHOOSE LIBRARY

**Zero Ambiguity** (MANDATORY):
- Every UI specification must be concrete and definitive
- Provide exact component usage with specific props
- Define all colors, fonts, spacing with precise values
- Include visual examples showing exactly what UI will look like
- NO open-ended "you could do X or Y" - make the decision
- Generate complete, copy-paste ready code

**Proper Theming** (MANDATORY):
- ALL UI must use centralized theme system
- NEVER hardcode colors, fonts, logos, or brand names
- Support white-label deployments from day one
- Use CSS variables for dynamic theme switching
- Theme system must be defined before any UI work begins

**Brownfield First**:
- Always discover existing patterns before creating new ones
- Match existing component APIs exactly
- Use same design tokens and color schemes
- Follow established accessibility patterns
- If brownfield uses a component library, continue using it (don't introduce a new one)

**Clear Communication**:
- Present visual examples when asking questions
- Show ASCII art mockups for layout decisions
- Provide code examples for each option
- Explain trade-offs clearly
- But always end with a definitive recommendation

**Consistency Over Novelty**:
- Prefer extending existing patterns over creating new ones
- Maintain visual consistency across the application
- Reuse existing components before building new ones
- If a component library has it, use it (don't reinvent)

**Accessibility by Default**:
- Component libraries provide accessibility built-in
- Include ARIA attributes in all generated components
- Ensure keyboard navigation works
- Maintain color contrast ratios (WCAG AA minimum)
- Add focus states to interactive elements
- Test with screen readers

---

## DOCUMENTATION

**CRITICAL**: Comprehensive UI documentation must be created once development and testing are complete. UI documentation ensures design consistency, facilitates handoffs, and enables future maintenance.

### UI Design Documentation

**Design System Documentation** (REQUIRED):
- [ ] **Component Library Documentation**: Complete reference for all UI components
  - Component name and import path
  - Available variants (primary, secondary, outline, etc.)
  - Available sizes (sm, md, lg)
  - Props interface with descriptions
  - Visual examples for each variant/size combination
  - States (default, hover, focus, disabled, loading, error)
  - Accessibility features (ARIA attributes, keyboard navigation)
  - Usage guidelines (when to use, when not to use)
  - Code examples for common use cases
  - Store: `/docs/ui/components/` or Storybook
- [ ] **Design Tokens Documentation**: All design system values
  - Color palette with hex values and usage
  - Typography scale (font sizes, weights, line heights)
  - Spacing scale (margin, padding values)
  - Border radius values
  - Shadow values (elevation levels)
  - Breakpoints (responsive design)
  - Animation/transition timings
  - Z-index scale
  - Store: `/docs/ui/design-tokens.md`
- [ ] **Theme Documentation** (if white-label support):
  - Theme architecture explanation
  - How to create a new theme
  - Theme switching implementation
  - CSS variables usage
  - White-label deployment strategies
  - Theme customization limits
  - Store: `/docs/ui/theming.md`
- [ ] **Layout Documentation**: Page layout patterns and templates
  - Grid system explanation
  - Container widths
  - Responsive behavior patterns
  - Common layout compositions
  - Page templates with usage examples
  - Store: `/docs/ui/layouts.md`

**Visual Design Documentation**:
- [ ] **UI Pattern Library**: Reusable UI patterns
  - Forms (login, registration, search, filters)
  - Navigation (top nav, sidebar, breadcrumbs)
  - Data display (tables, cards, lists)
  - Modals and dialogs
  - Notifications (toast, alerts, banners)
  - Empty states
  - Loading states
  - Error states
  - Screenshots or mockups for each pattern
  - Store: `/docs/ui/patterns/` or design tool (Figma, Sketch)
- [ ] **Responsive Design Guidelines**:
  - Mobile-first approach explanation
  - Breakpoint strategy
  - Component behavior at each breakpoint
  - Touch target sizes for mobile
  - Mobile navigation patterns
  - Store: `/docs/ui/responsive-design.md`
- [ ] **Accessibility Guidelines**:
  - WCAG AA compliance requirements
  - Color contrast ratios
  - Keyboard navigation patterns
  - Screen reader considerations
  - Focus state requirements
  - ARIA label usage
  - Accessibility testing checklist
  - Store: `/docs/ui/accessibility.md`

**Component Documentation Tools**:
- [ ] **Storybook** (RECOMMENDED): Interactive component documentation
  - Install and configure Storybook
  - Create stories for all components
  - Add controls for props
  - Document component states
  - Add accessibility addon
  - Deploy to hosting (Chromatic, Netlify, Vercel)
  - Store: `/src/components/**/*.stories.tsx`
- [ ] **StyleGuidist** (alternative): Living style guide generator
- [ ] **Docusaurus** (alternative): Documentation website generator

### Engineering UI Documentation

**Component Code Documentation**:
- [ ] **TypeScript Interface Documentation**: All component props documented
  - JSDoc comments for each prop
  - Prop types and default values
  - Required vs. optional props
  - Prop validation rules
  - Examples in comments
- [ ] **Component README Files**: Usage guide per component directory
  - Component purpose
  - Installation (if standalone)
  - Basic usage example
  - Advanced usage examples
  - Props reference
  - Styling customization
  - Store: `/src/components/[ComponentName]/README.md`
- [ ] **CSS/Styling Documentation**: Styling approach explanation
  - CSS-in-JS approach (if used)
  - Tailwind utility class conventions
  - CSS Modules usage (if used)
  - Global styles location
  - Custom utility classes
  - Theme variable usage
  - Store: `/docs/ui/styling.md`

**UI State Management Documentation** (if applicable):
- [ ] **State Management Guide**: How UI state is managed
  - State management library (Redux, Zustand, Context API)
  - Global state structure
  - Local component state patterns
  - State update patterns
  - Store: `/docs/ui/state-management.md`
- [ ] **Form Handling Documentation**: Forms and validation approach
  - Form library (React Hook Form, Formik)
  - Validation library (Zod, Yup)
  - Form submission patterns
  - Error handling patterns
  - Store: `/docs/ui/forms.md`

### Technical Implementation Documentation

**Animation & Interaction Implementation**:
- [ ] **Animation Implementation Guide**: Technical specs for animations
  - Transition timing values (duration in ms, easing functions)
  - CSS/JS animation implementation examples
  - Reduced motion media query implementation
  - Performance considerations
  - Store: `/docs/ui/animations.md`
- [ ] **Keyboard Shortcuts Documentation**: Implemented keyboard shortcuts
  - Complete list of keyboard shortcuts
  - Key combinations and their actions
  - Implementation code references
  - Store: `/docs/ui/keyboard-shortcuts.md`

### Operational UI Documentation

**Development Guidelines**:
- [ ] **UI Development Workflow**: How to add new UI components
  - Component creation checklist
  - File structure conventions
  - Naming conventions
  - Testing requirements (unit, integration, visual)
  - Code review checklist
  - Store: `/docs/ui/development-workflow.md`
- [ ] **Component Testing Guide**: How to test components
  - Unit testing (Jest, Vitest)
  - Component testing (React Testing Library)
  - Visual regression testing (Chromatic, Percy)
  - Accessibility testing (axe, jest-axe)
  - Store: `/docs/ui/testing.md`
- [ ] **Performance Guidelines**: UI performance best practices
  - Bundle size optimization
  - Code splitting strategies
  - Image optimization
  - Lazy loading patterns
  - Performance budgets
  - Store: `/docs/ui/performance.md`

**Browser Compatibility Documentation**:
- [ ] **Supported Browsers**: Browser support matrix
  - Supported browser versions (Chrome, Firefox, Safari, Edge)
  - Mobile browser support (iOS Safari, Chrome Android)
  - Known issues per browser
  - Polyfills required
  - Store: `/docs/ui/browser-support.md`

### User-Facing UI Documentation

**End-User Help Documentation** (if applicable):
- [ ] **Feature Guides**: How to use UI features
  - Step-by-step guides with screenshots
  - Common tasks tutorials
  - FAQ
  - Store: `/docs/help/` or external help center
- [ ] **UI Tour/Onboarding**: First-time user experience
  - Product tour flow
  - Tooltips and hints
  - Empty state guidance
  - Store: Implementation in app + `/docs/onboarding/`

### UI Documentation Standards

**UI Documentation Quality Requirements**:
- [ ] All UI documentation in Markdown or Storybook
- [ ] Component screenshots/mockups up-to-date
- [ ] Code examples tested and working
- [ ] Design tokens match implementation
- [ ] Accessibility requirements clearly stated
- [ ] Responsive behavior documented
- [ ] Browser compatibility clearly stated
- [ ] Version numbers for component library
- [ ] Links between design and code documentation

**UI Documentation Review**:
- [ ] Designer review for design accuracy
- [ ] Developer review for code accuracy
- [ ] Accessibility review for WCAG compliance
- [ ] Product review for user-facing documentation
- [ ] Quarterly design system audit
- [ ] Regular design token sync between design and code

### UI Documentation Checklist

**Before Production UI Deployment** (MANDATORY):
- [ ] Design system documentation complete
- [ ] Component library documented (Storybook or equivalent)
- [ ] Design tokens documented and synced with implementation
- [ ] Theme documentation complete (if white-label)
- [ ] UI pattern library created
- [ ] Responsive design guidelines documented
- [ ] Accessibility guidelines documented and verified
- [ ] TypeScript interfaces documented with JSDoc
- [ ] Component README files created for complex components
- [ ] CSS/styling approach documented
- [ ] State management patterns documented (if applicable)
- [ ] Form handling patterns documented
- [ ] UI development workflow documented
- [ ] Component testing guide created
- [ ] Performance guidelines documented
- [ ] Browser support matrix published
- [ ] End-user help documentation created (if applicable)
- [ ] All documentation reviewed and approved
- [ ] Design handoff complete (design → development)
- [ ] Knowledge transfer session conducted with team

**UI Documentation Storage**:
- **Design System**: `/docs/ui/` in repository or Storybook deployment
- **Component Docs**: Storybook or `/docs/ui/components/`
- **UX Docs**: `/docs/ux/` or design tool (Figma, Sketch)
- **User Help**: `/docs/help/` or external help center platform
- **Design Files**: Figma, Sketch, or design tool with version control

**UI Documentation Tools**:
- Component docs: Storybook, StyleGuidist, Docusaurus
- Design tools: Figma, Sketch, Adobe XD
- Prototyping: Figma, InVision, Principle
- Documentation sites: Docusaurus, GitBook, MkDocs
- Screenshot tools: Cleanshot, Snagit, browser DevTools

---

Generated using ui-pattern-discovery.md
Last updated: <date>
