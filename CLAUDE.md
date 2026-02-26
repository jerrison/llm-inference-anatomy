# LLM Anatomy — Claude Code Instructions

## Post-Change Workflow (MANDATORY)

After every code change, you MUST complete ALL of these steps — no exceptions:

1. **Commit** the change with a descriptive message
2. **Push** to the remote repository
3. **Deploy** to Cloudflare Pages: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`
4. **Update agent markdown files** (AGENTS.md, CLAUDE.md, GEMINI.md) with any new lessons learned, updated documentation, or testing requirements resulting from the change

Do not wait for the user to ask. Do not skip any step. This applies to every change, every session.

## Project Overview

A four-page interactive reference site covering LLM training and inference. Two pages cover the technical pipeline (training + inference), two cover business economics (training + inference). Dark/light theme, editorial aesthetic, progressive disclosure UI.

- **Live site**: https://llm-inference-anatomy.pages.dev
- **Hosting**: Cloudflare Pages, project name `llm-inference-anatomy`
- **Deploy**: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`

### File Structure
```
08-ai-infrastructure-inference/
├── AGENTS.md                        ← Codex agent instructions
├── ai-inference-knowledge-base.md   ← Source research document
├── CLAUDE.md                        ← Claude Code instructions (this file)
├── GEMINI.md                        ← Gemini agent instructions
└── site/
    ├── index.html                   ← Inference Technical Pipeline (~4,689 lines)
    ├── economics.html               ← Inference Economics (~3,189 lines)
    ├── training.html                ← Training Technical Pipeline (~2,084 lines)
    └── training-economics.html      ← Training Economics (~1,195 lines)
```

No build step, no framework, no dependencies beyond Google Fonts. Each HTML file is fully self-contained.

## Architecture

### index.html — Technical Pipeline

Page structure (top to bottom):
1. **Site Nav** — fixed top bar with page links + theme toggle
2. **Minimap** — fixed right sidebar, pipeline navigation
3. **Hero** — full-viewport intro with animated fade-up
4. **Phase Overview** — 3-card grid (A/B/C) linking to phase anchors
5. **Pipeline Section** — 11 steps grouped into 3 phases with vertical timeline
6. **Inference Metrics** (`id="inference-metrics"`) — Latency, throughput, resource utilization, SLOs, cost metrics, observability
7. **Cross-Cutting Optimizations** — Quantization + Parallelism (not a sequential step)
8. **Serving Frameworks** — vLLM, SGLang, TensorRT-LLM, Dynamo
9. **Journey Diagram** — interactive end-to-end summary with clickable phases
10. **Footer**

Three Phases:
- **Phase A (steps 01-04)**: Request Preparation — routing, preprocessing, tokenization, embedding
- **Phase B (steps 05-09)**: GPU Computation — scheduling, prefill, KV cache, attention, decode
- **Phase C (steps 10-11)**: Output & Delivery — sampling, detokenization/streaming

Line ranges:
| Section | Lines |
|---------|-------|
| `<style>` block | 10–1352 |
| Theme init script (in `<head>`) | 1353–1361 |
| Nav + minimap | 1365–1395 |
| Hero | 1398–1441 |
| Pipeline (phases A/B/C) | 1442–2447 |
| Cross-cutting optimizations | 2448–2605 |
| Serving frameworks | 2606–2659 |
| Journey diagram | 2660–2732 |
| Footer | 2734–2737 |
| `<script>` block | 2738–4243 |

### economics.html — Business Economics

Page structure (top to bottom):
1. **Site Nav** — same structure as index.html, `active` class on Economics link
2. **Minimap** — fixed right sidebar, section navigation
3. **Hero** — "From Token to Balance Sheet" (green accent tint vs blue)
4. **Phase Overview** — 3-card grid (D/E/F) linking to phase anchors
5. **Pipeline Section** — 9 sections grouped into 3 phases
6. **Footer**

