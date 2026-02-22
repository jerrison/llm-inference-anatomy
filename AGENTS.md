# LLM Inference Anatomy — Codex Agent Instructions

## Project Summary

Single-page interactive reference site explaining the 11 stages of LLM inference. Dark theme, editorial design, progressive disclosure. No framework, no build step — one HTML file with inline CSS and JS.

- **File**: `site/index.html` (~1965 lines)
- **Hosting**: Cloudflare Pages (`llm-inference-anatomy`)
- **Deploy**: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`
- **Live**: https://llm-inference-anatomy.pages.dev

## Sandbox Setup

This project has no dependencies to install. The only tool needed for deployment is `wrangler` (Cloudflare CLI). For local preview, any static file server works:

```bash
# Local preview
cd site && python3 -m http.server 8080

# Deploy
npx wrangler pages deploy ./site --project-name=llm-inference-anatomy
```

## File Structure

```
05-ai-infrastructure-inference/
├── AGENTS.md          ← You are here (Codex instructions)
├── CLAUDE.md          ← Claude Code instructions
├── GEMINI.md          ← Gemini instructions
└── site/
    └── index.html     ← The entire site (HTML + CSS + JS inline)
```

## Architecture

`site/index.html` is a self-contained single file:
- **Lines 1-10**: Head, meta, Google Fonts import
- **Lines 10-809**: `<style>` block (all CSS)
- **Lines 811-835**: Hero section
- **Lines 822-855**: Phase overview (3-card grid)
- **Lines 857-1652**: Pipeline section (11 steps in 3 phases)
- **Lines 1654-1712**: Cross-cutting optimizations
- **Lines 1714-1766**: Serving frameworks
- **Lines 1768-1829**: Journey diagram (ASCII art)
- **Lines 1831-1834**: Footer
- **Lines 1836-1962**: `<script>` block (all JS)

### Page Hierarchy
```
Hero
Phase Overview (3 cards: A, B, C)
Pipeline (11 steps)
  Phase A: Request Preparation
    01 Request Routing [network]
    02 Preprocessing [logic]
    03 Tokenization [logic]
    04 Embedding & Position [memory]
  Phase B: GPU Computation
    05 Scheduling & Batching [logic]
    06 Prefill Phase [compute]
    07 KV Cache & Memory [memory]
    08 Attention Mechanisms [compute]
    09 Decode Phase [memory]
  Phase C: Output & Delivery
    10 Sampling & Selection [logic]
    11 Detokenization & Streaming [io]
Cross-Cutting Optimizations (quantization, parallelism)
Serving Frameworks (vLLM, SGLang, TRT-LLM, Dynamo)
Journey Diagram
Footer
```

## Design System

### CSS Variables (Design Tokens)
```css
/* Backgrounds */
--bg: #0a0a0c;  --bg-card: #111115;  --bg-card-hover: #18181e;

/* Borders */
--border: #1e1e28;  --border-active: #3a3a50;

/* Text (light on dark) */
--text: #e8e6e3;       /* primary — 13.5:1 contrast */
--text-dim: #9a98a0;   /* secondary — 5.3:1 contrast */
--text-muted: #4a4850; /* decorative only — 2.2:1, WCAG exempt */

/* Accent */
--accent: #56B4E9;  --accent-dim: #3a8dc4;

/* Category palette (Wong colorblind-safe) */
--compute: #D55E00;  /* vermillion */
--memory: #0090D6;   /* blue */
--io: #E69F00;       /* amber */
--logic: #CC79A7;    /* pink */
--network: #56B4E9;  /* sky blue */

/* Fonts */
--font-display: 'Instrument Serif', serif;
--font-body: 'Atkinson Hyperlegible', sans-serif;
--font-mono: 'DM Mono', monospace;
```

### WCAG AA Requirements
All body text must achieve 4.5:1 contrast against `--bg-card` (#111115). When changing or adding colors, verify with:
```
contrast = (L_lighter + 0.05) / (L_darker + 0.05)
L = 0.2126*R_lin + 0.7152*G_lin + 0.0722*B_lin
```
Previous bug: `--memory` was `#0072B2` (3.6:1 — FAIL). Fixed to `#0090D6` (5.3:1 — PASS).

### Badge Categories
Each pipeline step has a badge with color + icon. Icons use `::before` pseudo-elements so color is never the only differentiator:

| Class | Color var | Icon | Unicode |
|-------|-----------|------|---------|
| `.badge-compute` | `--compute` | ⚡ | U+26A1 |
| `.badge-memory` | `--memory` | ☰ | U+2630 |
| `.badge-io` | `--io` | ⇄ | U+21C4 |
| `.badge-logic` | `--logic` | ⚙ | U+2699 |
| `.badge-network` | `--network` | ◎ | U+25CE |

### Typography
- Headings: `var(--font-display)` — Instrument Serif, weight 400
- Body: `var(--font-body)` — Atkinson Hyperlegible, weight 400, line-height 1.8
- Labels/code: `var(--font-mono)` — DM Mono

