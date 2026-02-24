# LLM Anatomy — Codex Agent Instructions

## Post-Change Workflow (MANDATORY)

After every code change, you MUST complete ALL of these steps — no exceptions:

1. **Commit** the change with a descriptive message
2. **Push** to the remote repository
3. **Deploy** to Cloudflare Pages: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`
4. **Update agent markdown files** (AGENTS.md, CLAUDE.md, GEMINI.md) with any new lessons learned, updated documentation, or testing requirements resulting from the change

Do not wait for the user to ask. Do not skip any step. This applies to every change, every session.

## Project Summary

A four-page interactive reference site covering LLM training and inference. Two pages cover the technical pipeline (training + inference), two cover business economics (training + inference). Dark/light theme, editorial design, progressive disclosure. No framework, no build step — four self-contained HTML files with inline CSS and JS.

- **Files**: `site/index.html` (~4,689 lines), `site/economics.html` (~1,752 lines), `site/training.html` (~2,084 lines), `site/training-economics.html` (~1,195 lines)
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
08-ai-infrastructure-inference/
├── AGENTS.md                        ← You are here (Codex instructions)
├── ai-inference-knowledge-base.md   ← Source research document
├── CLAUDE.md                        ← Claude Code instructions
├── GEMINI.md                        ← Gemini instructions
└── site/
    ├── index.html                   ← Inference Technical Pipeline (~4,689 lines)
    ├── economics.html               ← Inference Economics (~1,752 lines)
    ├── training.html                ← Training Technical Pipeline (~2,084 lines)
    └── training-economics.html      ← Training Economics (~1,195 lines)
```

## Architecture

### index.html — Inference Technical Pipeline

Self-contained single file:
- **Lines 10-1352**: `<style>` block (all CSS)
- **Lines 1353-1361**: Theme init script (in `<head>`, prevents FOUC)
- **Lines 1365-1395**: Site nav + minimap
- **Lines 1398-1441**: Hero section
- **Lines 1442-2447**: Pipeline section (11 steps in 3 phases)
- **Lines ~2448-2605**: Inference Metrics section (`id="inference-metrics"`)
- **Lines ~2606-2760**: Cross-cutting optimizations
- **Lines ~2761-2814**: Serving frameworks
- **Lines ~2815-2887**: Journey diagram (interactive)
- **Lines ~2889-2892**: Footer
- **Lines ~2893-4689**: `<script>` block (all JS)

### Page Hierarchy — Inference Technical Pipeline
```
Site Nav (fixed top bar — 4 links + divider + theme toggle)
Minimap (fixed right sidebar, collapsed by default, expands on hover)
Hero
Phase Overview (3 cards: A, B, C)
Pipeline (11 steps)
  Phase A: Request Preparation
    01 Request Routing [network]     — includes Geo-Aware Routing sub-topic
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
Inference Metrics (id="inference-metrics")
  — Latency Metrics (TTFT, TPOT, ITL, E2E, Queue Time)
  — Throughput Metrics (tokens/sec, req/sec, concurrent requests)
  — Resource Utilization (GPU, KV cache, HBM, forward time, prefix cache)
  — Reliability & SLOs (error rate, percentiles, availability, goodput)
  — Cost & Efficiency Metrics (cost/1M tokens, GPU-hours/req, cache hit rate)
  — Observability in Practice (Prometheus, Grafana, alerting)
Cross-Cutting Optimizations (quantization, parallelism)
Serving Frameworks (vLLM, SGLang, TRT-LLM, Dynamo)
Journey Diagram
Footer
```

### economics.html — Inference Economics

Self-contained single file with the same component patterns as index.html.

### Page Hierarchy — Inference Economics
```
Site Nav (fixed top bar, "Inference Economics" link active)
Minimap (fixed right sidebar, collapsed by default)
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

### training.html — Training Technical Pipeline

Color accent: `--accent: #E69F00` (Wong amber), distinct from inference blue (`#56B4E9`).

### Page Hierarchy — Training Technical Pipeline
```
Site Nav (fixed top bar, "Training" link active)
Minimap (fixed right sidebar, collapsed by default)
Hero — "Anatomy of LLM Training" (amber accent tint)
Phase Overview (3 cards: G, H, I)
Pipeline (10 sections)
  Phase G: Data & Architecture
    G1 Data Pipeline [io]               id="data-pipeline"
    G2 Tokenizer Training [logic]       id="tokenizer"
    G3 Model Architecture [compute]     id="architecture"
  Phase H: Training Process
    H1 Optimization [compute]           id="optimization"
    H2 Distributed Training [network]   id="distributed"
    H3 Monitoring & Recovery [logic]    id="monitoring"
  Phase I: Post-Training
    I1 Supervised Fine-Tuning [logic]   id="sft"
    I2 Alignment [logic]               id="alignment"
    I3 Reinforcement Fine-Tuning [logic] id="rft"
    I4 Evaluation [logic]              id="evaluation"
Footer
```