Three Phases:
- **Phase D — Unit Economics** (`id="phase-d"`)
  - D1: The Cost Stack (`id="cost-stack"`) — GPU CapEx, power, data center, networking, operations, GPU power draw & facility cost tables, 3 ownership scenarios (grid competitor, Crusoe-owned, Crusoe-renting), complete cost bridge ($/kWh → $/kW/month → $/GPU-hour)
  - D2: Throughput as Margin (`id="throughput"`) — optimization creates 3-5× (15-17×) throughput variance, cumulative optimization stack (300→5,000 tok/s), revenue per GPU-hour formulas, revenue per MW & facility economics, target margins by business model
  - D3: Pricing Structures (`id="pricing"`) — per-token, per-GPU-hour, reserved, batch, value-based, reserved contracts as fixed-rate swaps, deflation dynamic
- **Phase E — Business Models** (`id="phase-e"`)
  - E1: Managed vs GPU Rental (`id="managed-vs-rental"`) — statistical multiplexing, outcomes vs infra, revenue multiplier comparison (rental vs managed by model size), IPO valuation lens (infrastructure vs software multiples), cannibalization tension
  - E2: Buy vs Rent GPUs (`id="buy-vs-rent"`) — breakeven calculator, ownership cost model, option value lens, barbell strategy
  - E3: Data Centers (`id="data-centers"`) — power costs, PUE, facility economics, CapEx breakdown by component per MW, OpEx breakdown with Crusoe advantage analysis, colocation lease rate benchmarks (9 markets), 100 MW reference facility model
- **Phase F — Capital Structure** (`id="phase-f"`)
  - F1: Equity vs Debt (`id="equity-vs-debt"`) — risk, information asymmetry, tax shields
  - F2: Contracted Revenue (`id="contracted-revenue"`) — pricing decisions unlock debt capacity, 4 mechanisms, tenor economics ($127M/yr freed on 12yr vs 5yr), reserved pricing NPV analysis, virtuous cycle
  - F3: Stage-by-Stage (`id="stage-by-stage"`) — Series A → Public capital evolution, dual capital structure, CapEx in supply-constrained markets (sequential bottleneck cascade), barbell approach (6-domain resource allocation framework)

Uses the same component patterns as index.html: step-card, sub-topic, callout-box, data-table, metrics-row.

### training.html — Training Technical Pipeline

Color accent: `--accent: #E69F00` (Wong amber), distinct from inference blue.

Three Phases:
- **Phase G — Data & Architecture** (`id="phase-g"`)
  - G1: Data Pipeline (`id="data-pipeline"`) — collection, filtering, dedup, mixing
  - G2: Tokenizer Training (`id="tokenizer"`) — BPE, SentencePiece, vocabulary sizes
  - G3: Model Architecture (`id="architecture"`) — attention variants, SwiGLU, MoE, RoPE
- **Phase H — Training Process** (`id="phase-h"`)
  - H1: Optimization (`id="optimization"`) — AdamW, Muon, WSD, mixed precision
  - H2: Distributed Training (`id="distributed"`) — ZeRO, TP, PP, 4D parallelism
  - H3: Monitoring & Recovery (`id="monitoring"`) — failure stats, checkpointing
- **Phase I — Post-Training** (`id="phase-i"`)
  - I1: Supervised Fine-Tuning (`id="sft"`) — LoRA, full fine-tuning
  - I2: Alignment (`id="alignment"`) — RLHF, DPO, GRPO, Constitutional AI, KTO
  - I3: Reinforcement Fine-Tuning (`id="rft"`) — expert-guided RFT, verifiable rewards, process vs outcome rewards, RFT as product
  - I4: Evaluation (`id="evaluation"`) — MMLU, benchmarks, contamination detection

~30 term definitions in termDefs object.

Interactive Visuals (training.html):

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Data funnel | `visual-data-funnel` | `initDataFunnel()` | Animated pipeline showing data volume shrinking at each stage |
| Optimizer comparison | `visual-optimizer-compare` | `initOptimizerCompare()` | Bar charts comparing convergence speed and memory usage |
| Parallelism diagram | `visual-parallelism` | `initParallelism()` | 4x4 GPU grid with mode toggle buttons (DP/TP/PP/4D) |

### training-economics.html — Training Economics

Uses inference blue accent (same as economics.html) for consistency.

Three Phases:
- **Phase J — Training Cost Stack** (`id="phase-j"`)
  - J1: Hardware & Compute Costs (`id="hardware-costs"`)
  - J2: Scaling Laws & Efficiency (`id="scaling-laws"`)
  - J3: Failure & Wasted Compute (`id="failure-costs"`)