### Label Pattern (repeated throughout)
```css
font-family: var(--font-mono);
font-size: 0.6rem;
letter-spacing: 0.2em;
text-transform: uppercase;
```

### Responsive
Single breakpoint at `max-width: 768px`. Changes: pipeline grid narrows, badges hidden, phase arrows go vertical, sub-topics stack single-column.

## HTML Templates

### New Pipeline Step
```html
<div class="pipeline-step reveal" data-step="unique-id">
  <div class="step-marker"><div class="step-dot"></div></div>
  <div class="step-card">
    <div class="step-header">
      <span class="step-number">NN</span>
      <span class="step-name">Name</span>
      <span class="step-badge badge-CATEGORY">Label</span>
      <span class="step-expand-icon">+</span>
    </div>
    <div class="step-summary">Summary text.</div>
    <div class="step-detail">
      <div class="detail-inner">
        <!-- Callouts first -->
        <div class="callout-box callout-takeaway">
          <div class="callout-label">Key Takeaway</div>
          <p>Bold insight.</p>
        </div>
        <div class="callout-box callout-analogy">
          <div class="callout-label">Think of it like...</div>
          <p>Analogy.</p>
        </div>
        <!-- Then detail sections -->
        <div class="detail-section">
          <div class="detail-label">Section Title</div>
          <div class="detail-text">Content.</div>
        </div>
        <!-- Optional sub-topics -->
        <div class="detail-section">
          <div class="detail-label">Drill into specifics</div>
          <div class="sub-topics">
            <div class="sub-topic" onclick="toggleSubTopic(this)">
              <div class="sub-topic-header">
                <span class="sub-topic-name">Topic</span>
                <span class="sub-topic-icon">+</span>
              </div>
              <div class="sub-topic-preview">Preview text</div>
              <div class="sub-topic-detail"><p>Detail content.</p></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

### New Technical Term
1. Add to `termDefs` in `<script>`:
   ```js
   'term-key': 'Plain text definition. No HTML.',
   ```
2. Use inline:
   ```html
   <span class="term" data-term="term-key">Display Text</span>
   ```

## JavaScript Patterns

### Three Interaction Layers
1. **Step toggle**: `.step-card` click → toggles `.active` on `.pipeline-step` → reveals `.step-detail` via `max-height`
2. **Sub-topic accordion**: `onclick="toggleSubTopic(this)"` → toggles `.expanded`, closes siblings
3. **Term tooltip**: Direct click handler on each `.term` with `stopPropagation()` → shows/toggles floating tooltip

### Event Propagation Rule
Terms are nested inside sub-topics inside step-cards. Each layer must prevent its click from bubbling to parent layers:
- `.term` click handlers call `e.stopPropagation()` + `e.preventDefault()`
- `.step-card` click handler checks `if (e.target.closest('.sub-topic')) return;` and `if (e.target.closest('.term')) return;`

**Do NOT use event delegation** (document-level handlers) for elements nested inside other clickable containers. It causes parent handlers to fire before the delegation can intercept.

### Tooltip State
Two variables track tooltip state:
- `activeTooltip` — the tooltip DOM element (or null)
- `activeTermEl` — which `.term` element triggered it (or null)

Both must be cleared together. Without `activeTermEl`, clicking the same term creates a flash instead of toggling off.

### Scroll Reveal
`IntersectionObserver` adds `.visible` to `.reveal` elements. One-way (no removal on scroll-out). Threshold 0.1 with -40px bottom margin.

## Testing Checklist

When modifying interactions:
- [ ] Click each pipeline step 10+ times — verify toggle works consistently
- [ ] Click a term inside a sub-topic — verify only tooltip appears (no parent toggle)
- [ ] Click the same term twice — verify it dismisses cleanly (no flash)
- [ ] Click a term, then click elsewhere — verify tooltip closes
- [ ] Click a term, then click a different term — verify first closes, second opens
- [ ] Test at viewport < 768px — verify responsive layout
- [ ] Verify no JS errors in console
- [ ] Run `node --check site/index.html` equivalent on extracted JS

## Common Pitfalls

1. **WCAG contrast**: Always check new colors against `#111115` (bg-card). The Wong palette is colorblind-safe but not guaranteed WCAG AA on dark backgrounds.

2. **Stacking contexts**: `backdrop-filter`, `transform`, `filter`, `will-change` create new stacking contexts. `position: fixed` tooltips behave differently inside these. The tooltip is appended to `document.body` to avoid this issue.

3. **HTML entity encoding**: Use `&amp;` for &, `&rarr;` for →, `&times;` for ×, `&mdash;` for —, etc. Raw `<` and `>` in text content will break HTML parsing.

4. **Single-file editing**: With ~1965 lines in one file, always specify line ranges when making changes. Don't rewrite the entire file for small edits.

5. **Font loading**: Google Fonts are loaded via `<link>` with `display=swap`. If adding new weights, update the Google Fonts URL on line 9.
