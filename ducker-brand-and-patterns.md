# Ducker Creative — Brand & UI Patterns Reference
> Created 2026-04-30 from duckercreative.com + ai-roadmap.html build session

---

## Brand Colors

| Variable | Hex | Usage |
|---|---|---|
| `--dc-orange` | `#ff6b00` | Primary CTA, logo "D" accent, heading highlights |
| `--dc-orange2` | `#ff4500` | Secondary / gradient end (CTAs on hover) |
| `--dc-blue` | `#007acc` | Primary brand blue (hero tint, links) |
| `--dc-cyan` | `#00ccff` | Sky/hero background, secondary accent |
| `--dc-amber` | `#f5a524` | Warm gold, outcomes, success states |
| Black | `#000000` | Body text, logo wordmark |
| Dark gray | `#222222` | Secondary body text |
| Light bg | `#fff3e6` | Warm section backgrounds (orange tint) |
| White | `#ffffff` | Primary background |

### Button Gradients (exact from site CSS)
```css
/* Primary CTA */
background: linear-gradient(135deg, #ff6b00 0%, #ff4500 100%);

/* CTA Hover */
background: linear-gradient(135deg, #007acc 0%, #00ccff 100%);
```

---

## Typography

| Role | Font | Weight |
|---|---|---|
| Headings / Display | **Kumbh Sans** | 700, 800, 900 |
| Body / UI | **Inter** | 400, 500, 600, 700 |
| Code / Terminal | SF Mono, Monaco, Cascadia Code | 400 |

### Google Fonts import
```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Kumbh+Sans:wght@400;600;700;800;900&display=swap" rel="stylesheet" />
```

---

## Dark Theme Palette (for tools / internal docs)

Mapped from brand colors to a Mac-style dark UI:

```css
:root {
  --bg:          #080d14;    /* dark navy, brand blue tint */
  --bg2:         #0c1219;

  /* Ducker Brand */
  --dc-orange:      #ff6b00;
  --dc-orange2:     #ff4500;
  --dc-blue:        #007acc;
  --dc-cyan:        #00ccff;
  --dc-amber:       #f5a524;

  /* Accent system for multi-section docs */
  --blue:        #007acc;    /* Month 1 / Foundations */
  --blue-light:  #33b5e5;
  --purple:      #00ccff;    /* Month 2 / Building (cyan replaces purple) */
  --purple-light:#4dd9ff;
  --teal:        #ff6b00;    /* Month 3 / Advanced (orange replaces teal) */
  --teal-light:  #ff8c38;
  --orange:      #ff4500;    /* Section 4 / Tools */
  --orange-light:#ff7048;
  --green:       #f5a524;    /* Section 5 / Outcomes (amber replaces green) */
  --green-light: #fbbf24;
}
```

### Ambient background glow (body::before)
```css
radial-gradient(circle at 25% 20%, rgba(0,122,204,0.14) 0%, transparent 50%),
radial-gradient(circle at 75% 80%, rgba(255,107,0,0.10) 0%, transparent 50%),
radial-gradient(rgba(255,255,255,0.035) 1px, transparent 1px);
background-size: 100% 100%, 100% 100%, 28px 28px;
```

---

## Reusable UI Patterns (from ai-roadmap.html)

### 1. Mac-style Window Chrome
```css
.mac-window {
  background: rgba(20,20,32,0.85);
  backdrop-filter: blur(40px) saturate(180%);
  border: 1px solid rgba(255,255,255,0.10);
  border-radius: 18px;
  box-shadow: 0 8px 40px rgba(0,0,0,0.5), 0 0 0 1px rgba(255,255,255,0.04) inset;
  overflow: hidden;
}
/* Traffic lights: red #ff5f57, yellow #febc2e, green #28c840 */
```

### 2. Two-Level Accordion (Week card → Day section)
**CSS — smooth grid animation (no max-height guessing):**
```css
.week-body {
  display: grid;
  grid-template-rows: 0fr;
  transition: grid-template-rows 0.38s cubic-bezier(0.4, 0, 0.2, 1);
}
.week-body.open { grid-template-rows: 1fr; }
.week-body-inner { overflow: hidden; padding-top: 18px; }
```

