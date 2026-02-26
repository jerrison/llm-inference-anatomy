# LLM Anatomy — Gemini Agent Instructions

## Post-Change Workflow (MANDATORY)

After every code change, you MUST complete ALL of these steps — no exceptions:

1. **Commit** the change with a descriptive message
2. **Push** to the remote repository
3. **Deploy** to Cloudflare Pages: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`
4. **Update agent markdown files** (AGENTS.md, CLAUDE.md, GEMINI.md) with any new lessons learned, updated documentation, or testing requirements resulting from the change

Do not wait for the user to ask. Do not skip any step. This applies to every change, every session.

## What This Project Is

A four-page interactive educational site covering LLM training and inference. Two pages explain the technical pipelines (training + inference), two cover business economics (training + inference). All pages use progressive disclosure: hero → phase overview → clickable sections → expandable sub-topics → clickable term definitions.

**Key facts:**
- `site/index.html` (~4,689 lines, inline HTML + CSS + JS) — Inference Technical Pipeline
- `site/economics.html` (~3,189 lines, inline HTML + CSS + JS) — Inference Economics
- `site/training.html` (~2,084 lines, inline HTML + CSS + JS) — Training Technical Pipeline
- `site/training-economics.html` (~1,195 lines, inline HTML + CSS + JS) — Training Economics
- No build step, no framework, no npm dependencies
- Hosted on Cloudflare Pages as `llm-inference-anatomy`
- Live at https://llm-inference-anatomy.pages.dev
- Deploy: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`

### File Structure
```
08-ai-infrastructure-inference/
├── AGENTS.md                        ← Codex agent instructions
├── ai-inference-knowledge-base.md   ← Source research document
├── CLAUDE.md                        ← Claude Code instructions
├── GEMINI.md                        ← Gemini agent instructions (this file)
└── site/
    ├── index.html                   ← Inference Technical Pipeline (~4,689 lines)
    ├── economics.html               ← Inference Economics (~3,189 lines)
    ├── training.html                ← Training Technical Pipeline (~2,084 lines)
    └── training-economics.html      ← Training Economics (~1,195 lines)
```

## Content Architecture

### index.html — Inference Technical Pipeline (11 steps in 3 phases + Metrics)

**Phase A — Request Preparation** (steps 01-04)
| # | Step | Badge | data-step | What It Covers |
|---|------|-------|-----------|----------------|
| 01 | Request Routing | network | `routing` | KV-cache-aware routing, prefill/decode disaggregation, geo-aware routing, gateway frameworks |
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
- Inference Metrics (`id="inference-metrics"`): Latency (TTFT, TPOT, ITL), throughput, resource utilization, SLOs, cost metrics, observability
- Cross-Cutting Optimizations (`id="cross-cutting"`): Quantization (FP8, AWQ, GPTQ, GGUF), Tensor/Pipeline/Expert Parallelism
- Serving Frameworks (`id="frameworks"`): vLLM, SGLang, TensorRT-LLM, NVIDIA Dynamo
- Journey Diagram (`id="journey"`): Interactive end-to-end summary with clickable phase labels

**Important:** Pipeline steps use `data-step` attributes, NOT `id` attributes. Phase dividers and sections use `id` attributes: `phase-a`, `phase-b`, `phase-c`, `inference-metrics`, `cross-cutting`, `frameworks`, `journey`.

### economics.html — Inference Economics (9 sections in 3 phases)

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

### training.html — Training Technical Pipeline (10 sections in 3 phases)

Color accent: `--accent: #E69F00` (Wong amber), distinct from inference blue (`#56B4E9`).

