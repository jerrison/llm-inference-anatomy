# LLM Inference Anatomy — Gemini Agent Instructions

## Post-Change Workflow (MANDATORY)

After every code change, you MUST complete ALL of these steps — no exceptions:

1. **Commit** the change with a descriptive message
2. **Push** to the remote repository
3. **Deploy** to Cloudflare Pages: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`
4. **Update agent markdown files** (AGENTS.md, CLAUDE.md, GEMINI.md) with any new lessons learned, updated documentation, or testing requirements resulting from the change

Do not wait for the user to ask. Do not skip any step. This applies to every change, every session.

## What This Project Is

A two-page interactive educational site that explains every stage of LLM inference — from HTTP request to streamed token response (Technical Pipeline) — and the business economics behind it (Economics). Both pages use progressive disclosure: hero → phase overview → clickable sections → expandable sub-topics → clickable term definitions.

**Key facts:**
- `site/index.html` (~4,246 lines, inline HTML + CSS + JS) — Technical Pipeline
- `site/economics.html` (~1,694 lines, inline HTML + CSS + JS) — Business Economics
- No build step, no framework, no npm dependencies
- Hosted on Cloudflare Pages as `llm-inference-anatomy`
- Live at https://llm-inference-anatomy.pages.dev
- Deploy: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`

### File Structure
```
05-ai-infrastructure-inference/
├── AGENTS.md                        ← Codex agent instructions
├── ai-inference-knowledge-base.md   ← Source research document
├── CLAUDE.md                        ← Claude Code instructions
├── GEMINI.md                        ← Gemini agent instructions (this file)
└── site/
    ├── index.html                   ← Technical Pipeline (~4,246 lines)
    └── economics.html               ← Business Economics (~1,694 lines)
```

## Content Architecture

### index.html — Technical Pipeline (11 steps in 3 phases)

**Phase A — Request Preparation** (steps 01-04)
| # | Step | Badge | data-step | What It Covers |
|---|------|-------|-----------|----------------|
| 01 | Request Routing | network | `routing` | KV-cache-aware routing, prefill/decode disaggregation, gateway frameworks |
| 02 | Preprocessing | logic | `preprocessing` | Prompt templates, RAG retrieval, rate limiting, input validation |
| 03 | Tokenization | logic | `tokenization` | BPE, SentencePiece, vocabulary sizes, tiktoken, 2025 innovations |
| 04 | Embedding & Position | memory | `embedding` | Token embeddings, RoPE, ALiBi, Per-Layer Embeddings |

**Phase B — GPU Computation** (steps 05-09)
| # | Step | Badge | data-step | What It Covers |
|---|------|-------|-----------|----------------|
| 05 | Scheduling & Batching | logic | `scheduling` | Continuous batching, static batching, chunked prefill |
| 06 | Prefill Phase | compute | `prefill` | Parallel forward pass, TTFT, prefix caching, KV-Runahead |
| 07 | KV Cache & Memory | memory | `kvcache` | PagedAttention, KV compression, MLA, memory formula |
| 08 | Attention Mechanisms | compute | `attention` | MHA→GQA→MQA evolution, FlashAttention, MLA |
| 09 | Decode Phase | memory | `decode` | Autoregressive loop, memory-bandwidth bound, speculative decoding |

**Phase C — Output & Delivery** (steps 10-11)
| # | Step | Badge | data-step | What It Covers |
|---|------|-------|-----------|----------------|
| 10 | Sampling & Selection | logic | `sampling` | Temperature, top-k/top-p/min-p, repetition penalties, top-n-sigma |
| 11 | Detokenization & Streaming | io | `streaming` | UTF-8 buffering, SSE protocol, postprocessing pipeline |

**Separate sections (not pipeline steps):**
- Cross-Cutting Optimizations (`id="cross-cutting"`): Quantization (FP8, AWQ, GPTQ, GGUF), Tensor/Pipeline/Expert Parallelism
- Serving Frameworks (`id="frameworks"`): vLLM, SGLang, TensorRT-LLM, NVIDIA Dynamo
- Journey Diagram (`id="journey"`): Interactive end-to-end summary with clickable phase labels