### training-economics.html — Training Economics

### Page Hierarchy — Training Economics
```
Site Nav (fixed top bar, "Training Economics" link active)
Minimap (fixed right sidebar, collapsed by default)
Hero — "From GPU to Balance Sheet" (green accent tint)
Phase Overview (3 cards: J, K, L)
Pipeline (9 sections)
  Phase J: Training Cost Stack
    J1 Hardware & Compute Costs [economics]  id="hardware-costs"
    J2 Scaling Laws & Efficiency [economics] id="scaling-laws"
    J3 Failure & Wasted Compute [economics]  id="failure-costs"
  Phase K: Business Models
    K1 Build vs Fine-Tune vs API [economics]  id="build-vs-buy"
    K2 Training Providers [economics]         id="training-providers"
    K3 Cloud vs On-Premise [economics]        id="cloud-vs-onprem"
  Phase L: Capital & Market
    L1 GPU Financing [economics]              id="gpu-financing"
    L2 Foundation Model Funding [economics]   id="model-funding"
    L3 Training vs Inference Spend [economics] id="training-vs-inference"
Footer
```

## Shared Navigation System

All four pages have an identical `<nav class="site-nav">` with:
- Logo link "LLM Anatomy" to index.html
- "Training" link (active on training.html)
- "Training Economics" link (active on training-economics.html)
- Vertical `nav-divider` separating training from inference
- "Inference" link (active on index.html)
- "Inference Economics" link (active on economics.html)
- Theme toggle button (synced via `localStorage` key `'theme'`)

```html
<nav class="site-nav">
  <a href="index.html" class="nav-logo">LLM Anatomy</a>
  <div class="nav-links">
    <a href="training.html" class="nav-link">Training</a>
    <a href="training-economics.html" class="nav-link">Training Economics</a>
    <span class="nav-divider"></span>
    <a href="index.html" class="nav-link active">Inference</a>
    <a href="economics.html" class="nav-link">Inference Economics</a>
    <button class="theme-toggle" id="theme-toggle" aria-label="Toggle theme"></button>
  </div>
</nav>
```

The nav uses `backdrop-filter: blur(12px)` which creates a stacking context. Do not nest `position: fixed` elements inside it.

### Theme System
- **Init script** runs in `<head>` before body renders (prevents FOUC)
- Reads `localStorage.getItem('theme')`, falls back to `prefers-color-scheme`
- Sets `data-theme` attribute on `<html>` element
- All four pages share the same `localStorage` key `'theme'`
- Any new page must include the exact same init pattern

### Minimap Behavior
- Collapsed by default on all pages (labels hidden with `opacity: 0; width: 0`)
- Expands on hover (`.minimap:hover .minimap-label { opacity: 1; width: auto; }`)
- Hidden entirely below 1440px viewport width
- Click handler uses `window.scrollTo` with 64px offset (to avoid nav overlap)
- `scrollLock` timeout (800ms) suppresses the IntersectionObserver during smooth scroll

## Cross-Link System

### Inference Technical → Inference Economics (4 content links)
- Step 09 (decode) → `economics.html#throughput`
- Step 11 (streaming) → `economics.html#pricing`
- Cross-cutting (optimization) → `economics.html#throughput`
- Cross-cutting (networking) → `economics.html#cost-stack`

### Inference Technical → Training (3 content links)
- Step 03 (Tokenization) → `training.html#tokenizer`
- Step 04 (Embedding) → `training.html#architecture`
- Cross-cutting (Parallelism) → `training.html#distributed`

### Inference Economics → Inference Technical (6 content links)
- D1 (cost stack) → `index.html#cross-cutting`
- D2 (throughput, optimization) → `index.html#cross-cutting`
- D2 (throughput, batching) → `index.html#phase-b`
- D3 (pricing) → `index.html#phase-a`
- E1 (managed vs rental) → `index.html#phase-b`
- E3 (data centers) → `index.html#cross-cutting`

### Inference Economics → Training Economics (2 content links)
- D1 (cost stack, networking) → `training-economics.html#hardware-costs`
- F3 (stage by stage) → `training-economics.html#model-funding`

### Training → Inference/Economics (3 content links)
- G2 (tokenizer) → `index.html#phase-a`
- H2 (distributed) → `index.html#cross-cutting`
- H3 (monitoring) → `training-economics.html#failure-costs`