**JS — DOM restructure on load (works on any existing HTML):**
```js
function mkChevron(cls) {
  const ns = 'http://www.w3.org/2000/svg';
  const svg = document.createElementNS(ns, 'svg');
  svg.setAttribute('class', cls);
  svg.setAttribute('viewBox', '0 0 24 24');
  svg.setAttribute('fill', 'none');
  svg.setAttribute('stroke', 'currentColor');
  svg.setAttribute('stroke-width', '2.5');
  const p = document.createElementNS(ns, 'polyline');
  p.setAttribute('points', '6 9 12 15 18 9');
  svg.appendChild(p);
  return svg;
}

function initAccordion() {
  document.querySelectorAll('.week-card').forEach(card => {
    const hdr = document.createElement('div');
    hdr.className = 'week-card-header';
    // ... wrap existing header content + append chevron
    // ... wrap remaining children in .week-body > .week-body-inner
    hdr.addEventListener('click', () => {
      const open = body.classList.toggle('open');
      card.classList.toggle('expanded', open);
    });
  });
}
initAccordion();
```

**Key rule:** `stopPropagation()` on nested level-2 click handlers so inner clicks don't collapse the parent.

### 3. Copy-to-Clipboard Code Blocks
```html
<div class="code-block">
  <span class="code-line">npm install -g @anthropic-ai/claude-code</span>
  <button class="copy-btn" onclick="copyCode(this)">Copy</button>
</div>
```

```js
function copyCode(btn) {
  const raw = btn.closest('.code-block').querySelector('.code-line').innerText;
  // Dedent: strip common leading whitespace (handles multiline)
  const lines = raw.split('\n');
  const minIndent = lines
    .filter(l => l.trim().length > 0)
    .reduce((min, l) => Math.min(min, l.match(/^(\s*)/)[1].length), Infinity);
  const cleaned = lines.map(l => l.slice(minIndent)).join('\n').trim();
  navigator.clipboard.writeText(cleaned).then(() => {
    btn.textContent = 'Copied!';
    btn.classList.add('copied');
    setTimeout(() => { btn.textContent = 'Copy'; btn.classList.remove('copied'); }, 2000);
  });
}
```

**For multiline blocks with comments:**
```html
<span class="code-line"><span class="cmd-comment"># install</span>
brew install node</span>
```
`# comments` are valid bash — they copy cleanly and are ignored by the terminal.

### 4. Print / PDF CSS for Dark UIs
```css
@media print {
  body { background: #fff !important; color: #000 !important; }
  body::before { display: none; }
  .mac-window { box-shadow: none !important; background: #fff !important; backdrop-filter: none !important; }
  .title-bar, .tab-nav { display: none !important; }
  .tab-panel { display: block !important; }    /* show all tabs */
  .week-body { display: block !important; }    /* expand accordion */
  .week-body-inner { overflow: visible !important; }
  .step-section-body { display: block !important; }
  .step-section-inner { overflow: visible !important; }
  .week-chevron, .section-chevron { display: none !important; }
  .code-block { background: #f0f0f5 !important; border-color: #ccc !important; }
  .code-line { color: #1a1a2e !important; }
}
```

### 5. Step-by-Step Tutorial Card Layout
```html
<div class="step">
  <div class="step-num">1</div>   <!-- circular number badge -->
  <div class="step-body">
    <div class="step-label">Install Homebrew</div>
    <div class="code-block">...</div>
  </div>
</div>
```

### 6. Prompt Example Box
```html
<div class="prompt-box">
  <div class="prompt-box-label">Paste this into Claude Code →</div>
  <div class="prompt-box-text">You are a direct-response copywriter...</div>
</div>
```

### 7. 3-Part Formula Display
```html
<div class="prompt-formula">
  <div class="formula-part"><div class="formula-label">Role</div><div class="formula-desc">...</div></div>
  <div class="formula-plus">+</div>
  <div class="formula-part">...</div>
  <div class="formula-plus">+</div>
  <div class="formula-part">...</div>
</div>
```

---

## File Locations

| File | Purpose |
|---|---|
| `/Users/apple/abdul/ducker_creative/frontends/pricing/ai-roadmap.html` | 3-month AI roadmap, Mac dark UI, accordion, copy buttons, PDF export |

---

## Notes for Future Projects

- Always use **Kumbh Sans** for display/heading text in Ducker Creative materials
- Use **Inter** for body/UI text (already used on their site too)
- **Orange `#ff6b00`** is the primary action color — use it for CTAs, key highlights, and primary accents
- **Blue `#007acc` + Cyan `#00ccff`** are the trust/tech colors — use for informational elements, Month 1 foundations
- For multi-section docs, use the brand color progression: **Blue → Cyan → Orange → Orange-Red → Amber** (foundation → building → advanced → tools → outcomes)
- The dark Mac theme works well for technical tools, internal docs, and roadmaps — keeps it professional yet brand-consistent
- `grid-template-rows: 0fr → 1fr` is the cleanest CSS accordion animation (no height guessing, truly smooth)