- **Phase K — Business Models** (`id="phase-k"`)
  - K1: Build vs Fine-Tune vs API (`id="build-vs-buy"`)
  - K2: Training Providers (`id="training-providers"`)
  - K3: Cloud vs On-Premise (`id="cloud-vs-onprem"`)
- **Phase L — Capital & Market** (`id="phase-l"`)
  - L1: GPU Financing (`id="gpu-financing"`)
  - L2: Foundation Model Funding (`id="model-funding"`)
  - L3: Training vs Inference Spend (`id="training-vs-inference"`)

Interactive Visuals (training-economics.html):

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Training cost waterfall | `visual-training-waterfall` | `initTrainingWaterfall()` | Stacked bar breakdown + model cost comparison |
| Scaling curve | `visual-scaling-curve` | `initScalingCurve()` | Log-scale over-training comparison bars |
| Cost calculator | `visual-cost-calc` | `initCostCalc()` | Two sliders (model size, tokens) update 4 cost results |
| Spend timeline | `visual-spend-timeline` | `initSpendTimeline()` | Stacked bars showing training/inference spend shift 2023-2026 |

## Shared Navigation System

All four pages share an identical `<nav class="site-nav">` structure:
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

The `active` class moves to whichever page is current. The `nav-divider` is a 1px vertical line separating training from inference links. Theme toggle is inside the nav bar (not a floating button).

**CSS**: `.site-nav` uses `position: fixed`, `backdrop-filter: blur(12px)`, translucent background. This creates a stacking context — do NOT place `position: fixed` children inside it.

### Theme System

All four pages use the same localStorage-based theme:
1. **Init script in `<head>`** (runs before body renders to prevent FOUC):
   ```js
   (function(){
     var s = localStorage.getItem('theme');
     if (s) { document.documentElement.setAttribute('data-theme', s); }
     else if (window.matchMedia('(prefers-color-scheme: light)').matches) {
       document.documentElement.setAttribute('data-theme', 'light');
     } else { document.documentElement.setAttribute('data-theme', 'dark'); }
   })();
   ```
2. **Toggle handler**: Reads current `data-theme`, flips to opposite, saves to `localStorage` key `'theme'`
3. **CSS overrides**: `[data-theme="light"]` selector changes all design tokens

Any new page must include this exact init pattern in `<head>` to stay in sync.

## Cross-Link System

### Technical → Economics (4 content links + 1 nav)
| Source (index.html) | Target (economics.html) |
|---------------------|------------------------|
| Nav bar | `economics.html` |
| Step 09 (decode) | `economics.html#throughput` |
| Step 11 (streaming) | `economics.html#pricing` |
| Cross-cutting (optimization) | `economics.html#throughput` |
| Cross-cutting (networking) | `economics.html#cost-stack` |

### Inference Technical → Training (3 content links)
| Source (index.html) | Target |
|---------------------|--------|
| Step 03 (Tokenization) | `training.html#tokenizer` |
| Step 04 (Embedding) | `training.html#architecture` |
| Cross-cutting (Parallelism) | `training.html#distributed` |

### Economics → Technical (6 content links + 1 nav)
| Source (economics.html) | Target (index.html) |
|------------------------|---------------------|
| Nav bar | `index.html` |
| D1: Cost Stack | `index.html#cross-cutting` |
| D1: Cost Stack (networking) | `training-economics.html#hardware-costs` |
| D2: Throughput (optimization) | `index.html#cross-cutting` |
| D2: Throughput (batching) | `index.html#phase-b` |
| D3: Pricing | `index.html#phase-a` |
| E1: Managed vs Rental | `index.html#phase-b` |
| E3: Data Centers | `index.html#cross-cutting` |
| F3: Stage by Stage | `training-economics.html#model-funding` |

### Training → Inference/Economics
| Source (training.html) | Target |
|------------------------|--------|
| G2: Tokenizer | `index.html#phase-a` |
| H2: Distributed | `index.html#cross-cutting` |
| H3: Monitoring | `training-economics.html#failure-costs` |

