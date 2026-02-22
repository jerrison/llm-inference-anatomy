# LLM Inference Anatomy — Claude Code Instructions

## Project Overview

A single-page interactive reference site explaining the 11 stages of LLM inference, from request routing to streamed response. Dark theme, editorial aesthetic, progressive disclosure UI.

- **Live site**: https://llm-inference-anatomy.pages.dev
- **Single file**: `site/index.html` (~1965 lines, all HTML + CSS + JS inline)
- **Hosting**: Cloudflare Pages, project name `llm-inference-anatomy`

## Architecture

Everything lives in one file: `site/index.html`. No build step, no framework, no dependencies beyond Google Fonts.

### Page Structure (top to bottom)
1. **Hero** — full-viewport intro with animated fade-up
2. **Phase Overview** — 3-card grid (A/B/C) linking to phase anchors
3. **Pipeline Section** — 11 steps grouped into 3 phases with vertical timeline
4. **Cross-Cutting Optimizations** — Quantization + Parallelism (not a sequential step)
5. **Serving Frameworks** — vLLM, SGLang, TensorRT-LLM, Dynamo
6. **Journey Diagram** — ASCII-art end-to-end summary
7. **Footer**

### Three Phases
- **Phase A (steps 01-04)**: Request Preparation — routing, preprocessing, tokenization, embedding
- **Phase B (steps 05-09)**: GPU Computation — scheduling, prefill, KV cache, attention, decode
- **Phase C (steps 10-11)**: Output & Delivery — sampling, detokenization/streaming

## CSS Conventions

### Design Tokens (CSS Variables)
```css
/* Backgrounds */
--bg: #0a0a0c;  --bg-card: #111115;  --bg-card-hover: #18181e;

/* Borders */
--border: #1e1e28;  --border-active: #3a3a50;

/* Text hierarchy */
--text: #e8e6e3;  --text-dim: #9a98a0;  --text-muted: #4a4850;

/* Accent */
--accent: #56B4E9;  --accent-dim: #3a8dc4;  --accent-glow: rgba(86,180,233,0.08);

/* Wong colorblind-safe palette (badge categories) */
--compute: #D55E00;   /* vermillion */
--memory: #0090D6;    /* blue (brightened for WCAG AA) */
--io: #E69F00;        /* amber */
--logic: #CC79A7;     /* reddish pink */
--network: #56B4E9;   /* sky blue */

/* Typography */
--font-display: 'Instrument Serif', serif;   /* headings */
--font-body: 'Atkinson Hyperlegible', sans-serif;  /* body text */
--font-mono: 'DM Mono', monospace;           /* labels, code, badges */
```

### Badge System
Each step has a category badge with color + icon (non-color differentiator):
- `.badge-compute` — `⚡` (U+26A1) vermillion
- `.badge-memory` — `☰` (U+2630) blue
- `.badge-io` — `⇄` (U+21C4) amber
- `.badge-logic` — `⚙` (U+2699) pink
- `.badge-network` — `◎` (U+25CE) sky blue

Icons are set via `::before` pseudo-elements.

### Label Pattern
All section/detail labels use this pattern:
```css
font-family: var(--font-mono);
font-size: 0.6rem;
letter-spacing: 0.2em;
text-transform: uppercase;
color: var(--accent-dim);  /* or --text-muted for section labels */
```

### Responsive Breakpoint
Single breakpoint at `768px`. Key changes: pipeline grid shrinks, badges hidden, phase arrows become vertical.

## HTML Templates

### Adding a New Pipeline Step
```html
<div class="pipeline-step reveal" data-step="step-id">
  <div class="step-marker"><div class="step-dot"></div></div>
  <div class="step-card">
    <div class="step-header">
      <span class="step-number">NN</span>
      <span class="step-name">Step Name</span>
      <span class="step-badge badge-CATEGORY">Category</span>
      <span class="step-expand-icon">+</span>
    </div>
    <div class="step-summary">One-line summary with optional <span class="term" data-term="term-key">Term Name</span>.</div>
    <div class="step-detail">
      <div class="detail-inner">
        <div class="callout-box callout-takeaway">
          <div class="callout-label">Key Takeaway</div>
          <p>Bold insight sentence.</p>
        </div>
        <div class="callout-box callout-analogy">
          <div class="callout-label">Think of it like...</div>
          <p>Real-world analogy in italics.</p>
        </div>
        <div class="detail-section">
          <div class="detail-label">Section Name</div>
          <div class="detail-text">Content here.</div>
        </div>
      </div>
    </div>
  </div>
</div>
```

### Adding a New Technical Term
1. Add the definition to the `termDefs` object in the `<script>` section:
   ```js
   'term-key': 'Definition text with no HTML.',
   ```
2. Use the term inline in HTML:
   ```html
   <span class="term" data-term="term-key">Display Text</span>
   ```

Terms are clickable with tooltip popups. The JS handles `stopPropagation()` to prevent parent container toggles from firing.

## JavaScript Interaction Patterns

### Three interaction layers (nested, with event isolation)
1. **Pipeline step toggle** — clicking `.step-card` toggles `.active` on `.pipeline-step`
2. **Sub-topic toggle** — clicking `.sub-topic` expands it (accordion within step)
3. **Term tooltip** — clicking `.term` shows a definition tooltip

### Critical: Event Propagation
Terms are inside sub-topics which are inside step-cards. Direct `addEventListener` with `stopPropagation()` on each `.term` element prevents parent handlers from firing. Do NOT use event delegation for terms — it causes bubbling bugs.

### Tooltip Toggle Pattern
Track both `activeTooltip` (DOM element) and `activeTermEl` (which term is showing). Clicking the same term dismisses; clicking a different term swaps.

## Deployment

```bash
npx wrangler pages deploy ./site --project-name=llm-inference-anatomy
```

No build step needed. The `site/` directory is deployed directly.

## WCAG AA Compliance

All text colors must maintain 4.5:1 contrast ratio against their background:
- `--text` (#e8e6e3) on `--bg-card` (#111115): 13.5:1 ✓
- `--text-dim` (#9a98a0) on `--bg-card`: 5.3:1 ✓
- `--accent` (#56B4E9) on `--bg-card`: 7.2:1 ✓
- `--memory` (#0090D6) on `--bg-card`: 5.3:1 ✓ (was #0072B2 at 3.6:1, fixed)
- `--text-muted` (#4a4850): Decorative only, exempt as incidental text

When adding new colors, always verify contrast against `--bg-card` (#111115). Use relative luminance formula: `L = 0.2126*R + 0.7152*G + 0.0722*B` after linearizing sRGB.

## Lessons Learned

### Event Handling in Nested Clickables
Never use document-level event delegation for elements nested inside other clickable containers. By the time the event reaches the document handler, it has already triggered parent handlers. Attach direct handlers with `stopPropagation()` at the source element.

### Tooltip Toggle State
Always track the active element reference alongside the tooltip DOM node. Without `activeTermEl`, clicking the same term creates a flash (remove + recreate) instead of a clean toggle-off.

### WCAG Contrast Checking
The Wong palette is colorblind-safe by design, but individual colors still need contrast verification against the specific background. `#0072B2` is a standard Wong blue but only achieves 3.6:1 on dark backgrounds — insufficient for WCAG AA body text.

### Prefix Cache Optimization
When constructing prompts for this site's content (if used as context for an LLM), place static/shared content first and dynamic content last to maximize prefix cache hits.
