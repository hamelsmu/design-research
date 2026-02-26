---
name: design-research
description: Conduct comprehensive design research on any website or web application using a team of browser-automation agents. Use when asked to analyze, audit, or document a website's design system, UX patterns, layout, typography, colors, components, accessibility, responsive behavior, and technical implementation.
---

# Website Design Research

You are orchestrating a comprehensive design research audit of a website. You will create an **agent team** with parallel browser-automation agents to investigate every aspect of the site's design and implementation.

## Input

The user provides a URL to research: `$ARGUMENTS`

If no URL was provided, ask the user for the target URL before proceeding.

## Critical Requirements

1. **You MUST use agent teams** (TeamCreate, TaskCreate, TaskUpdate, SendMessage, etc.) — NOT loose subagents via the Task tool
2. **Every teammate MUST use `mcp__claude-in-chrome__*` browser automation tools** — never WebFetch, curl, or any other method
3. **Write all findings to a single output file** specified by the user (or default to `notes/design-research.md`)

## Browser Tool Startup Sequence (for every teammate)

Every agent must follow this sequence when starting:

1. `mcp__claude-in-chrome__tabs_context_mcp` — get browser context
2. `mcp__claude-in-chrome__tabs_create_mcp` — create a new tab
3. `mcp__claude-in-chrome__navigate` — go to the target URL
4. `mcp__claude-in-chrome__computer` (action=screenshot) — capture visual state
5. `mcp__claude-in-chrome__read_page` — inspect DOM/accessibility tree
6. `mcp__claude-in-chrome__javascript_tool` — extract computed styles, class names, data
7. `mcp__claude-in-chrome__find` — locate specific elements by description
8. `mcp__claude-in-chrome__computer` (action=scroll) — see content below the fold

### Key tools by purpose

| Purpose | Tool |
|---|---|
| Navigate to URLs | `mcp__claude-in-chrome__navigate` |
| Take screenshots | `mcp__claude-in-chrome__computer` (action=screenshot) |
| Read DOM / accessibility tree | `mcp__claude-in-chrome__read_page` |
| Run JavaScript (computed styles, data extraction) | `mcp__claude-in-chrome__javascript_tool` |
| Click elements | `mcp__claude-in-chrome__computer` (action=left_click) |
| Find elements by description | `mcp__claude-in-chrome__find` |
| Scroll the page | `mcp__claude-in-chrome__computer` (action=scroll) |
| Resize viewport (responsive testing) | `mcp__claude-in-chrome__resize_window` |
| Read page text content | `mcp__claude-in-chrome__get_page_text` |
| Read console logs | `mcp__claude-in-chrome__read_console_messages` |
| Read network requests | `mcp__claude-in-chrome__read_network_requests` |

## Research Areas (10 Tasks)

### Task 1: Site Structure & Navigation
- Map all top-level URLs and page types (home, product, content, settings, etc.)
- Document the navigation hierarchy (breadcrumbs, sidebar, tabs, footer links)
- Identify navigation redundancy patterns (how many ways to get to the same content)
- Note URL patterns and routing conventions
- Identify navigation gaps (missing back-links, dead ends, no search, etc.)

### Task 2: Page Layout & Chrome (per page type)
- For each distinct page type, document the shared "chrome" (header, sidebar, footer)
- Measure column layout (widths, sticky behavior, responsive breakpoints)
- Document the top bar elements (icons, toggles, user menu)
- Note which chrome elements appear/disappear per page type (build a Page Chrome Matrix)
- Record CSS classes and measurements for key layout containers

### Task 3: Design System — Colors & Typography
- Extract exact color values (RGB/hex) for backgrounds, text, links, borders, accents
- Document the font stack (families, sizes, weights, line-heights per element)
- Capture syntax highlighting color scheme if code is present
- Note the color hierarchy and how it creates depth/layering
- Document the button system (variants, sizes, states)
- Identify whether CSS custom properties or utility classes are used

### Task 4: Interactive Components Inventory
- Catalog every interactive component: forms, modals, accordions, toggles, collapsibles, carousels, players, search, etc.
- Document each component's states (collapsed/expanded, active/inactive, loading, error, disabled)
- Note toolbar buttons and their functions
- Identify the underlying libraries (if visible in DOM/network)

### Task 5: Content Type Templates (per type)
- For each distinct content/page type, document:
  - The exact content flow (e.g., H1 → hero → body → sidebar → CTA → footer)
  - Unique elements per type
  - Variants within the type
- Build a Content Template Matrix showing which features appear on which page types