### Training Economics → Other Pages
| Source (training-economics.html) | Target |
|---------------------------------|--------|
| J1: Hardware | `economics.html#cost-stack` |
| J3: Failures | `training.html#monitoring` |
| K3: Cloud vs On-Prem | `economics.html#data-centers` |
| L1: GPU Financing | `economics.html#equity-vs-debt` |

Cross-links use `.cross-link` class for consistent styling.

**Critical**: index.html pipeline steps use `data-step` attributes, **NOT** `id` attributes. Cross-links from economics.html must target phase/section IDs (`#phase-a`, `#phase-b`, `#cross-cutting`), not step IDs. If individual steps need to be linkable from other pages, add explicit `id` attributes to those steps.

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
--economics: #009E73;  /* green (economics page) — light mode: #007A59 */

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
- `.badge-economics` — `$` dollar sign, green (`--economics`)

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
Single breakpoint at `768px`. Key changes: pipeline grid shrinks, badges hidden, phase arrows become vertical. Minimap hidden below `1440px`.

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
   'term-key': { def: 'Definition text.', src: 'https://source-url' },
   ```
2. Use the term inline in HTML:
   ```html
   <span class="term" data-term="term-key">Display Text</span>
   ```

Terms are clickable with tooltip popups. The JS handles `stopPropagation()` to prevent parent container toggles from firing.

### Adding a Cross-Page Link
```html
<a href="economics.html#section-id" class="cross-link">&rarr; Link text</a>
```
Always verify the target `id` exists in the target file before creating the link.

## JavaScript Interaction Patterns

### Three interaction layers (nested, with event isolation)
1. **Pipeline step toggle** — clicking `.step-card` toggles `.active` on `.pipeline-step` (multiple sections can be open simultaneously — no accordion)
2. **Sub-topic toggle** — clicking `.sub-topic-header` toggles `.expanded` on parent `.sub-topic` (via `el.closest('.sub-topic')`). Only the header is clickable, not the card body. Multiple sub-topics can be open simultaneously — no accordion.
3. **Term tooltip** — clicking `.term` shows a definition tooltip

### Critical: Event Propagation
Terms are inside sub-topics which are inside step-cards. Direct `addEventListener` with `stopPropagation()` on each `.term` element prevents parent handlers from firing. Do NOT use event delegation for terms — it causes bubbling bugs.

### Tooltip Toggle Pattern
Track both `activeTooltip` (DOM element) and `activeTermEl` (which term is showing). Clicking the same term dismisses; clicking a different term swaps.

### Interactive Visuals (economics.html)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Cost waterfall | `visual-cost-waterfall` | `initCostWaterfall()` | Stacked horizontal bars + vertical cost stack columns; hover tooltip (fixed-position, appended to body) shows category, value, % of total |
| Deflation timeline | `visual-deflation-timeline` | `initDeflationTimeline()` | Scroll-triggered fill via IntersectionObserver |
| Breakeven calculator | `visual-breakeven-calc` | `initBreakevenCalc()` | Two range sliders (utilization, WACC) update 4 results |
| Capital structure bars | `visual-capital-bars` | `initCapitalBars()` | Click row to toggle detail panel |

**Critical**: The breakeven calculator creates `.term` elements via `innerHTML` at runtime. These miss the initial `querySelectorAll('.term')` event binding loop. Manual event handlers are bound inside `initBreakevenCalc()` after DOM insertion.

## Implementation Methodology for Large Single-File HTML Pages

### The approach that works
```
Phase 1: Research — Read ALL source material before writing any code
Phase 2: Plan — Design information architecture, section IDs, cross-references
Phase 3: CSS first — Write complete <head> with all styles as Part 1
Phase 4: HTML in chunks — Append one phase of content at a time (verify after each)
Phase 5: JS last — Write all interaction handlers, visual initializers as final part
Phase 6: Verify — Tag balance, anchor validity, cross-link targets, browser test
Phase 7: Deploy
```

### Why this order
- CSS first ensures components look right from the first HTML added
- HTML in chunks avoids output token limits (>500 lines per write risks truncation)
- JS last because it references DOM elements that must exist
- Tag balance check after each chunk catches nesting errors early

### Chunk sizing
Each append should be 200-400 lines. For a ~1,700-line file: CSS head (~340 lines), Phase D HTML (~390 lines), Phase E HTML (~290 lines), Phase F + footer (~270 lines), JavaScript (~400 lines).

### Cross-reference protocol
Before creating any cross-page link:
1. Verify the target anchor `id` exists in the target file (`grep 'id="target"' file.html`)
2. index.html steps use `data-step` attributes, NOT `id` — link to phase/section IDs instead
3. After all links are written, extract all `href="otherpage.html#X"` and confirm each `id="X"` exists

## Deployment

```bash
npx wrangler pages deploy ./site --project-name=llm-inference-anatomy
```

No build step needed. The `site/` directory is deployed directly.

### Deployment Verification Checklist
- [ ] All 4 pages load without console errors
- [ ] All 11 inference steps + 9 inference economics + 9 training steps + 9 training economics sections expand/collapse
- [ ] Nav shows 4 links + divider + theme toggle on all pages, `.active` class correct
- [ ] Cross-links navigate to correct sections across all 4 pages
- [ ] Theme toggle works on all 4 pages, persists across navigation
- [ ] Interactive visuals on economics.html: cost waterfall, breakeven sliders, deflation timeline, capital bars
- [ ] All ~100 term tooltips work (~46 inference + ~21 inference-econ + ~30 training + ~3 training-econ) — click show/dismiss
- [ ] Responsive at 768px: nav adapts, minimap hides, content stacks on all pages
- [ ] Light/dark theme: all 4 pages adapt correctly
- [ ] Minimap navigation: click EVERY minimap item on all 4 pages and verify correct section
- [ ] Training page uses amber accent (#E69F00), distinct from inference blue

## WCAG AA Compliance

All text colors must maintain 4.5:1 contrast ratio against their background:
- `--text` (#e8e6e3) on `--bg-card` (#111115): 13.5:1 ✓
- `--text-dim` (#9a98a0) on `--bg-card`: 5.3:1 ✓
- `--accent` (#56B4E9) on `--bg-card`: 7.2:1 ✓
- `--memory` (#0090D6) on `--bg-card`: 5.3:1 ✓ (was #0072B2 at 3.6:1, fixed)
- `--economics` (#009E73) on `--bg-card`: 5.65:1 ✓
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

### Output Token Limits on Large File Generation
AI tools have output limits (~32K tokens). A 1,700-line HTML file exceeds this in a single write. Never attempt to generate >500 lines in one tool call. Plan file creation as sequential appends from the start.

### Anchor ID Mismatches Across Pages
index.html pipeline steps use `data-step="routing"` (no `id` attribute), but cross-links need `id` attributes as targets. Always verify target anchors before creating links. If individual steps need to be linkable from other pages, add explicit `id` attributes to those `.pipeline-step` elements.

### Background Agents for Creative Work
Delegating large creative file generation to a background agent fails due to output limits. For files >500 lines, write sequentially yourself. Use background agents only for research, not large-file generation.

### Dynamic Content Needs Manual Event Binding
Interactive visuals that create DOM elements via `innerHTML` (like the breakeven calculator's WACC term tooltip) miss event listeners from the initial `querySelectorAll` pass. Bind handlers manually after `innerHTML` assignment.

### Stacking Context from Nav Bar
`.site-nav` uses `backdrop-filter: blur(12px)`, which creates a new stacking context. `position: fixed` elements inside it behave differently. Tooltips and minimap are outside the nav to avoid this.

### Theme Sync Across Pages
Both pages use the same `localStorage.getItem('theme')` key and the same theme-init script in `<head>` (before body renders) to prevent FOUC. Any new page must include this exact init pattern.

### Minimap Scroll and Highlight
Two issues with minimap navigation: (1) `scrollIntoView({ block: 'start' })` scrolls behind the nav, and `block: 'center'` fails for bottom-of-page sections. Fix: `window.scrollTo({ top: el.getBoundingClientRect().top + window.scrollY - 64, behavior: 'smooth' })`. (2) The scroll-based minimap highlight observer fires during smooth scroll, detecting the wrong section as active (collapsed sections are short, so the next section's top crosses the threshold). Fix: immediately set the clicked item as active, and use a `scrollLock` timeout (800ms) to suppress the observer during the smooth scroll animation. See index.html's minimap handler for the reference pattern.

### Section Toggle Responsiveness
For pipeline section expansion/collapse, avoid `transition: all` on step interaction elements. Keep transitions targeted and short across all four pages: step dot/card/icon around `0.18s` and `.step-detail` at `max-height 0.18s ease-out`. This prevents the UI from feeling laggy when opening and closing sections repeatedly.

### Search Responsiveness and Coverage
Search should not be limited to section headers. Index section body content (`textContent`) for the current page and lazily enrich with same-origin page fetches for cross-page matches. Use fuzzy scoring (token + subsequence matching), debounce input (~70ms), and delegate result-row events from the container to avoid per-render listener churn and sluggish typing/navigation.

### Knowledge Markdown Export Freshness
The knowledge download should be generated at click time from live page HTML, not from a static repository file. Fetch all four same-origin pages, parse their visible instructional sections, and export a single markdown blob so the downloaded file always tracks the latest deployed website state.

### Interactive Visual Sizing — Avoid Percentage-Based Cell Heights
The prefill attention matrix used `paddingBottom: 100%` to create square grid cells, but with `1fr` columns on wide viewports each cell grew to ~100px tall, overflowing the 260px `.step-visual` container. Fix: compute cell pixel size from available container height (`(availH - gaps) / rows`), then use fixed `width`/`height` in pixels. Always test visuals at multiple viewport widths. Only the prefill visual had this issue — all other visuals use absolute positioning or fixed heights.

### Visual Element Overlap — Check All Positioned Children
The prefill visual's stat label ("Memory-bound · Sequential") overlapped the legend box because both were absolutely positioned in the top-right. When fixing a UI element, audit the same pattern across all pages. For overlapping positioned elements, move one to a different quadrant (e.g., stat below the title on the left, legend stays top-right).

### Radial Layout Overflow — Account for Node Size and Container Padding
The decode loop visual positions 4 nodes in a circle using trigonometry. Nodes use `nodeW = 100` for wider labels. The correct decode cycle is: Compute QKV → Cache + Attend → FFN → Logits → Sample (steps 1-3 repeat per layer). Radius is `Math.min(cx - nodeW/2 - 10, cy - padTop - 10) * 0.52`. Clamp positions to ≥34px from edges. Node coordinates are stored on the step object (`s.cx`, `s.cy`) for packet positioning.

### Hero and Minimap Visibility
Hero uses `min-height: 50vh` (not 100vh) so content is reachable faster. On index.html, minimap is always visible (has `visible` class in HTML, no observer-based hiding). It covers all page sections: pipeline steps (Request Prep, GPU Compute, Output), Observability (Metrics), and Deep Dives (Cross-Cutting Optimizations, Serving Frameworks, Journey). The scroll-spy observes both `data-step` pipeline steps and section-level `id` targets. Other pages (training.html, training-economics.html) use IntersectionObservers; economics.html uses a scroll handler. Minimap phase labels use descriptive names ("Request Prep", "GPU Compute", etc.) not letter codes (A, B, C).

### External Data Sources Integrated
The site now includes concrete pricing and infrastructure data from Fireworks AI and Crusoe Cloud public docs. When updating this data:
- **Fireworks**: Serverless tiers by model size, model-specific pricing (DeepSeek V3/R1, GLM-5, Kimi K2.5), on-demand GPU rates ($5.49/hr H100, $3.19/hr A100), fine-tuning pricing ($0.50-$10/M tokens), DPO at 2× SFT, SOC2/HIPAA/GDPR compliance, FireAttention/LoRA multiplexing
- **Crusoe**: On-demand rates (H200 $4.29, H100 $3.90, A100 $1.95, MI300X $3.45), spot rates (H100 $1.60, A100 $1.30, MI300X $0.95), MemoryAlloy (cluster KV cache via RDMA), facility portfolio (Abilene TX 1.2GW, Iceland, Wyoming, Norway, Argentina), $3.4B Series E + $9.6B infra debt, VPC/InfiniBand/managed K8s/Slurm/AutoClusters, 99.98% uptime SLA
- **Term definitions**: `rdma` added to economics.html termDefs and termStepMap
