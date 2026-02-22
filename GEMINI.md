# LLM Inference Anatomy — Gemini Agent Instructions

## What This Project Is

A single-page interactive educational site that explains every stage of LLM inference — from HTTP request to streamed token response. It uses progressive disclosure: hero → phase overview → 11 clickable pipeline steps → expandable sub-topics → clickable term definitions.

**Key facts:**
- Everything is in `site/index.html` (~1965 lines, inline HTML + CSS + JS)
- No build step, no framework, no npm dependencies
- Hosted on Cloudflare Pages as `llm-inference-anatomy`
- Live at https://llm-inference-anatomy.pages.dev
- Deploy: `npx wrangler pages deploy ./site --project-name=llm-inference-anatomy`

## Content Architecture

### Pipeline Steps (11 steps in 3 phases)

**Phase A — Request Preparation** (steps 01-04)
| # | Step | Badge | What It Covers |
|---|------|-------|----------------|
| 01 | Request Routing | network | KV-cache-aware routing, prefill/decode disaggregation, gateway frameworks |
| 02 | Preprocessing | logic | Prompt templates, RAG retrieval, rate limiting, input validation |
| 03 | Tokenization | logic | BPE, SentencePiece, vocabulary sizes, tiktoken, 2025 innovations |
| 04 | Embedding & Position | memory | Token embeddings, RoPE, ALiBi, Per-Layer Embeddings |

**Phase B — GPU Computation** (steps 05-09)
| # | Step | Badge | What It Covers |
|---|------|-------|----------------|
| 05 | Scheduling & Batching | logic | Continuous batching, static batching, chunked prefill |
| 06 | Prefill Phase | compute | Parallel forward pass, TTFT, prefix caching, KV-Runahead |
| 07 | KV Cache & Memory | memory | PagedAttention, KV compression, MLA, memory formula |
| 08 | Attention Mechanisms | compute | MHA→GQA→MQA evolution, FlashAttention, MLA |
| 09 | Decode Phase | memory | Autoregressive loop, memory-bandwidth bound, speculative decoding |

**Phase C — Output & Delivery** (steps 10-11)
| # | Step | Badge | What It Covers |
|---|------|-------|----------------|
| 10 | Sampling & Selection | logic | Temperature, top-k/top-p/min-p, repetition penalties, top-n-sigma |
| 11 | Detokenization & Streaming | io | UTF-8 buffering, SSE protocol, postprocessing pipeline |

**Separate sections (not pipeline steps):**
- Cross-Cutting Optimizations: Quantization (FP8, AWQ, GPTQ, GGUF), Tensor/Pipeline/Expert Parallelism
- Serving Frameworks: vLLM, SGLang, TensorRT-LLM, NVIDIA Dynamo

### Each Step Contains
1. **Summary** (always visible) — one-line description
2. **Callout: Key Takeaway** — bold insight sentence (blue left border)
3. **Callout: Think of it like...** — real-world analogy (pink left border)
4. **Detail sections** — deeper explanations
5. **Sub-topics** — expandable cards with even deeper content (tables, metrics, code blocks)
6. **Term references** — clickable inline terms that show tooltip definitions

## Design System Reference

### Color Palette
```
Backgrounds:   #0a0a0c (page)  #111115 (cards)  #18181e (hover)
Borders:        #1e1e28 (default)  #3a3a50 (active)
Text:           #e8e6e3 (primary)  #9a98a0 (secondary)  #4a4850 (muted/decorative)
Accent:         #56B4E9 (sky blue)  #3a8dc4 (dimmed)

Category badges (Wong colorblind-safe palette):
  Compute:  #D55E00 (vermillion)  icon: ⚡
  Memory:   #0090D6 (blue)        icon: ☰
  IO:       #E69F00 (amber)       icon: ⇄
  Logic:    #CC79A7 (pink)        icon: ⚙
  Network:  #56B4E9 (sky blue)    icon: ◎
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

## How to Make Changes

### Adding a New Pipeline Step

Insert within the appropriate phase in `.pipeline-flow`. Follow this template:

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

Update the phase overview cards and journey diagram to reflect the new step.

### Adding a Technical Term Definition

Two steps — both required:

**Step 1**: Add to `termDefs` object (around line 1868):
```js
'term-key': 'Plain text definition. Use Unicode escapes for special chars.',
```

**Step 2**: Use the term inline in HTML:
```html
<span class="term" data-term="term-key">Display Text</span>
```

The tooltip system handles everything else automatically.

### Existing Term Definitions (41 terms)
`kv-cache`, `ttft`, `tpot`, `bpe`, `rope`, `gqa`, `mha`, `mqa`, `mla`, `flashattention`, `pagedattention`, `prefill`, `decode`, `tp`, `pp`, `ep`, `hbm`, `sram`, `nvlink`, `rdma`, `sse`, `fp16`, `fp8`, `int4`, `lora`, `moe`, `softmax`, `logits`, `autoregressive`, `speculative-decoding`, `continuous-batching`, `prefix-caching`, `awq`, `gptq`, `gguf`, `alibi`, `chunked-prefill`, `min-p`, `top-p`, `top-k`, `eos`

## JavaScript Interaction Model

### Three Nested Click Layers
The site has three levels of clickable elements, nested inside each other:

```
.pipeline-step  →  click .step-card to toggle .active (shows/hides detail)
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

## Pitfalls and Lessons Learned

### 1. WCAG Contrast on Dark Backgrounds
The Wong colorblind-safe palette was designed for white backgrounds. On dark backgrounds (#111115), some colors fail WCAG AA 4.5:1. Always verify:
```
contrast_ratio = (L1 + 0.05) / (L2 + 0.05)
```
where L1 > L2 are relative luminances. Badge text colors on `--bg-card` must all pass.

### 2. Tooltip Positioning and Stacking Contexts
Tooltips are appended to `document.body` (not inside the clicked element) to avoid stacking context issues from `transform`, `backdrop-filter`, etc. on parent elements. Position is calculated from `getBoundingClientRect()` with edge-clamping.

### 3. HTML Entities in Single-File HTML
All `<`, `>`, `&` in visible text must be entity-encoded. Mathematical formulas use entities: `&times;`, `&radic;`, `&sup2;`, `&rarr;`, `&mdash;`. Forgetting this causes silent parse errors where content disappears.

### 4. Badge Icon Method
Icons are CSS `::before` content using Unicode characters, not SVGs or icon fonts. This keeps the single-file approach clean. When adding badges, use characters that render well across platforms (test on macOS, Windows, Linux).

### 5. No Unnecessary Abstractions
This is intentionally simple. Don't add:
- A build step or bundler
- A CSS preprocessor
- A JS framework
- Separate CSS/JS files
- A component system

The single-file approach is a feature, not a limitation. It makes deployment trivial and the entire site greppable.

## Deployment Verification

After deploying, verify:
1. All 11 steps expand/collapse correctly
2. Sub-topics within steps work as accordions
3. Term tooltips appear on click and dismiss correctly
4. Phase overview cards scroll to correct anchors
5. Journey diagram renders without horizontal overflow on mobile
6. No console errors