### Task 6: UX Patterns & Micro-interactions
- Progressive disclosure patterns (what's hidden by default, how it reveals)
- State persistence (localStorage, cookies, URL params, session tracking)
- Gamification elements (progress bars, badges, streaks, points, rewards)
- Callout/info box styles and when each is used (info vs warning vs success vs error)
- Hover effects, transitions, and animations
- Loading states and skeleton screens

### Task 7: Visual Design & Personality (qualitative)
- Illustrations, icons, and visual metaphors
- Whimsical or distinctive details (dividers, badges, mascots, copy tone)
- Typography warmth and font pairing analysis
- Overall emotional tone and brand personality
- Comparison to common design paradigms (minimal, editorial, playful, corporate, etc.)
- What makes this design memorable or distinctive

### Task 8: Mobile & Responsive Behavior
- Resize to mobile breakpoint and document what changes
- Sidebar → drawer conversions
- Touch targets and hamburger menu behavior
- Features that are desktop-only
- Critical mobile issues (missing navigation, undersized targets, viewport bugs)
- Document all breakpoints used

### Task 9: Technical Implementation
- Identify the framework (React, Vue, Next.js, Nuxt, Svelte, etc.)
- Identify CSS approach (Tailwind, styled-components, CSS modules, vanilla, etc.)
- Key component/class naming conventions visible in DOM
- Libraries and services (video hosting, analytics, auth providers, CDNs, etc.)
- API endpoints and data fetching patterns visible in network tab
- SEO implementation (meta tags, OG tags, structured data, canonical URLs)

### Task 10: Accessibility & Semantics
- Heading hierarchy (proper H1 → H2 → H3 nesting)
- ARIA attributes on interactive elements
- Semantic HTML usage (main, nav, header, footer, aside, section, article)
- Keyboard navigability of key components
- Skip-to-content links
- Color contrast adequacy (check muted text on light backgrounds especially)
- Screen reader friendliness (labeled inputs, alt text, etc.)
- Assign an overall accessibility grade (A through F)

## Execution Strategy

### Dependencies

| Task | Dependencies | Notes |
|---|---|---|
| 1. Site Structure | None | Map the site first |
| 2. Page Layouts | Task 1 | Needs page types identified |
| 3. Design System | None | Can run immediately |
| 4. Components | Task 1 | Needs site map to find all component types |
| 5. Content Templates | Task 1 | Needs page types identified |
| 6. UX Patterns | Task 1 | Needs site map for thorough coverage |
| 7. Visual Design | None | Can run immediately |
| 8. Mobile/Responsive | Task 2 | Needs layout baseline for comparison |
| 9. Technical Impl. | None | Can run immediately |
| 10. Accessibility | None | Can run immediately |

### Three-Pass Execution

**First pass (5 agents, parallel, no dependencies):**
Tasks 1, 3, 7, 9, 10

**Second pass (4 agents, parallel, after Task 1 completes):**
Tasks 2, 4, 5, 6

**Third pass (1 agent, after Task 2 completes):**
Task 8

## Team Orchestration Procedure

Follow these steps exactly:

1. **Create the team**: `TeamCreate` with name like `design-research-{site}`
2. **Create all 10 tasks**: Use `TaskCreate` for each research area
3. **Set dependencies**: Use `TaskUpdate` with `addBlockedBy` to wire up the dependency chain
4. **Spawn first-pass agents** (5 agents for Tasks 1, 3, 7, 9, 10):
   - Each agent prompt must include:
     - The target URL
     - The specific research task description (copy from above)
     - The browser tool startup sequence
     - Instruction to report findings back via SendMessage
   - Use `team_name` parameter on the Task tool to attach agents to the team
5. **As agents complete**:
   - Mark their tasks as completed via `TaskUpdate`
   - Send shutdown requests via `SendMessage`
   - Approve shutdown responses
6. **When Task 1 completes**: spawn second-pass agents (Tasks 2, 4, 5, 6), passing the site map from Task 1's findings
7. **When Task 2 completes**: spawn third-pass agent (Task 8), passing the layout findings
8. **After all agents complete**: compile all findings into the output file
9. **Clean up**: `TeamDelete` to remove team resources

## Output Format

Compile all findings into a single markdown file with this structure:

```markdown
# {Site Name} — Design & UX Research Report

> **URL**: {target URL}
> **Date**: {today's date}
> **Summary**: One-line description of the site's design approach

---

## Table of Contents
[Auto-generated from sections below]

## 1. Site Structure & Navigation
[Findings from Task 1]

## 2. Page Layouts & Chrome
[Findings from Task 2]

## 3. Design System — Colors & Typography
[Findings from Task 3 — include color tables with hex values and Tailwind equivalents]

## 4. Interactive Components
[Findings from Task 4]

## 5. Content Type Templates
[Findings from Task 5]

## 6. UX Patterns & Micro-interactions
[Findings from Task 6]

## 7. Visual Design & Personality
[Findings from Task 7]

## 8. Mobile & Responsive Behavior
[Findings from Task 8]

## 9. Technical Implementation
[Findings from Task 9 — include architecture diagram]

## 10. Accessibility & Semantics
[Findings from Task 10 — include grade and top recommendations]
```

Use tables, code blocks, and structured formatting throughout. Include exact CSS values, class names, hex colors, and measurements — not vague descriptions.