### Training Economics → Other Pages (4 content links)
- J1 (hardware) → `economics.html#cost-stack`
- J3 (failures) → `training.html#monitoring`
- K3 (cloud vs on-prem) → `economics.html#data-centers`
- L1 (GPU financing) → `economics.html#equity-vs-debt`

**Critical: Anchor ID mismatch risk.** index.html pipeline steps use `data-step` attributes, NOT `id` attributes. Cross-links must target phase/section IDs (`#phase-a`, `#phase-b`, `#cross-cutting`, `#inference-metrics`), not step-level IDs. Verify target anchors before creating any cross-page link.

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

## Interactive Visuals

### economics.html (4 visuals)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Cost waterfall | `visual-cost-waterfall` | `initCostWaterfall()` | Hover segments for cost breakdown |
| Deflation timeline | `visual-deflation-timeline` | `initDeflationTimeline()` | Scroll-triggered fill via IntersectionObserver |
| Breakeven calculator | `visual-breakeven-calc` | `initBreakevenCalc()` | Two range sliders update 4 result values |
| Capital structure bars | `visual-capital-bars` | `initCapitalBars()` | Click row to toggle detail panel |

### training.html (3 visuals)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Data funnel | `visual-data-funnel` | `initDataFunnel()` | Animated pipeline showing data volume shrinking at each stage |
| Optimizer comparison | `visual-optimizer-compare` | `initOptimizerCompare()` | Bar charts comparing convergence speed and memory usage |
| Parallelism diagram | `visual-parallelism` | `initParallelism()` | 4x4 GPU grid with mode toggle buttons (DP/TP/PP/4D) |

### training-economics.html (4 visuals)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Training cost waterfall | `visual-training-waterfall` | `initTrainingWaterfall()` | Stacked bar breakdown + model cost comparison |
| Scaling curve | `visual-scaling-curve` | `initScalingCurve()` | Log-scale over-training comparison bars |
| Cost calculator | `visual-cost-calc` | `initCostCalc()` | Two sliders (model size, tokens) update 4 cost results |
| Spend timeline | `visual-spend-timeline` | `initSpendTimeline()` | Stacked bars showing training/inference spend shift 2023-2026 |

**Critical**: The breakeven calculator (economics.html) creates `.term` elements via `innerHTML`. These miss the initial `querySelectorAll('.term')` event binding loop — manual event handlers are bound inside `initBreakevenCalc()`.

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
1. **Step toggle**: `.step-card` click → toggles `.active` on `.pipeline-step` → reveals `.step-detail` via `max-height`. Multiple sections can be open simultaneously — no accordion.
2. **Sub-topic toggle**: `onclick="toggleSubTopic(this)"` → toggles `.expanded`. Multiple sub-topics can be open simultaneously — no accordion.
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

When modifying interactions on any page:
- [ ] Click each pipeline step / section 10+ times — verify toggle works consistently
- [ ] Click a term inside a sub-topic — verify only tooltip appears (no parent toggle)
- [ ] Click the same term twice — verify it dismisses cleanly (no flash)
- [ ] Click a term, then click elsewhere — verify tooltip closes
- [ ] Click a term, then click a different term — verify first closes, second opens
- [ ] Search modal opens via Cmd+K (Mac) / Ctrl+K (Windows) without visible lag
- [ ] Fuzzy query tolerance works (e.g., minor typos still return intended section)
- [ ] In-content search works (query from paragraph/detail text, not just section title, returns a hit)
- [ ] Knowledge download button exports a `.md` file without console errors
- [ ] Exported markdown includes content from all four pages (spot check one phrase from each page body)
- [ ] Test at viewport < 768px — verify responsive layout
- [ ] Verify no JS errors in console on all 4 pages