**Important:** Pipeline steps use `data-step` attributes, NOT `id` attributes. Phase dividers and sections use `id` attributes: `phase-a`, `phase-b`, `phase-c`, `cross-cutting`, `frameworks`, `journey`.

### economics.html — Business Economics (9 sections in 3 phases)

**Phase D — Unit Economics** (`id="phase-d"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| D1 | The Cost Stack | `cost-stack` | GPU CapEx, power, data center, networking, operations; cost waterfall visual |
| D2 | Throughput as Margin | `throughput` | 3-5× throughput variance from optimization; batch vs real-time economics |
| D3 | Pricing Structures | `pricing` | Per-token, per-GPU-hour, reserved, batch, value-based; deflation timeline visual |

**Phase E — Business Models** (`id="phase-e"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| E1 | Managed vs GPU Rental | `managed-vs-rental` | Outcomes vs infrastructure; statistical multiplexing |
| E2 | Buy vs Rent GPUs | `buy-vs-rent` | Ownership cost model; breakeven calculator with sliders |
| E3 | Data Centers | `data-centers` | Power costs, PUE, facility location economics |

**Phase F — Capital Structure** (`id="phase-f"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| F1 | Equity vs Debt | `equity-vs-debt` | Risk, information asymmetry, tax shields |
| F2 | Contracted Revenue | `contracted-revenue` | How pricing decisions unlock debt capacity; DSCR |
| F3 | Stage-by-Stage | `stage-by-stage` | Series A → Public capital evolution; capital bars visual |

### Each Step/Section Contains
1. **Summary** (always visible) — one-line description
2. **Callout: Key Takeaway** — bold insight sentence (accent left border)
3. **Callout: Think of it like...** — real-world analogy (pink left border)
4. **Detail sections** — deeper explanations
5. **Sub-topics** — expandable cards with tables, metrics, code blocks
6. **Term references** — clickable inline terms that show tooltip definitions

## Shared Navigation System

Both pages have the same `<nav class="site-nav">`:
```html
<nav class="site-nav">
  <a href="index.html" class="nav-logo">LLM Inference Anatomy</a>
  <div class="nav-links">
    <a href="index.html" class="nav-link active">Technical Pipeline</a>
    <a href="economics.html" class="nav-link">Economics</a>
    <button class="theme-toggle" id="theme-toggle" aria-label="Toggle theme"></button>
  </div>
</nav>
```

The `active` class moves to whichever page is current. The theme toggle is inside the nav (not floating).

### Theme System
- **Init script** runs in `<head>` before body renders (prevents FOUC)
- Reads `localStorage.getItem('theme')`, falls back to `prefers-color-scheme`
- Sets `data-theme` attribute on `<html>` element
- Both pages share the same `localStorage` key `'theme'`
- Any new page must include the exact same init pattern

## Cross-Link System

### Technical → Economics (4 content links + nav)
| Source in index.html | Target in economics.html |
|---------------------|------------------------|
| Step 09 (decode) | `economics.html#throughput` |
| Step 11 (streaming) | `economics.html#pricing` |
| Cross-cutting (optimization) | `economics.html#throughput` |
| Cross-cutting (networking) | `economics.html#cost-stack` |

### Economics → Technical (6 content links + nav)
| Source in economics.html | Target in index.html |
|------------------------|---------------------|
| D1: Cost Stack | `index.html#cross-cutting` |
| D2: Throughput (optimization) | `index.html#cross-cutting` |
| D2: Throughput (batching) | `index.html#phase-b` |
| D3: Pricing | `index.html#phase-a` |
| E1: Managed vs Rental | `index.html#phase-b` |
| E3: Data Centers | `index.html#cross-cutting` |

Cross-links use `.cross-link` class. **Critical:** index.html steps use `data-step` (no `id`). Cross-links must target phase/section IDs, not step IDs.

## Interactive Visuals (economics.html)

| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Cost waterfall chart | `visual-cost-waterfall` | `initCostWaterfall()` | Hover segments for Crusoe vs CoreWeave cost breakdown |
| Deflation timeline | `visual-deflation-timeline` | `initDeflationTimeline()` | Scroll-triggered fill animation via IntersectionObserver |
| Breakeven calculator | `visual-breakeven-calc` | `initBreakevenCalc()` | Two range sliders (utilization %, WACC %) update 4 result values in real time |
| Capital structure bars | `visual-capital-bars` | `initCapitalBars()` | Click funding stage row to toggle detail panel; 5 stages (Series A → Public) |

**Note:** The breakeven calculator creates `.term` elements via `innerHTML` at runtime — these require manual event handler binding inside `initBreakevenCalc()` since they miss the initial `querySelectorAll('.term')` loop.

## Design System Reference

### Color Palette
```
Backgrounds:   #0a0a0c (page)  #111115 (cards)  #18181e (hover)
Borders:       #1e1e28 (default)  #3a3a50 (active)
Text:          #e8e6e3 (primary)  #9a98a0 (secondary)  #4a4850 (muted/decorative)
Accent:        #56B4E9 (sky blue)  #3a8dc4 (dimmed)

Category badges (Wong colorblind-safe palette):
  Compute:    #D55E00 (vermillion)  icon: ⚡
  Memory:     #0090D6 (blue)        icon: ☰
  IO:         #E69F00 (amber)       icon: ⇄
  Logic:      #CC79A7 (pink)        icon: ⚙
  Network:    #56B4E9 (sky blue)    icon: ◎
  Economics:  #009E73 (green)       icon: $    (light mode: #007A59)
```

### Fonts
- **Headings**: Instrument Serif (Google Fonts) — weight 400, italic for emphasis
- **Body**: Atkinson Hyperlegible (Google Fonts) — weight 400, line-height 1.8
- **Code/labels**: DM Mono (Google Fonts)

### Accessibility Requirements
- Body text: minimum 4.5:1 contrast ratio against card background (#111115)
- Badge icons provide non-color differentiation (symbols via CSS `::before`)
- All interactive elements are keyboard-accessible via natural tab order
- Previously fixed: `--memory` was #0072B2 (3.6:1 FAIL) → brightened to #0090D6 (5.3:1 PASS)
- `--economics` (#009E73) achieves 5.65:1 on #111115 — PASS

## How to Make Changes

### Adding a New Pipeline Step / Economics Section

Insert within the appropriate phase. Follow this template:

```html
<div class="pipeline-step reveal" data-step="unique-id">
  <div class="step-marker"><div class="step-dot"></div></div>
  <div class="step-card">
    <div class="step-header">
      <span class="step-number">NN</span>
      <span class="step-name">Step Name</span>
      <span class="step-badge badge-CATEGORY">Category</span>
      <span class="step-expand-icon">+</span>
    </div>
    <div class="step-summary">Summary with optional <span class="term" data-term="key">Term</span>.</div>
    <div class="step-detail">
      <div class="detail-inner">
        <div class="callout-box callout-takeaway">
          <div class="callout-label">Key Takeaway</div>
          <p>Insight.</p>
        </div>
        <div class="callout-box callout-analogy">
          <div class="callout-label">Think of it like...</div>
          <p>Analogy.</p>
        </div>
        <div class="detail-section">
          <div class="detail-label">Heading</div>
          <div class="detail-text">Content.</div>
        </div>
      </div>
    </div>
  </div>
</div>
```

Update the phase overview cards and (for index.html) journey diagram to reflect any new step.

### Adding a Technical Term Definition

Two steps — both required:

**Step 1**: Add to `termDefs` object in `<script>`:
```js
'term-key': { def: 'Definition text.', src: 'https://source-url' },
```

**Step 2**: Use the term inline in HTML:
```html
<span class="term" data-term="term-key">Display Text</span>
```

The tooltip system handles everything else automatically.

### Adding a Cross-Page Link

```html
<a href="economics.html#section-id" class="cross-link">&rarr; Link text</a>
```
Always verify the target `id` exists in the target file before creating.

### Term Definitions Reference

**index.html (46 terms):**
`alibi`, `arithmetic-intensity`, `autoregressive`, `awq`, `bpe`, `chunked-prefill`, `cold-start`, `continuous-batching`, `decode`, `disaggregated-serving`, `eos`, `ep`, `flashattention`, `fp16`, `fp8`, `fsm-decoding`, `gguf`, `gptq`, `gqa`, `hbm`, `infiniband`, `int4`, `kv-cache`, `logits`, `lora`, `mha`, `min-p`, `mla`, `moe`, `mqa`, `nvlink`, `pagedattention`, `pp`, `prefill`, `prefix-caching`, `rdma`, `rope`, `softmax`, `speculative-decoding`, `sram`, `sse`, `top-k`, `top-p`, `tp`, `tpot`, `ttft`

**economics.html (12 terms):**
`capex`, `dscr`, `fixed-rate-swap`, `gross-margin`, `opex`, `pue`, `residual-value`, `sofr`, `statistical-multiplexing`, `tco`, `utilization-breakeven`, `wacc`

## JavaScript Interaction Model

### Three Nested Click Layers
The site has three levels of clickable elements, nested inside each other:

```
.pipeline-step  →  click .step-card to toggle .active (shows/hides detail, multiple can be open)
  └─ .sub-topic  →  onclick="toggleSubTopic(this)" (accordion within step)
       └─ .term  →  direct addEventListener with stopPropagation (tooltip)
```

### Critical Rule: Event Isolation
Each inner layer must prevent its click from triggering outer layers:
- `.term` handlers call `e.stopPropagation()` and `e.preventDefault()`
- `.step-card` handler checks `if (e.target.closest('.sub-topic')) return;` and `if (e.target.closest('.term')) return;`
- Sub-topics use inline `onclick` which doesn't propagate to step-card because of the `.closest()` guard

**Why not event delegation?** Using a document-level click handler with delegation fails here because the event bubbles through parent handlers (sub-topic, step-card) _before_ reaching the document. By the time delegation fires, parents have already toggled. Direct handlers with `stopPropagation()` at the source are the only reliable approach.

### Tooltip State Machine
```
States: CLOSED | OPEN(term)
CLOSED + click(term) → OPEN(term)      [create tooltip]
OPEN(A) + click(A)   → CLOSED          [remove tooltip — toggle off]
OPEN(A) + click(B)   → OPEN(B)         [swap tooltip]
OPEN(A) + click(bg)  → CLOSED          [dismiss]
```

Tracked with two variables: `activeTooltip` (DOM node) and `activeTermEl` (which term). Both must be set/cleared together.

### Scroll Reveal
`IntersectionObserver` with threshold 0.1 adds `.visible` class to `.reveal` elements. One-directional — elements don't un-reveal on scroll-out.

## Implementation Methodology for Large Single-File Pages

### The approach that works
```
Phase 1: Research — Read ALL source material before writing any code
Phase 2: Plan — Design information architecture, section IDs, cross-references
Phase 3: CSS first — Write complete <head> with all styles
Phase 4: HTML in chunks — Append one phase at a time (200-400 lines each, verify after each)
Phase 5: JS last — Write all interaction handlers, visual initializers as final part
Phase 6: Verify — Tag balance, anchor validity, cross-link targets, browser test
Phase 7: Deploy
```

### Why this order
- CSS first ensures components look right from the first HTML added
- HTML in chunks avoids output token limits (>500 lines per write risks truncation)
- JS last because it references DOM elements that must exist
- Tag balance check after each chunk catches nesting errors early

### Cross-reference verification protocol
Before creating any cross-page link:
1. `grep 'id="target"' file.html` to verify the anchor exists
2. index.html steps use `data-step`, NOT `id` — link to phase/section IDs instead
3. After all links are written, extract all `href="otherpage.html#X"` and confirm each `id="X"` exists

## Pitfalls and Lessons Learned

### 1. WCAG Contrast on Dark Backgrounds
The Wong colorblind-safe palette was designed for white backgrounds. On dark backgrounds (#111115), some colors fail WCAG AA 4.5:1. Always verify.

### 2. Tooltip Positioning and Stacking Contexts
Tooltips are appended to `document.body` (not inside the clicked element) to avoid stacking context issues from `transform`, `backdrop-filter`, etc. on parent elements. Position is calculated from `getBoundingClientRect()` with edge-clamping.

### 3. HTML Entities in Single-File HTML
All `<`, `>`, `&` in visible text must be entity-encoded. Mathematical formulas use entities: `&times;`, `&radic;`, `&sup2;`, `&rarr;`, `&mdash;`. Forgetting this causes silent parse errors where content disappears.

### 4. Badge Icon Method
Icons are CSS `::before` content using Unicode characters, not SVGs or icon fonts. This keeps the single-file approach clean. When adding badges, use characters that render well across platforms.

### 5. No Unnecessary Abstractions
This is intentionally simple. Don't add a build step, CSS preprocessor, JS framework, separate CSS/JS files, or a component system. The single-file approach is a feature, not a limitation.

### 6. Output Token Limits on Large File Generation
AI tools have output limits (~32K tokens). A 1,700-line HTML file exceeds this in a single write. Never attempt to generate >500 lines in one tool call. Plan file creation as sequential appends from the start.

### 7. Anchor ID Mismatches Across Pages
index.html pipeline steps use `data-step="routing"` (no `id`). Cross-links from economics.html that targeted `index.html#step-01` would break. Always verify target anchors exist. Link to phase/section IDs (`#phase-a`, `#phase-b`, `#cross-cutting`).

### 8. Background Agents for Creative Work
Delegating large creative file generation to a background agent fails due to output limits. For files >500 lines, write sequentially yourself. Use agents only for research, not large-file generation.

### 9. Dynamic Content Needs Manual Event Binding
Interactive visuals that create DOM elements via `innerHTML` (like the breakeven calculator's WACC term) miss event listeners from the initial `querySelectorAll` pass. Bind handlers manually after `innerHTML` assignment.

### 10. Stacking Context from Nav Bar
`.site-nav` uses `backdrop-filter: blur(12px)`, creating a new stacking context. `position: fixed` elements inside it behave differently. Tooltips and minimap are placed outside the nav.

### 11. Theme Sync Across Pages
Both pages use the same `localStorage` key `'theme'` and the same init script in `<head>` (before body renders). Any new page must include this exact pattern to prevent FOUC and maintain theme continuity.

### 12. Minimap Scroll and Highlight
Two issues with minimap navigation: (1) `scrollIntoView` with `block: 'start'` scrolls behind the nav, and `block: 'center'` fails for bottom-of-page sections. Fix: `window.scrollTo({ top: el.getBoundingClientRect().top + window.scrollY - 64, behavior: 'smooth' })`. (2) The scroll-based minimap highlight observer fires during smooth scroll, detecting the wrong section as active (collapsed sections are short, so the next section's top crosses the threshold). Fix: immediately set the clicked item as active, and use a `scrollLock` timeout (800ms) to suppress the observer during the smooth scroll animation.

## Deployment Verification

After deploying, verify:
1. All 11 technical steps + 9 economics sections expand/collapse correctly
2. Nav bar links work bidirectionally (Technical Pipeline ↔ Economics)
3. Cross-links navigate to correct sections on the other page
4. Theme toggle works on both pages, persists across navigation
5. Interactive visuals: cost waterfall hover, breakeven sliders work, deflation timeline animates, capital bars expand
6. All ~58 term tooltips work (46 technical + 12 economics) — click show/dismiss
7. Sub-topics within steps work as accordions
8. Phase overview cards scroll to correct anchors on both pages
9. Responsive at 768px: nav adapts, minimap hides, content stacks
10. Light/dark theme: both pages render correctly in both modes
11. Minimap navigation: click EVERY minimap item on both pages and verify the correct section header is visible below the nav bar — especially sections near the bottom of the page (e.g., Data Centers, Contracted Revenue, Stage by Stage on economics; Sampling, Streaming on index). The minimap dot must highlight the clicked section, not a neighboring one.
12. No console errors on either page
