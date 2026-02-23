# LLM Inference Anatomy — Codex Agent Instructions

## Project Summary

Two-page interactive reference site: a Technical Pipeline explaining the 11 stages of LLM inference, and a Business Economics page covering unit economics, business models, and capital structure. Dark/light theme, editorial design, progressive disclosure. No framework, no build step — two self-contained HTML files with inline CSS and JS.

- **Files**: `site/index.html` (~4,246 lines), `site/economics.html` (~1,694 lines)
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
├── AGENTS.md                        ← You are here (Codex instructions)
├── ai-inference-knowledge-base.md   ← Source research document
├── CLAUDE.md                        ← Claude Code instructions
├── GEMINI.md                        ← Gemini instructions
└── site/
    ├── index.html                   ← Technical Pipeline (HTML + CSS + JS inline, ~4,246 lines)
    └── economics.html               ← Business Economics (HTML + CSS + JS inline, ~1,694 lines)
```

## Architecture

### index.html — Technical Pipeline

Self-contained single file:
- **Lines 10-1352**: `<style>` block (all CSS)
- **Lines 1353-1361**: Theme init script (in `<head>`, prevents FOUC)
- **Lines 1365-1395**: Site nav + minimap
- **Lines 1398-1441**: Hero section
- **Lines 1442-2447**: Pipeline section (11 steps in 3 phases)
- **Lines 2448-2605**: Cross-cutting optimizations
- **Lines 2606-2659**: Serving frameworks
- **Lines 2660-2732**: Journey diagram (interactive)
- **Lines 2734-2737**: Footer
- **Lines 2738-4243**: `<script>` block (all JS)

### Page Hierarchy — Technical Pipeline
```
Site Nav (fixed top bar)
Minimap (fixed right sidebar)
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

### economics.html — Business Economics

Self-contained single file with the same component patterns as index.html.

### Page Hierarchy — Economics
```
Site Nav (fixed top bar, "Economics" link active)
Minimap (fixed right sidebar)
Hero — "From Token to Balance Sheet" (green accent tint)
Phase Overview (3 cards: D, E, F)
Pipeline (9 sections)
  Phase D: Unit Economics
    D1 The Cost Stack [economics]        id="cost-stack"
    D2 Throughput as Margin [economics]  id="throughput"
    D3 Pricing Structures [economics]    id="pricing"
  Phase E: Business Models
    E1 Managed vs GPU Rental [economics]  id="managed-vs-rental"
    E2 Buy vs Rent GPUs [economics]       id="buy-vs-rent"
    E3 Data Centers [economics]           id="data-centers"
  Phase F: Capital Structure
    F1 Equity vs Debt [economics]         id="equity-vs-debt"
    F2 Contracted Revenue [economics]     id="contracted-revenue"
    F3 Stage-by-Stage [economics]         id="stage-by-stage"
Footer
```

## Shared Navigation System

Both pages have an identical `<nav class="site-nav">` with:
- Logo link to index.html
- "Technical Pipeline" link (active on index.html)
- "Economics" link (active on economics.html)
- Theme toggle button (synced via `localStorage` key `'theme'`)

The nav uses `backdrop-filter: blur(12px)` which creates a stacking context. Do not nest `position: fixed` elements inside it.

## Cross-Link System

### Technical → Economics (4 content links)
- Step 09 (decode) → `economics.html#throughput`
- Step 11 (streaming) → `economics.html#pricing`
- Cross-cutting (optimization) → `economics.html#throughput`
- Cross-cutting (networking) → `economics.html#cost-stack`

### Economics → Technical (6 content links)
- D1 (cost stack) → `index.html#cross-cutting`
- D2 (throughput, optimization) → `index.html#cross-cutting`
- D2 (throughput, batching) → `index.html#phase-b`
- D3 (pricing) → `index.html#phase-a`
- E1 (managed vs rental) → `index.html#phase-b`
- E3 (data centers) → `index.html#cross-cutting`

