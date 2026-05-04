# CLAUDE.md — AI Driven Development Course (Ducker Creative)

## Project Overview

Single-page HTML document: a 10-week intensive course on AI Driven Development — teaching the AI Agent Factory paradigm, production agents, MCP integration, and autonomous system design. No build step — pure HTML/CSS/JS, open directly in a browser or print to PDF.

**File:** `public/index.html`  
**Brand reference:** `ducker-brand-and-patterns.md`

**Course Structure:**
- Weeks 1–2: The AI Agent Factory Paradigm (Foundations)
- Weeks 3–4: General Agents & Communication Mastery (Building)
- Weeks 6–7: Context Engineering & Spec-Driven Development (Production Quality)
- Weeks 8–9: Seven Principles & Team Workflows (Principles & Advanced)
- Week 10 & Outcomes: Integration, Deployment & Career Path

---

## Brand Rules (enforce on every edit)

### Colors — always use CSS variables, never hardcode

| Variable | Hex | Use |
|---|---|---|
| `--dc-orange` | `#ff6b00` | Primary CTAs, highlights, logo accent |
| `--dc-orange2` | `#ff4500` | Gradient end, hover states |
| `--dc-blue` | `#007acc` | Month 1 / foundations, links |
| `--dc-cyan` | `#00ccff` | Month 2 / building, sky accent |
| `--dc-amber` | `#f5a524` | Outcomes, success, gold states |

**Never use:** Tailwind color values (`59,130,246` · `139,92,246` · `34,197,94` · `20,184,166` · `249,115,22`) — these are off-brand. Replace with brand equivalents above.

### Week accent progression
```
Weeks 1–2 → --blue   (#007acc)   --accent-rgb: 0,122,204   (Foundations)
Weeks 3–4 → --purple (#00ccff)   --accent-rgb: 0,204,255   (brand cyan, Building)
Weeks 6–7 → --teal   (#ff6b00)   --accent-rgb: 255,107,0   (brand orange, Production)
Weeks 8–9 → --orange (#ff4500)   --accent-rgb: 255,69,0    (Principles)
Week 10   → --green  (#f5a524)   --accent-rgb: 245,165,36  (brand amber, Outcomes)
```

### Fonts
- **Kumbh Sans** (700/800/900) — all headings: `h1`, `h2`, `h3`, `h4`, `.month-title`, `.week-title`, `.agent-section-title`, `.full-card h3`, `.agent-card h4`
- **Inter** (400–700) — body, UI labels, buttons, tags
- **SF Mono / Monaco / Cascadia Code** — code blocks only

### Button gradient (primary CTA)
```css
background: linear-gradient(135deg, #ff6b00 0%, #ff4500 100%);
/* hover → */ background: linear-gradient(135deg, #007acc 0%, #00ccff 100%);
```

---

## Architecture

The entire page is one self-contained HTML file with inline `<style>` and `<script>`. No dependencies except Google Fonts.

### Tab system
Five tabs: `w1`, `w2`, `w3`, `w4`, `outcomes`. Switched via `switchTab(id, btn)`. Each tab panel has class `tab-panel` + the tab id.

**Tab IDs and content:**
- `w1` — Weeks 1–2 (Foundations): AI Agent Factory Paradigm
- `w2` — Weeks 3–4 (Building): General Agents & Communication
- `w3` — Weeks 6–7 (Production): Context Engineering & Spec-Driven Development
- `w4` — Weeks 8–9 (Principles): Seven Principles & Team Workflows
- `outcomes` — Week 10 & Learning Outcomes

### Month accent system
Each week card carries a class (`m1`, `m2`, `m3`) that sets three CSS custom properties:
- `--accent` — primary accent color
- `--accent-l` — lighter variant
- `--accent-rgb` — raw RGB for use in `rgba()`

Week cards, progress bars, tag styles, borders, and bullet dots all derive color from `var(--accent)` and `var(--accent-rgb)` — do not hardcode colors per-card.

### Accordion (two-level)
Built by `initAccordion()` on page load using the `grid-template-rows: 0fr → 1fr` pattern. Level 1 = week cards. Level 2 = day/step sections inside Week 1. Always use `e.stopPropagation()` on inner click handlers to prevent collapsing the parent.

### Copy-to-clipboard
`copyCode(btn)` — dedents multiline code blocks and writes to clipboard. The `<span class="cmd-comment">` wrapper makes bash comments safe to copy (they're valid bash, ignored by terminal).

### Print / PDF export
`window.print()` triggered by the Download PDF button. Print CSS resets background to white, expands all accordions, hides UI chrome. Test print styles when editing layout.

---

## Dark Theme Palette

```css
--bg:      #080d14   /* navy body background */
--bg2:     #0c1219
--surface: rgba(255,255,255,0.055)
--border:  rgba(255,255,255,0.10)
--text:    #f0f0f5
--text-2:  #a0a0b0
--text-3:  #606075
```

Ambient background glow (body::before): blue radial at top-left, orange radial at bottom-right, 28px dot grid.

---

## Mac Window Chrome

The page renders inside a `.mac-window` with:
- Traffic lights: red `#ff5f57`, yellow `#febc2e`, green `#28c840`
- Title bar with "Download PDF" button (orange gradient)
- Tab nav below the title bar

---

## What NOT to Do

- Do not add a build system, bundler, or package.json — this stays as a single HTML file
- Do not use Tailwind CSS color values (they are not brand-accurate)
- Do not hardcode `rgba(59,130,246,...)` — use `rgba(0,122,204,...)` (brand blue)
- Do not use generic green `#4ade80` — outcomes/success = brand amber `var(--dc-amber)`
- Do not use purple (`#4f46e5`, `#c4b5fd`, `rgba(139,92,246,...)`) — not in brand palette
- Do not add `font-family: Inter` to heading elements — headings use Kumbh Sans