**Phase G — Data & Architecture** (`id="phase-g"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| G1 | Data Pipeline | `data-pipeline` | Collection, filtering, dedup, mixing; data funnel visual |
| G2 | Tokenizer Training | `tokenizer` | BPE, SentencePiece, vocabulary sizes |
| G3 | Model Architecture | `architecture` | Attention variants, SwiGLU, MoE, RoPE |

**Phase H — Training Process** (`id="phase-h"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| H1 | Optimization | `optimization` | AdamW, Muon, WSD, mixed precision; optimizer comparison visual |
| H2 | Distributed Training | `distributed` | ZeRO, TP, PP, 4D parallelism; parallelism diagram visual |
| H3 | Monitoring & Recovery | `monitoring` | Failure stats, checkpointing, MFU |

**Phase I — Post-Training** (`id="phase-i"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| I1 | Supervised Fine-Tuning | `sft` | LoRA, full fine-tuning, PEFT methods |
| I2 | Alignment | `alignment` | RLHF, DPO, GRPO, Constitutional AI, KTO |
| I3 | Reinforcement Fine-Tuning | `rft` | Expert-guided RFT, verifiable rewards, process vs outcome rewards, RFT as product |
| I4 | Evaluation | `evaluation` | MMLU, benchmarks, contamination detection, dynamic evaluation |

### training-economics.html — Training Economics (9 sections in 3 phases)

**Phase J — Training Cost Stack** (`id="phase-j"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| J1 | Hardware & Compute Costs | `hardware-costs` | GPU pricing, cloud rental decline, cost benchmarks; training cost waterfall visual |
| J2 | Scaling Laws & Efficiency | `scaling-laws` | Chinchilla optimal, over-training, inference cost shift; scaling curve visual |
| J3 | Failure & Wasted Compute | `failure-costs` | Llama 3 failures, OPT-175B waste, checkpoint overhead |

**Phase K — Business Models** (`id="phase-k"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| K1 | Build vs Fine-Tune vs API | `build-vs-buy` | Foundation model vs fine-tuning vs API; cost calculator visual |
| K2 | Training Providers | `training-providers` | Foundation model companies, training-as-a-service |
| K3 | Cloud vs On-Premise | `cloud-vs-onprem` | Cloud advantages, on-prem breakeven, hidden costs |

**Phase L — Capital & Market** (`id="phase-l"`)
| # | Section | ID | What It Covers |
|---|---------|-----|----------------|
| L1 | GPU Financing | `gpu-financing` | CoreWeave debt, Lambda sale-leaseback, GPU depreciation |
| L2 | Foundation Model Funding | `model-funding` | Mega-rounds, Anthropic/OpenAI, Big Tech infrastructure spend |
| L3 | Training vs Inference Spend | `training-vs-inference` | Spend ratio shift 2023-2026, revenue gap; spend timeline visual |

### Each Step/Section Contains
1. **Summary** (always visible) — one-line description
2. **Callout: Key Takeaway** — bold insight sentence (accent left border)
3. **Callout: Think of it like...** — real-world analogy (pink left border)
4. **Detail sections** — deeper explanations
5. **Sub-topics** — expandable cards with tables, metrics, code blocks
6. **Term references** — clickable inline terms that show tooltip definitions

## Shared Navigation System

All four pages have the same `<nav class="site-nav">`:
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

The `active` class moves to whichever page is current. The `nav-divider` is a 1px vertical line separating training from inference links. The theme toggle is inside the nav (not floating).

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
- `scrollLock` timeout (800ms) suppresses IntersectionObserver during smooth scroll

## Cross-Link System

### Inference Technical → Inference Economics (4 content links + nav)
| Source in index.html | Target in economics.html |
|---------------------|------------------------|
| Step 09 (decode) | `economics.html#throughput` |
| Step 11 (streaming) | `economics.html#pricing` |
| Cross-cutting (optimization) | `economics.html#throughput` |
| Cross-cutting (networking) | `economics.html#cost-stack` |

### Inference Technical → Training (3 content links)
| Source in index.html | Target in training.html |
|---------------------|------------------------|
| Step 03 (Tokenization) | `training.html#tokenizer` |
| Step 04 (Embedding) | `training.html#architecture` |
| Cross-cutting (Parallelism) | `training.html#distributed` |

### Inference Economics → Inference Technical (6 content links + nav)
| Source in economics.html | Target in index.html |
|------------------------|---------------------|
| D1: Cost Stack | `index.html#cross-cutting` |
| D2: Throughput (optimization) | `index.html#cross-cutting` |
| D2: Throughput (batching) | `index.html#phase-b` |
| D3: Pricing | `index.html#phase-a` |
| E1: Managed vs Rental | `index.html#phase-b` |
| E3: Data Centers | `index.html#cross-cutting` |

### Inference Economics → Training Economics (2 content links)
| Source in economics.html | Target |
|------------------------|--------|
| D1: Cost Stack (networking) | `training-economics.html#hardware-costs` |
| F3: Stage by Stage | `training-economics.html#model-funding` |

### Training → Inference/Economics (3 content links)
| Source in training.html | Target |
|------------------------|--------|
| G2: Tokenizer | `index.html#phase-a` |
| H2: Distributed | `index.html#cross-cutting` |
| H3: Monitoring | `training-economics.html#failure-costs` |

### Training Economics → Other Pages (4 content links)
| Source in training-economics.html | Target |
|---------------------------------|--------|
| J1: Hardware | `economics.html#cost-stack` |
| J3: Failures | `training.html#monitoring` |
| K3: Cloud vs On-Prem | `economics.html#data-centers` |
| L1: GPU Financing | `economics.html#equity-vs-debt` |

Cross-links use `.cross-link` class. **Critical:** index.html steps use `data-step` (no `id`). Cross-links must target phase/section IDs, not step IDs.

## Interactive Visuals

### economics.html (4 visuals)
| Visual | Container ID | Init Function | Interaction |
|--------|-------------|---------------|-------------|
| Cost waterfall chart | `visual-cost-waterfall` | `initCostWaterfall()` | Stacked horizontal bars + vertical cost stack columns; hover tooltip (fixed-position, appended to body) shows category, value, % of total |
| Deflation timeline | `visual-deflation-timeline` | `initDeflationTimeline()` | Scroll-triggered fill animation via IntersectionObserver |
| Breakeven calculator | `visual-breakeven-calc` | `initBreakevenCalc()` | Two range sliders (utilization %, WACC %) update 4 result values in real time |
| Capital structure bars | `visual-capital-bars` | `initCapitalBars()` | Click funding stage row to toggle detail panel; 5 stages (Series A → Public) |

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

**Note:** The breakeven calculator (economics.html) creates `.term` elements via `innerHTML` at runtime — these require manual event handler binding inside `initBreakevenCalc()` since they miss the initial `querySelectorAll('.term')` loop.

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

### Adding a New Pipeline Step / Section

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

Update the phase overview cards and minimap to reflect any new section.

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

**index.html (~46 terms):**
`alibi`, `arithmetic-intensity`, `autoregressive`, `awq`, `bpe`, `chunked-prefill`, `cold-start`, `continuous-batching`, `decode`, `disaggregated-serving`, `eos`, `ep`, `flashattention`, `fp16`, `fp8`, `fsm-decoding`, `gguf`, `gptq`, `gqa`, `hbm`, `infiniband`, `int4`, `kv-cache`, `logits`, `lora`, `mha`, `min-p`, `mla`, `moe`, `mqa`, `nvlink`, `pagedattention`, `pp`, `prefill`, `prefix-caching`, `rdma`, `rope`, `softmax`, `speculative-decoding`, `sram`, `sse`, `top-k`, `top-p`, `tp`, `tpot`, `ttft`

**economics.html (~21 terms):**
`barbell-strategy`, `capex`, `dscr`, `fixed-rate-swap`, `gross-margin`, `interleaved-thinking`, `opex`, `preserved-thinking`, `pue`, `rdma`, `reasoning-content`, `reasoning-effort`, `reasoning-history`, `residual-value`, `revenue-per-watt`, `sofr`, `statistical-multiplexing`, `tco`, `thinking-budget`, `utilization-breakeven`, `wacc`

**training.html (~30 terms):**
`bpe`, `sentencepiece`, `minhash`, `rmsnorm`, `swiglu`, `gqa`, `mla`, `rope`, `moe`, `adamw`, `muon`, `wsd`, `fsdp`, `zero`, `tp`, `pp`, `dpo`, `grpo`, `rlhf`, `constitutional-ai`, `kto`, `mmlu`, `mfu`, `rft`, `prm`, and more

**training-economics.html (~3 terms):**
`chinchilla`, `mfu`, `lora`

## JavaScript Interaction Model

### Three Nested Click Layers
The site has three levels of clickable elements, nested inside each other:

```
.pipeline-step  →  click .step-card to toggle .active (multiple can be open, no accordion)
  └─ .sub-topic-header  →  onclick="toggleSubTopic(this)" toggles parent .sub-topic (multiple can be open, no accordion)
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
index.html pipeline steps use `data-step="routing"` (no `id`). Cross-links from other pages that target `index.html#step-01` would break. Always verify target anchors exist. Link to phase/section IDs (`#phase-a`, `#phase-b`, `#cross-cutting`, `#inference-metrics`).

### 8. Background Agents for Creative Work
Delegating large creative file generation to a background agent fails due to output limits. For files >500 lines, write sequentially yourself. Use agents only for research, not large-file generation.

### 9. Dynamic Content Needs Manual Event Binding
Interactive visuals that create DOM elements via `innerHTML` (like the breakeven calculator's WACC term) miss event listeners from the initial `querySelectorAll` pass. Bind handlers manually after `innerHTML` assignment.

### 10. Stacking Context from Nav Bar
`.site-nav` uses `backdrop-filter: blur(12px)`, creating a new stacking context. `position: fixed` elements inside it behave differently. Tooltips and minimap are placed outside the nav.

### 11. Theme Sync Across Pages
All four pages use the same `localStorage` key `'theme'` and the same init script in `<head>` (before body renders). Any new page must include this exact pattern to prevent FOUC and maintain theme continuity.

### 12. Minimap Scroll and Highlight
Two issues with minimap navigation: (1) `scrollIntoView` with `block: 'start'` scrolls behind the nav, and `block: 'center'` fails for bottom-of-page sections. Fix: `window.scrollTo({ top: el.getBoundingClientRect().top + window.scrollY - 64, behavior: 'smooth' })`. (2) The scroll-based minimap highlight observer fires during smooth scroll, detecting the wrong section as active (collapsed sections are short, so the next section's top crosses the threshold). Fix: immediately set the clicked item as active, and use a `scrollLock` timeout (800ms) to suppress the observer during the smooth scroll animation.

### 13. Minimap Collapse by Default
Minimap labels must be hidden at base CSS level (`opacity: 0; width: 0`), not inside a media query. Revealed via `.minimap:hover .minimap-label`. Previously, labels were inside `@media (max-width: 1440px)` which left them always visible on wider screens.

### 14. Section Toggle Responsiveness
For fast open/close behavior, avoid broad `transition: all` on pipeline step elements. Use targeted transitions around `0.18s` for `.step-dot`, `.step-card`, `.step-card::before`, and `.step-expand-icon`, and keep `.step-detail` at `max-height 0.18s ease-out` across all four pages.

### 15. Search Responsiveness and Full-Content Matching
Search must support fuzzy matching and body-content discovery, not just titles/descriptions. Build index text from section `textContent`, lazily fetch same-origin pages to enrich cross-page corpus, debounce input, and use event delegation for search result interactions. Avoid expensive search-overlay blur effects that cause interface jank.

### 16. Knowledge Markdown Export Freshness
Do not rely on a static markdown snapshot for site knowledge. Generate markdown dynamically from the live HTML of all four pages at download time so exported knowledge stays aligned with the latest deployed content.

### 17. Interactive Visual Sizing — Avoid Percentage-Based Cell Heights
The prefill attention matrix used `paddingBottom: 100%` for square cells, but `1fr` columns on wide viewports made cells ~100px tall, overflowing the 260px `.step-visual` container. Fix: compute cell pixel size from available container height. Always test visuals at multiple viewport widths.

### 18. Visual Element Overlap — Check All Positioned Children
The prefill stat label overlapped the legend because both were positioned top-right. When fixing a UI element, audit the same pattern across all pages. For overlapping positioned elements, move one to a different quadrant.

### 19. Radial Layout Overflow — Account for Node Dimensions
The decode loop visual positions nodes in a circle using trigonometry. The original center was shifted upward, pushing the top node into the container border. Always account for node width/height plus border/padding when computing the radius — the node center is not its visual edge. Add a top padding buffer, shift the circle center down, and clamp node positions to stay >=20px from container edges.

### 20. Hero and Minimap Visibility
Hero uses `min-height: 50vh` (not 100vh) so content is reachable faster. Minimap is visible from page load via Set-based IntersectionObserver on both `.hero` and `#pipeline` — hides only in footer area when neither is in viewport. Minimap phase labels use descriptive names ("Request Prep", "GPU Compute", "Unit Economics", etc.) not letter codes (A, B, C).

### 21. External Data Sources Integrated
The site includes concrete pricing and infrastructure data from Fireworks AI and Crusoe Cloud public docs. Key data: Fireworks serverless tiers, model-specific pricing (DeepSeek V3/R1, GLM-5, Kimi K2.5), on-demand GPU rates, fine-tuning pricing ($0.50-$10/M tokens), SOC2/HIPAA/GDPR compliance, FireAttention/LoRA multiplexing. Crusoe: on-demand/spot GPU rates, MemoryAlloy (cluster KV cache via RDMA), facility portfolio (Abilene TX 1.2GW, Iceland, Wyoming, Norway, Argentina), $3.4B Series E + $9.6B infra debt. The `rdma` term is defined in both index.html and economics.html termDefs.

## Deployment Verification

After deploying, verify:
1. All 4 pages load without console errors
2. All 11 inference steps + 10 training sections + 9 inference economics + 9 training economics sections expand/collapse
3. Nav shows 4 links + divider + theme toggle on all pages, `.active` class correct
4. Cross-links navigate to correct sections across all 4 pages
5. Theme toggle works on all 4 pages, persists across navigation
6. Interactive visuals on economics.html: cost waterfall hover, breakeven sliders work, deflation timeline animates, capital bars expand
7. Interactive visuals on training.html: data funnel animates, optimizer bars render, parallelism grid toggles modes
8. Interactive visuals on training-economics.html: training waterfall renders, scaling curve bars, cost calculator sliders, spend timeline bars
9. All ~100 term tooltips work (~46 inference + ~21 inference-econ + ~30 training + ~3 training-econ) — click show/dismiss
10. Sub-topics within sections toggle independently (no accordion — multiple can be open)
11. Phase overview cards scroll to correct anchors on all pages
12. Responsive at 768px: nav adapts, minimap hides, content stacks on all pages
13. Light/dark theme: all 4 pages render correctly in both modes
14. Minimap navigation: click EVERY minimap item on all 4 pages and verify correct section header visible below nav bar
15. Training page uses amber accent (#E69F00), distinct from inference blue
16. No console errors on any page