**Critical: Anchor ID mismatch risk.** index.html pipeline steps use `data-step` attributes, NOT `id` attributes. Cross-links must target phase/section IDs (`#phase-a`, `#phase-b`, `#cross-cutting`), not step-level IDs. Verify target anchors before creating any cross-page link.

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
--compute: #D55E00;   /* vermillion */
--memory: #0090D6;    /* blue */
--io: #E69F00;        /* amber */
--logic: #CC79A7;     /* pink */
--network: #56B4E9;   /* sky blue */
--economics: #009E73;  /* green — light mode: #007A59 */

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
Previous fixes: `--memory` was `#0072B2` (3.6:1 FAIL) → `#0090D6` (5.3:1 PASS). `--economics` (#009E73) achieves 5.65:1 PASS.

### Badge Categories
Each pipeline step/section has a badge with color + icon. Icons use `::before` pseudo-elements so color is never the only differentiator:

| Class | Color var | Icon | Unicode |
|-------|-----------|------|---------|
| `.badge-compute` | `--compute` | ⚡ | U+26A1 |
| `.badge-memory` | `--memory` | ☰ | U+2630 |
| `.badge-io` | `--io` | ⇄ | U+21C4 |
| `.badge-logic` | `--logic` | ⚙ | U+2699 |
| `.badge-network` | `--network` | ◎ | U+25CE |
| `.badge-economics` | `--economics` | $ | U+0024 |

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
Single breakpoint at `max-width: 768px`. Changes: pipeline grid narrows, badges hidden, phase arrows go vertical, sub-topics stack single-column. Minimap hidden below `1440px`.

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
        <div class="callout-box callout-takeaway">
          <div class="callout-label">Key Takeaway</div>
          <p>Bold insight.</p>
        </div>
        <div class="callout-box callout-analogy">
          <div class="callout-label">Think of it like...</div>
          <p>Analogy.</p>
        </div>
        <div class="detail-section">
          <div class="detail-label">Section Title</div>
          <div class="detail-text">Content.</div>
        </div>
      </div>
    </div>
  </div>
</div>
```

### New Technical Term
1. Add to `termDefs` in `<script>`:
   ```js
   'term-key': { def: 'Definition text.', src: 'https://source-url' },
   ```
2. Use inline:
   ```html
   <span class="term" data-term="term-key">Display Text</span>
   ```

### New Cross-Page Link
```html
<a href="economics.html#section-id" class="cross-link">&rarr; Link text</a>
```
Verify the target `id` exists before creating.

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

### Interactive Visuals (economics.html only)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Cost waterfall | `visual-cost-waterfall` | `initCostWaterfall()` | Hover segments for cost breakdown |
| Deflation timeline | `visual-deflation-timeline` | `initDeflationTimeline()` | Scroll-triggered fill via IntersectionObserver |
| Breakeven calculator | `visual-breakeven-calc` | `initBreakevenCalc()` | Two range sliders update 4 result values |
| Capital structure bars | `visual-capital-bars` | `initCapitalBars()` | Click row to toggle detail panel |

**Critical**: The breakeven calculator creates `.term` elements via `innerHTML`. These miss the initial `querySelectorAll('.term')` event binding loop — manual event handlers are bound inside `initBreakevenCalc()`.

### Scroll Reveal
`IntersectionObserver` adds `.visible` to `.reveal` elements. One-way (no removal on scroll-out). Threshold 0.1 with -40px bottom margin.

## Implementation Methodology for Large Single-File HTML Pages

### Recommended approach
```
Phase 1: Research — Read ALL source material before writing any code
Phase 2: Plan — Design information architecture, section IDs, cross-references
Phase 3: CSS first — Write complete <head> with all styles as Part 1
Phase 4: HTML in chunks — Append one phase at a time (verify tag balance after each)
Phase 5: JS last — Write all interaction handlers, visual initializers as final part
Phase 6: Verify — Tag balance, anchor validity, cross-link targets, browser test
Phase 7: Deploy
```

### Chunk sizing
Each append should be 200-400 lines. Never attempt >500 lines in one write — AI output token limits cause truncation. For a ~1,700-line file: CSS head (~340 lines), Phase D (~390 lines), Phase E (~290 lines), Phase F + footer (~270 lines), JavaScript (~400 lines).

### Cross-reference verification
Before creating any cross-page link:
1. `grep 'id="target"' file.html` to verify the anchor exists
2. index.html steps use `data-step`, NOT `id` — link to phase/section IDs instead
3. After all links are written, extract all `href="otherpage.html#X"` and confirm each `id="X"` exists

## Testing Checklist

When modifying interactions on either page:
- [ ] Click each pipeline step / economics section 10+ times — verify toggle works consistently
- [ ] Click a term inside a sub-topic — verify only tooltip appears (no parent toggle)
- [ ] Click the same term twice — verify it dismisses cleanly (no flash)
- [ ] Click a term, then click elsewhere — verify tooltip closes
- [ ] Click a term, then click a different term — verify first closes, second opens
- [ ] Test at viewport < 768px — verify responsive layout
- [ ] Verify no JS errors in console on either page

### Deployment Verification
- [ ] All 11 technical steps + 9 economics sections expand/collapse
- [ ] Nav bar links work bidirectionally (Technical Pipeline ↔ Economics)
- [ ] Cross-links navigate to correct sections on the other page
- [ ] Theme toggle works on both pages, persists across navigation
- [ ] Interactive visuals: cost waterfall hover, breakeven sliders work, deflation timeline animates, capital bars expand
- [ ] All ~58 term tooltips work (46 technical + 12 economics) — click show/dismiss
- [ ] Responsive at 768px: nav adapts, minimap hides, content stacks
- [ ] Light/dark theme: both pages adapt correctly
- [ ] No console errors on either page

## Common Pitfalls

1. **WCAG contrast**: Always check new colors against `#111115` (bg-card). The Wong palette is colorblind-safe but not guaranteed WCAG AA on dark backgrounds.

2. **Stacking contexts**: `backdrop-filter`, `transform`, `filter`, `will-change` create new stacking contexts. `position: fixed` tooltips behave differently inside these. Tooltips are appended to `document.body` to avoid this.

3. **HTML entity encoding**: Use `&amp;` for &, `&rarr;` for →, `&times;` for ×, `&mdash;` for —. Raw `<` and `>` in text content break HTML parsing.

4. **Single-file editing**: With ~4,246 lines in index.html and ~1,694 in economics.html, always specify line ranges when making changes. Don't rewrite entire files for small edits.

5. **Font loading**: Google Fonts are loaded via `<link>` with `display=swap`. If adding new weights, update the Google Fonts URL.

6. **Output token limits**: Never generate >500 lines in a single write. Plan large file creation as sequential appends.

7. **Anchor ID mismatches**: index.html steps use `data-step` (no `id`). Cross-links from economics.html must target phase/section IDs, not step IDs.

8. **Dynamic DOM + event binding**: Elements created via `innerHTML` miss initial event listener setup. Bind handlers manually after insertion.

9. **Minimap scroll offset**: `scrollIntoView({ block: 'start' })` scrolls the target behind the fixed 48px nav bar. Always use `block: 'center'` for minimap navigation so the section header is visible.

9. **Theme FOUC**: The theme init script must be in `<head>` (before body renders). If adding a new page, include the exact same init pattern.

10. **No unnecessary abstractions**: The single-file approach is intentional. Don't add build steps, CSS preprocessors, JS frameworks, or separate files.
