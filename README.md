# design-research

You found a website whose design you love and want to build something that looks and feels like it. Before you start coding, you need to understand exactly how it's built — the colors, typography, spacing, components, layout patterns, responsive behavior, and all the small details that make it feel right.

This prompt for [Claude Code](https://code.claude.com) automates that process using [agent teams](https://code.claude.com/docs/en/agent-teams) (setup instructions below). Give it a URL and it dispatches 10 specialized browser-automation agents to document every aspect of the site's design — exact hex colors, font stacks, component inventories, page layout measurements, content templates, UX patterns, responsive breakpoints, and more. You get back a single structured markdown reference document with all the specifics you need to faithfully recreate the design.

![Agent team running a design research audit in split-pane mode](agent-team-screenshot.jpg)

## Prerequisites

### 1. Claude Code

Install Claude Code (version 2.0.73+):

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. Claude in Chrome extension

The [Claude in Chrome](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn) extension lets Claude drive your actual browser — the same one where you're already logged in. This matters because many sites you'd want to research are behind authentication (course platforms, SaaS dashboards, gated content). Rather than dealing with API tokens or headless browser auth flows, Claude just opens a new tab in your Chrome session and inherits all your cookies and login state. Each research agent navigates, screenshots, reads the DOM, extracts computed styles, and interacts with the page as if you were doing it yourself. See the [setup guide](https://code.claude.com/docs/en/chrome) for installation and troubleshooting.

### 3. tmux

You need tmux for [agent teams](https://code.claude.com/docs/en/agent-teams) to work properly. Without it, all agents run in-process in a single terminal and you can't see what they're doing. With tmux, each agent gets its own split pane so you can watch them work in parallel.

```bash
# macOS
brew install tmux

# Ubuntu/Debian
sudo apt install tmux
```

On macOS with iTerm2, use `tmux -CC` for native iTerm2 tab/split integration instead of tmux's built-in UI.

### 4. Enable agent teams

Agent teams are experimental and disabled by default. Enable them by adding this to your `~/.claude/settings.json`:

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

Or set the environment variable directly:

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

## Usage

### Starting Claude Code

Start a tmux session, then launch Claude with Chrome and permissions flags:

```bash
# iTerm2 on macOS (recommended)
tmux -CC

# Then inside tmux:
claude --chrome --dangerously-skip-permissions
```

`--chrome` connects to the Chrome extension. `--dangerously-skip-permissions` is needed so the 10 browser agents don't each prompt you for approval on every action. See the [agent teams docs](https://code.claude.com/docs/en/agent-teams) for more on how this works.

### Running the audit

Just point Claude at the prompt file in this repo and tell it what site to research:

```
Read https://raw.githubusercontent.com/hamelsmu/design-research/main/skills/design-research/SKILL.md and follow those instructions for https://example.com. Write findings to notes/example-research.md
```

Or if you've cloned the repo locally:

```
Read /path/to/design-research/skills/design-research/SKILL.md and follow those instructions for https://example.com. Write findings to notes/example-research.md
```

### What happens

1. Claude creates an agent team
2. **First pass** — 5 agents run in parallel (no dependencies):
   - Site Structure & Navigation
   - Design System (Colors & Typography)
   - Visual Design & Personality
   - Technical Implementation
   - Accessibility & Semantics
3. **Second pass** — 4 agents run in parallel (after site structure is mapped):
   - Page Layouts & Chrome
   - Interactive Components Inventory
   - Content Type Templates
   - UX Patterns & Micro-interactions
4. **Third pass** — 1 agent (after page layouts are documented):
   - Mobile & Responsive Behavior
5. The lead synthesizes all findings into a single structured markdown report

### Output

The output is a comprehensive markdown document covering all 10 research areas with:
- Exact color values (hex, RGB, Tailwind equivalents)
- Typography scales with font families, sizes, weights
- Complete component inventories
- Page layout diagrams with CSS measurements
- Content flow documentation per page type
- Responsive breakpoint behavior
- Technical architecture summary
- Accessibility grade and recommendations

## Tips

- **Chrome must be open** with the extension active before starting Claude
- **You must be logged in** to the target site if it requires authentication — agents share your browser's login state
- **Expect ~10-15 minutes** for a full audit depending on site complexity
- **Token usage is significant** — each agent has its own context window. A full 10-agent audit uses roughly 10x the tokens of a single session
- If a connection drops, run `/chrome` inside Claude to reconnect
- The prompt works best on sites with multiple distinct page types

## How it works

Uses Claude Code [agent teams](https://code.claude.com/docs/en/agent-teams) to spawn 10 browser-automation agents across three passes. Independent tasks (colors, accessibility, tech stack) run immediately; dependent tasks (page layouts, components) wait for the site structure agent to finish first. Each agent opens its own Chrome tab, does its analysis, and reports back. The lead synthesizes everything into one document.

## Installing as a skill

If you use this frequently, you can install it as a [Claude Code skill](https://code.claude.com/docs/en/skills) for `/design-research URL` shorthand:

```bash
mkdir -p ~/.claude/skills
cp -r skills/design-research ~/.claude/skills/
```

## License

MIT