### Deployment Verification
- [ ] All 4 pages load without console errors
- [ ] All 11 inference steps + 10 training sections + 9 inference economics + 9 training economics sections expand/collapse
- [ ] Nav shows 4 links + divider + theme toggle on all pages, `.active` class correct
- [ ] Cross-links navigate to correct sections across all 4 pages
- [ ] Theme toggle works on all 4 pages, persists across navigation
- [ ] Interactive visuals on economics.html: cost waterfall hover, breakeven sliders, deflation timeline, capital bars
- [ ] Interactive visuals on training.html: data funnel, optimizer comparison, parallelism diagram
- [ ] Interactive visuals on training-economics.html: training waterfall, scaling curve, cost calculator, spend timeline
- [ ] All ~90 term tooltips work (~46 inference + ~12 inference-econ + ~30 training + ~3 training-econ) — click show/dismiss
- [ ] Responsive at 768px: nav adapts, minimap hides, content stacks on all pages
- [ ] Light/dark theme: all 4 pages adapt correctly
- [ ] Minimap navigation: click EVERY minimap item on all 4 pages and verify correct section
- [ ] Training page uses amber accent (#E69F00), distinct from inference blue
- [ ] Knowledge markdown download works on all 4 pages and reflects latest live content
- [ ] No console errors on any page

## Common Pitfalls

1. **WCAG contrast**: Always check new colors against `#111115` (bg-card). The Wong palette is colorblind-safe but not guaranteed WCAG AA on dark backgrounds.

2. **Stacking contexts**: `backdrop-filter`, `transform`, `filter`, `will-change` create new stacking contexts. `position: fixed` tooltips behave differently inside these. Tooltips are appended to `document.body` to avoid this.

3. **HTML entity encoding**: Use `&amp;` for &, `&rarr;` for →, `&times;` for ×, `&mdash;` for —. Raw `<` and `>` in text content break HTML parsing.

4. **Single-file editing**: With ~4,689 lines in index.html and ~2,084 in training.html, always specify line ranges when making changes. Don't rewrite entire files for small edits.

5. **Font loading**: Google Fonts are loaded via `<link>` with `display=swap`. If adding new weights, update the Google Fonts URL.

6. **Output token limits**: Never generate >500 lines in a single write. Plan large file creation as sequential appends.

7. **Anchor ID mismatches**: index.html steps use `data-step` (no `id`). Cross-links from other pages must target phase/section IDs, not step IDs.

8. **Dynamic DOM + event binding**: Elements created via `innerHTML` miss initial event listener setup. Bind handlers manually after insertion.

9. **Minimap scroll and highlight**: Two issues: (1) `scrollIntoView` with `block: 'start'` or `block: 'center'` fails (behind nav, or can't center bottom sections). Use `window.scrollTo` with 64px offset. (2) The scroll-based minimap observer fires during smooth scroll, highlighting the wrong section (collapsed sections are short). Fix: immediately set clicked item as active + use a `scrollLock` timeout (800ms) to suppress the observer during animation.

10. **Theme FOUC**: The theme init script must be in `<head>` (before body renders). If adding a new page, include the exact same init pattern.

11. **No unnecessary abstractions**: The single-file approach is intentional. Don't add build steps, CSS preprocessors, JS frameworks, or separate files.

12. **Section toggle responsiveness**: Keep section expand/collapse interactions snappy. For `.pipeline-step` UI states, use targeted transitions (`border-color`, `background-color`, `opacity`, `transform`) around `0.18s` instead of broad `transition: all 0.3s`. Keep `.step-detail` at `max-height 0.18s ease-out` across all four pages for consistent feel.

13. **Search responsiveness and quality**: Avoid expensive effects in search interactions (`backdrop-filter` blur, broad `transition: all`, per-render event listener binding). Keep search fast with debounced input, event delegation for result rows, and lazy cross-page content indexing so fuzzy matching can include section body text.

14. **Knowledge export freshness**: Do not maintain a static checked-in knowledge markdown snapshot. Generate markdown on demand from live page HTML (same-origin fetch + parsing) so downloads always reflect the latest deployed content.

15. **Interactive visual sizing**: The prefill attention matrix used `paddingBottom: 100%` for square cells, but `1fr` columns on wide viewports made cells ~100px tall, overflowing the 260px container. Fix: compute cell pixel size from available container height. Always test visuals at multiple viewport widths.

16. **Visual element overlap**: The prefill stat label overlapped the legend because both were positioned top-right. When fixing a UI element, audit the same pattern across all pages. For overlapping positioned elements, move one to a different quadrant.

17. **Radial layout overflow**: The decode loop visual positions nodes in a circle using trigonometry. Always account for node dimensions (width/height) plus border/padding when computing the radius — the node center is not its visual edge. Add a top padding buffer, shift the circle center down, and clamp node positions to stay >=20px from container edges.

18. **External data sources**: The site includes concrete pricing and infrastructure data from Fireworks AI and Crusoe Cloud public docs. Key data points: Fireworks serverless tiers by model size, model-specific pricing (DeepSeek V3/R1, GLM-5, Kimi K2.5), on-demand GPU rates, fine-tuning pricing ($0.50-$10/M tokens), SOC2/HIPAA/GDPR compliance, FireAttention/LoRA multiplexing. Crusoe: on-demand/spot GPU rates, MemoryAlloy (cluster KV cache via RDMA), facility portfolio (Abilene TX 1.2GW, Iceland, Wyoming, Norway, Argentina), $3.4B Series E + $9.6B infra debt. The `rdma` term is defined in both index.html and economics.html termDefs.
