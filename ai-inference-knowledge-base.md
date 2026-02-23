# AI Inference & Infrastructure — Complete Knowledge Base

> This document captures all critical technical and business concepts from a study session preparing for PM interviews at Fireworks AI and Crusoe Energy Systems. It is structured to serve as a source document for creating an interactive educational website.

---

## Table of Contents

1. [Pricing AI Inference End-to-End](#1-pricing-ai-inference-end-to-end)
2. [Managed Inference vs. GPU Rental — Two Different Businesses](#2-managed-inference-vs-gpu-rental)
3. [Buy vs. Rent GPUs — The Full Decision Framework](#3-buy-vs-rent-gpus)
4. [How Contracted Revenue Works With Lenders](#4-how-contracted-revenue-works-with-lenders)
5. [Debt vs. Equity Across Company Stages](#5-debt-vs-equity-across-company-stages)
6. [Why Equity Costs More Than Debt](#6-why-equity-costs-more-than-debt)
7. [InfiniBand and Networking Costs for Multi-GPU Inference](#7-infiniband-and-networking-costs)
8. [Why Data Center Costs Are $/kW/month](#8-why-data-center-costs-are-dollar-per-kw-per-month)
9. [Why Smaller Models Process Tokens Faster](#9-why-smaller-models-process-tokens-faster)
10. [HBM (High Bandwidth Memory)](#10-hbm-high-bandwidth-memory)
11. [The Full Inference Pipeline — Request to Token](#11-the-full-inference-pipeline)
12. [Sampling — Top-k vs. Top-p](#12-sampling-top-k-vs-top-p)
13. [Finite State Machines for Constrained Decoding](#13-finite-state-machines)
14. [Server-Sent Events (SSE)](#14-server-sent-events)
15. [KV Cache Persistence Between Requests](#15-kv-cache-persistence-between-requests)
16. [The GPU Memory Hierarchy — Disk to Compute](#16-the-gpu-memory-hierarchy)
17. [Why the Same Model Has Massive Performance Variance](#17-why-same-model-has-massive-performance-variance)
18. [CapEx vs. OpEx — The Strategic Framework for AI Infrastructure](#18-capex-vs-opex)
19. [Why CapEx Is Value-Accretive in Supply-Constrained Markets](#19-capex-value-accretive)
20. [The Barbell Approach — Resource Allocation Across the Business](#20-the-barbell-approach)
21. [Key Concepts for Interview Readiness](#21-interview-readiness)

---

## 1. Pricing AI Inference End-to-End

### The Core Formula

**Cost per million tokens = (Total hourly cost of GPU + power + overhead) ÷ (tokens served per hour on that GPU)**

Throughput optimization is literally margin expansion. If custom CUDA kernels serve 2x the tokens/sec on the same GPU, cost per token halves.

### The Full Cost Stack (Bottom-Up)

**Hardware/Compute Cost:**
- GPU purchase: H100 SXM ~$25-30K, B200 ~$30-40K
- Buy vs. rent: Purchased GPUs amortize over 3-5 years (cheaper per hour if utilization is high, but depreciation risk from technology obsolescence); rented GPUs are OpEx
- H100 rental: ~$2.00-3.00/hr mid-market (down from $7-8/hr peak)

**Power & Cooling:**
- Power cost varies wildly: $0.03-0.05/kWh (Crusoe's stranded energy) vs. $0.08-0.12/kWh (grid in Northern Virginia)
- Single H100 draws ~700W under load. At $0.10/kWh = $0.07/hr per GPU. At 10,000 GPUs = $6.1M/year in electricity
- PUE (Power Usage Effectiveness): 1.1 means 10% overhead for cooling; 1.4 means 40%. Liquid cooling gets closer to 1.1

**Networking & Storage:**
- InfiniBand for multi-GPU inference adds 15-25% to total cluster cost
- Storage for model weights, KV cache spill, logging: ~5-10% of compute cost

**Data Center:**
- Expressed as $/kW/month (not $/sq ft) because power capacity is the binding constraint, not physical space
- AI racks draw 40-140 kW vs. 2-4 kW for legacy racks
- Typical range: $80-250/kW/month depending on facility type

**Operations:**
- SRE/DevOps staff, monitoring, on-call
- GPU failure rate: ~2-5% annually, need spares as buffer

### What Drives Throughput (The Denominator)

- **Model size**: Serving 8B model is ~10x cheaper per token than 405B
- **Optimization level**: Same model/GPU can have 3-5x throughput variance (naive vs. fully optimized). This is why prices vary 10x+ across providers
- **Batch size and utilization**: GPU serving one request at a time wastes most capacity. Continuous batching enables 80-90% utilization
- **Request characteristics**: Long context uses more KV cache memory, reducing batch slots. Output tokens cost more than input tokens (sequential decode vs. parallel prefill)
- **Hardware generation**: B200 with FP4 serves same model at dramatically higher throughput than H100 at FP16

### Target Margins

- Managed inference platform (Fireworks, asset-light): 50-65% gross margin at scale
- GPU rental infrastructure (Crusoe, asset-heavy): 40-55% gross margin
- Energy cost advantage (Crusoe): flows to either higher margin or competitive pricing

### Customer Segmentation and Willingness to Pay

| Segment | Characteristics | Pricing Model | Margin Profile |
|---|---|---|---|
| Hobbyist/prototyping | <1M tokens/day, spiky, price-sensitive | Serverless per-token, free tier | Low/negative (acquisition) |
| Growth startup | 1-100M tokens/day, latency-sensitive | Serverless → on-demand transition | Medium, expanding |
| Enterprise | 100M+ tokens/day, SLA requirements | Reserved capacity, custom contracts | High |
| Batch/offline | Large volumes, latency-insensitive | Batch pricing (50% discount) | Medium (fills idle capacity) |

### Pricing Structures

- **Per-token (serverless)**: $/million tokens, split input/output. Standard for API providers
- **Per-GPU-hour (dedicated)**: Billing per second of dedicated GPU access. Better economics at high utilization
- **Reserved/committed**: 30-60% discounts for 1-3 year commitments. Revenue predictability for both sides
- **Batch pricing**: 40-50% discount for non-real-time workloads. Fills idle capacity like standby airline tickets
- **Value-based**: Charging per image, per voice minute, etc. — aligns with customer's value perception

### The Deflation Dynamic & Reserved Contract Economics

Frontier inference cost declining ~10x annually. GPT-3.5 equivalent: $20/M tokens (late 2022) → $0.40/M tokens (2025).

**Reserved contracts are economically similar to fixed-rate swaps on GPU compute prices:**
- Provider receives fixed ($2.20/hr) from the customer
- Provider effectively pays floating (true market cost of delivering that compute, including opportunity cost)
- If market prices fall (most likely — secular deflation): Provider profits on the swap — receiving above-market fixed payments from the contracted customer. **Existing contracts become more valuable in a deflationary environment.**
- If market prices rise (demand spike/scarcity): Provider loses — delivering compute worth more than they're receiving, forgoing higher-paying on-demand customers

**The real risk isn't the contract price — it's what the contract signals about capacity planning.** Crusoe signs $500M in 3-year reserved contracts at $2.20/hr and uses those contracts to justify building a $2B data center. Financial model assumes uncontracted capacity fills at similar rates. When prices deflate to $1.20/hr, contracted $500M holds fine (above market), but uncontracted capacity must compete at $1.20/hr. Revenue falls short of the model that sized the debt.

**Both sides trade optionality for certainty:**
- Customer gives up price flexibility (locked at $2.20 when market may drop to $1.20 — overpaying). In exchange: guaranteed capacity, predictable costs, budget certainty
- Provider gives up volume flexibility (those GPUs are spoken for even if $4.00/hr buyers appear). In exchange: predictable revenue, debt capacity enablement, lower WACC

**The uncontracted capacity is the exposed position.** Contracted revenue is hedged. Uncontracted revenue is an outright bet on future pricing and demand. The PM must model what percentage of total capacity is contracted vs. speculative, and ensure debt obligations can be serviced even if uncontracted capacity fills at significantly lower rates.

### Strategic Pricing as a Weapon

- **Land and expand**: Price serverless tier aggressively (even below cost) to acquire developers, monetize at scale
- **Value-based for specialized use cases**: Voice agent inference priced per-minute, not per-token
- **Competitive moat pricing**: If you have 2x throughput advantage (FireAttention), price 30% below competitors while maintaining better margins

---

## 2. Managed Inference vs. GPU Rental

### Managed Inference (Fireworks Serverless, Crusoe Managed Inference)

**What you're selling:** Outcomes — tokens, completions, minutes of voice. Customer never thinks about GPUs.

**Additional costs beyond base stack:**
- Serving infrastructure software (FireAttention, MemoryAlloy) — significant R&D amortized across customers
- Multi-tenancy overhead: isolation, fair scheduling, abuse prevention, rate limiting
- Model hosting and lifecycle: maintaining 100+ model catalog, keeping warm on GPUs
- Operational SLAs: redundancy, failover, monitoring, on-call

**The magic: Statistical multiplexing.** Customer A peaks at 2pm, Customer B at 6pm, Customer C runs batch overnight. Pooling across thousands of customers enables 80-90% GPU utilization — far higher than any single customer. Like insurance pooling — law of large numbers applies to token traffic.

**Risk: Correlated demand spikes.** When a new model drops or viral AI demo happens, everyone hits the API simultaneously. This is the "catastrophic event" — need burst capacity or graceful degradation.

**Pricing design decisions:**
- Input vs. output pricing split (output 2-4x more expensive to serve — if not split, adverse selection occurs)
- Cached input pricing (~50% discount, incentivizes consistent prefixes)
- Model-specific pricing (based on cost to serve, competitive pricing, demand elasticity, strategic value)
- Batch vs. real-time (batch is "off-peak electricity" pricing)

**Realistic gross margins:** 55-70% on popular models, lower on long-tail catalog with sparse traffic.

**Key PM decisions:**
1. Which models to add to catalog and when (each has hosting cost whether used or not)
2. Where to set rate limits and SLA tiers (maps to burst capacity reserved per customer)
3. When to deprecate models (old models eating GPU memory, but enterprise customers may depend on them)
4. How aggressively to price new optimizations (pass savings to customers vs. keep as margin)

### GPU Rental (Crusoe Cloud, CoreWeave, Lambda)

**What you're selling:** Infrastructure — raw compute capacity. Customer brings own inference stack.

**Cost structure is simpler but more capital-intensive:**
- Hardware acquisition: 10,000 GPU cluster = $250-400M CapEx before serving a single customer
- Power is larger percentage of cost structure (no software margin layer on top)
- Networking: InfiniBand for 1,000 GPU cluster = $5-15M
- Platform/orchestration: Kubernetes, Slurm, monitoring, auto-node-replacement

**Pricing structure:**
- **On-demand**: Full price, no commitment, available when capacity exists
- **Reserved (1-3 year)**: 30-60% discount. Customer guarantees utilization, provider guarantees availability. Revenue predictability engine. Like a forward contract
- **Spot/preemptible**: 60-80% discount, can reclaim GPUs with short notice. Fills idle capacity

**Commitment structure is the most strategically important pricing decision.** GPU prices in secular decline. Reserved contracts function like fixed-rate swaps — provider locks in above-market revenue as prices deflate (the likely scenario), but forgoes upside if demand spikes push spot prices higher.

**The asymmetric risk:** In a deflationary environment, existing contracts favor the provider on the price dimension. The real exposure is on uncontracted capacity — if market rates fall 40%, the uncontracted portion of the fleet must compete at lower prices while debt was sized to the original rate assumptions. The PM must model contracted vs. speculative capacity ratios and stress-test revenue against deflation scenarios to ensure debt service coverage holds.

**Discount economics:** A 40% commitment discount looks expensive in isolation, but the contracted revenue it creates enables cheaper debt (SOFR + 250bps vs. + 400bps on uncontracted). The NPV of the capital structure benefit can exceed the discount given.

**Illustrative margin comparison:**

| Component | Crusoe (owns infra) | CoreWeave (leases some) |
|---|---|---|
| GPU CapEx amortized (3yr) | ~$0.95/hr | ~$1.10/hr |
| Power | ~$0.03/hr | ~$0.07/hr |
| Data center (amortized) | ~$0.15/hr | ~$0.25/hr |
| Networking | ~$0.10/hr | ~$0.10/hr |
| Operations/platform | ~$0.10/hr | ~$0.12/hr |
| **Total cost per H100-hr** | **~$1.33/hr** | **~$1.64/hr** |
| Selling price | $2.20/hr | $2.20/hr |
| **Gross margin** | **~40%** | **~34%** |

Crusoe's energy advantage flows straight to margin or competitive pricing power.

### The Strategic Tension Within One Company

When Crusoe launches Managed Inference, they create an internal tension:
- If MemoryAlloy delivers 9.9x better TTFT and 81% cost reduction, why would any inference customer rent raw GPUs?
- GPU rental increasingly serves training customers and custom workloads
- Managed inference captures inference demand at higher margin
- Total revenue per GPU potentially goes up
- PM must model the revenue migration carefully — cannibalization analysis

---

## 3. Buy vs. Rent GPUs

### The Core Financial Comparison

**Buy side TCO per GPU-hour:**
- Purchase price amortized over useful life
- Cost of capital on purchase price
- Power and cooling for life of asset
- Maintenance, failure/replacement (~2-5% annual)
- Residual/salvage value at end of life

**Depreciation tension:** H100 straight-line over 3 years = ~$0.95/hr. Over 5 years = ~$0.57/hr. But GPU technology cycles accelerating — H100 (2022) → H200 (2024) → B200 (2025). If Blackwell delivers 3-5x performance per dollar, H100 depreciating over 5 years becomes economically obsolete before fully depreciated.

**Rent side:** ~$2.00-3.00/hr for H100. No upfront CapEx. Flexibility to scale. Obsolescence risk sits with lessor. But paying their margin.

### Breakeven Utilization (Before Cost of Capital)

- Owned H100 fully loaded cost: ~$1.10/hr
- Rental: ~$2.50/hr
- Breakeven: ~44% utilization

### Cost of Capital Changes Everything

**Crusoe's capital costs:**
- Cost of equity: 20-30%+ (implied by venture investor expectations)
- Cost of debt: 7-10% (SOFR + 250-400 bps for secured infrastructure debt)
- Equity is 3-4x more expensive than debt

**Adding WACC to buy scenario:**
- If blended WACC for GPU purchases is 12%, the $28K H100 has economic cost of $3,360/year in capital charges alone
- Adds ~$0.38/hr to ownership cost, pushing fully loaded from $1.10 to ~$1.48/hr
- Breakeven utilization moves from 44% to ~59%
- If funded with pure equity at 25%: breakeven jumps to ~76%

**Interest rate environment matters.** 300-400 bps difference on $1B GPU purchase = $30-40M/year in additional interest expense.

### Technology Obsolescence — The Option Value Lens

**Buying a GPU = being long a depreciating asset with uncertain future value.**

**Renting a GPU = buying a monthly call option on compute capacity.** You pay a premium but maintain optionality to switch hardware, scale down, or pivot.

Option value of flexibility increases when:
- Volatility is high (AI hardware landscape changing every 12-18 months)
- Time horizon is uncertain
- Interest rates are high (higher cost of carry on owned assets)

Buying is attractive when:
- Demand is highly predictable (Crusoe's 15-year Abilene lease)
- Structural cost advantage in ownership (Crusoe's cheap power extends economic life)
- Can redeploy the asset across use cases

### Residual Value Scenarios

- **Bull case**: H100 retains 40% residual after 3 years (~$11K)
- **Base case**: 20% residual (~$5.5K)
- **Bear case**: 5% residual (~$1.4K) — next-gen makes it essentially worthless

### Optimal Strategy: Barbell Approach

Heavy ownership of base-load capacity funded by low-cost infrastructure debt secured against long-term contracts, combined with rental/spot capacity for flexibility. Like utilities: own baseload plants, buy peaking capacity on spot market.

---

## 4. How Contracted Revenue Works With Lenders

### It's Not Collateral — It's Cash Flow Underwriting

Collateral = physical assets lender can seize (data center, GPUs, land). Contracted revenue is a contractual obligation, not a physical asset.

Contracted revenue answers the question lenders care about most: **"How likely is it that you default in the first place?"**

### The Specific Mechanisms

**1. Higher debt capacity (how much they'll lend):**

Lenders size loans using Debt Service Coverage Ratio (DSCR) — want cash flow at 1.3-2.0x annual debt service.

Without contracted revenue: Lender underwrites to conservative estimate of $300M → $150M FCF → supports ~$100M/year debt service → ~$1-1.5B total debt capacity.

With 15-year Oracle contract guaranteeing $600M/year: Lender underwrites to $600M → $400M FCF → supports ~$267M/year debt service → ~$3-4B total debt capacity.

Same facility, dramatically different borrowing capacity.

**2. Lower interest rate (risk premium):**

Contracted revenue with investment-grade counterparty compresses spread. SOFR + 400bps → SOFR + 250bps. On $5B = $75M/year in interest savings.

**3. Looser covenants:**

Fewer restrictions on additional borrowing, less stringent maintenance ratios, more operational flexibility.

**4. Longer tenor:**

15-year contracted revenue can support 10-12 year debt. Uncontracted revenue might only support 3-5 year debt.

**The math matters enormously.** Same $1B borrowed:

5-year debt at 8%:
- Annual principal: $200M ($1B ÷ 5)
- Year 1 interest: ~$80M
- Total Year 1 debt service: ~$280M

12-year debt at 7% (lower rate because contracted revenue reduces risk):
- Annual principal: ~$83M ($1B ÷ 12)
- Year 1 interest: ~$70M
- Total Year 1 debt service: ~$153M

Same $1B borrowed. **$127M/year less in debt service.** $127M freed up annually to reinvest, build more capacity, or weather downturns.

**Why lenders tie tenor to contract duration:** A lender's nightmare is the loan outlasting the cash flows that support it. With a 15-year Oracle lease, a 10-12 year loan has cash flow coverage for the entire repayment period plus 3-5 years of cushion. Without the contract, the lender won't extend beyond 3-5 years — they want frequent exit points to re-evaluate.

With 15-year contract:
```
Year 1 ──────────────────────────────── Year 15
|████████████████████████████████████████| Oracle pays $600M/yr (guaranteed)
|██████████████████████████|              Loan repayment (12 years)
                            ^^^^^^^^^^^^  3-year cushion — contract outlasts debt
```

Without contract:
```
Year 1 ──────────────────────────────── Year 15
|░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░| Projected revenue (uncertain)
|████████|                                Loan repayment (3-5 years)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Lender doesn't want exposure here
```

**Compounding strategic effects of longer tenor:**

*CapEx timing alignment:* Data centers take 12-18 months to build and 5-7 years to reach full utilization. A 3-year loan forces aggressive principal repayment while the asset is still ramping — cash flow negative not because economics are bad, but because repayment doesn't match the asset's earning curve. A 12-year loan aligns repayment with productive life.

*Refinancing risk elimination:* 5-year loan on a 15-year asset means refinancing at year 5. Interest rates might be 300bps higher. Credit markets might be tight. Revenue might have dipped temporarily. Every refinancing event is a moment of vulnerability. A 12-year loan eliminates two or three of those cycles.

*DSCR resilience:* Lower annual debt service means the same cash flow supports a higher DSCR:
- 5-year tenor: $280M debt service on $400M FCF → DSCR = 1.43x (tight — breaches 1.3x covenant with just 9% revenue decline)
- 12-year tenor: $153M debt service on $400M FCF → DSCR = 2.61x (comfortable — survives 45% revenue decline before breach)

*Competitive pricing flexibility:* $127M/year less debt service = $127M more free cash flow to price aggressively, fund R&D, or build next facility. A competitor with shorter-tenor debt has higher fixed obligations and less room to cut prices during a price war. **Debt structure becomes a competitive weapon.**

**The Abilene example:** Crusoe's $9.6B JPMorgan facility for Abilene is secured against the 15-year Oracle/Stargate lease. If financed with 5-year debt instead, annual principal alone would be ~$1.92B versus ~$800M on a 12-year amortization. The difference — over $1B/year — would make the project borderline unfinanceable because early-year cash flows (still ramping to full capacity) couldn't cover payments.

### Assignment of Contracts (Quasi-Collateral)

Lenders structure assignment of contracts as security interest. If Crusoe defaults, JPMorgan could step into Crusoe's position and receive Oracle's lease payments directly. Like mortgage-backed securities — payment streams are the security.

### The PM Implication

**Pricing decisions directly affect capital structure capacity:**

Commitment discounts aren't just about giving customers lower prices. They convert uncertain future revenue into bankable contracted revenue that multiplies capital-raising capacity.

A 40% discount on a 3-year commitment might look expensive in a simple pricing analysis, but the debt capacity it unlocks can make the NPV significantly higher than full-price on-demand.

Every 1-year or 3-year GPU reservation contract signed makes the debt package more attractive to lenders → cheaper debt → lower WACC → more competitive pricing → more customers → more contracts → even more debt capacity. A virtuous cycle.

---

## 5. Debt vs. Equity Across Company Stages

### The Fundamental Principle

Debt requires predictability. Equity tolerates uncertainty. As a company matures, cash flows become more predictable, shifting the optimal mix.

### Stage-by-Stage Breakdown

**Series A — Almost Entirely Equity:**
- No revenue, no assets, no track record. Might not exist in 18 months
- Cost of equity: 50-100%+ implied (VCs need 100x potential to justify risk)
- Cost of debt: N/A (unavailable)
- Mix: 95-100% equity
- Exception: Venture debt (20-30% of last equity round) with warrants, effectively equity-flavored

**Series B — Equity-Dominant, Debt Emerging:**
- $5-20M ARR, real product, paying customers
- Cost of equity: 30-50%
- Cost of debt: 12-15% (venture debt with warrants)
- Mix: 80-90% equity
- Equipment financing possible (80% LTV on GPU purchases)

**Series C/D — Mix Shifts Meaningfully:**
- $50-200M+ ARR, proven unit economics, enterprise contracts
- Cost of equity: 15-25%
- Cost of debt: 8-12% (term loans, asset-backed facilities)
- Mix: 60-80% equity
- CoreWeave pioneered billions in GPU-backed debt at this stage
- Fireworks (Series C, $4B valuation) entering this zone

**Pre-IPO / Late Stage:**
- $200M-1B+ revenue, clear path to profitability
- Cost of equity: 12-20%
- Cost of debt: 6-9% (investment-grade or near)
- Mix: 40-60% equity
- Convertible notes popular: lower interest (2-5%) with embedded call option to convert to equity
- Crusoe is here now (Series E, $10B+, Michael Gordon hired as CFO)

**Public Company — Full Optimization:**
- Cost of equity drops dramatically: 10-15% (liquidity, transparency, diversifiability)
- Cost of debt: 4-7% (investment-grade bonds, commercial paper)
- Gap between equity and debt narrows but never closes (debt holders always paid first)
- Active management: share buybacks, debt-funded buybacks, dividend policy, credit rating management

### Crusoe's Unique Position

Two capital structures in one company:
- **Project-finance-funded infrastructure** (low cost of capital, asset-heavy, contracted): Abilene's $9.6B debt + $5B equity
- **Venture-funded software platform** (high cost of capital, asset-light, uncertain): Cloud platform, managed inference

PM must understand which investments belong to which bucket. Managed inference feature ($20M) → equity-funded, needs venture-scale returns. Data center with signed contract → leverage cheap debt, lower hurdle rate.

---

## 6. Why Equity Costs More Than Debt

### Core Reason: Risk Hierarchy (Who Gets Paid First)

Debt holders have priority in capital structure. If company struggles, they get paid first. They have contractual cash flows (fixed interest, principal repayment). Can force bankruptcy and seize collateral. Less risk → accept lower return.

Equity holders are last in line. Return is entirely residual. No guarantee of any return. Might make 10x or lose everything. Higher risk → higher required return → higher cost of capital.

### Specific Mechanisms

**Tax shield:** Interest payments are tax-deductible. At 21% corporate tax rate, 8% interest → ~6.3% after-tax cost. Equity returns have no such benefit.

**Contractual vs. residual claims:** Debt holder's max upside is the interest rate (8%). They price for downside protection. Equity investor's return is uncapped but so is downside. Need much higher expected return to justify.

**Information asymmetry and control:** Debt holders protect themselves with covenants. Equity holders have board seats but can't contractually force profitability. More trust required = riskier.

### Why Not All Debt?

- Financial distress risk: Too much leverage makes temporary downturn existential
- Debt capacity limits: Lenders won't fund beyond cash flow coverage
- Rising marginal cost: First billion at 8%, fifth billion at 12%. At some point additional debt costs more than equity

---

## 7. InfiniBand and Networking Costs

### Why Multi-GPU Inference Needs Special Networking

Single H100 = 80 GB HBM. Llama 70B at FP16 = 140 GB (needs 2+ GPUs). Llama 405B at FP16 = 810 GB (needs 10-12+ GPUs).

When model is split via tensor parallelism, every forward pass requires GPUs to exchange data via all-reduce at every layer. This happens for every token generated. During decode, computation per token is so fast that communication latency becomes proportionally large.

### InfiniBand

- Bandwidth: 400 Gbps (NDR), moving to 800 Gbps
- Latency: ~1 microsecond port-to-port vs. ~5-10+ for Ethernet
- RDMA: GPUs read/write directly to each other's memory without CPU/OS involvement
- GPUDirect: GPU → InfiniBand NIC → InfiniBand NIC → GPU, no CPU involved

**Costs for 1,000 H100 cluster (~$5-15M, 15-25% of total cluster cost):**
- ConnectX-7 NICs: $1,500-3,000 each, 4-8 per node = $6,000-24,000/node
- Switches: $15,000-100,000+ each, 50-100+ needed
- Cabling: $500-2,000 each, thousands needed
- NVIDIA/Mellanox near-monopoly = limited price negotiation

### Ethernet Alternative

- 30-50% cheaper switches and NICs, multiple vendors
- RoCE (RDMA over Converged Ethernet) partially closes performance gap
- 2-3x latency gap vs. InfiniBand with RoCEv2 (down from 5-10x)
- Ultra Ethernet Consortium building open standard, 2-3 years out

### NVLink (Intra-Node)

- 900 GB/s bidirectional on H100 (18x faster than 400Gbps InfiniBand)
- Keeping model within a single node is dramatically better for performance
- **Blackwell GB200 NVL72**: 72 GPUs in single NVLink domain at 1.8 TB/s — 405B model that previously needed inter-node InfiniBand now fits within one NVLink domain

---

## 8. Why Data Center Costs Are $/kW/month

### Power Is the Binding Constraint, Not Space

Legacy server rack: 2-4 kW. AI rack with 8 H100s: 40-70 kW. GB200 NVL72 rack: 120-140 kW. That's 30-70x more power but roughly the same physical footprint. Pricing by square footage breaks completely.

### What $/kW/month Bundles

- **Power delivery infrastructure**: Transformers ($1-5M each), switchgear, UPS, backup generators, distribution
- **Cooling capacity**: Every watt consumed → watt of heat to remove. At 140 kW/rack, air cooling physically can't keep up. Liquid cooling required. Cost scales linearly with kW
- **Redundancy**: N+1 or 2N power redundancy. 100 kW provisioned → 200 kW built
- **Physical space**: Essentially free at this point — tiny fraction of total cost

### Typical Ranges

| Facility Type | $/kW/month |
|---|---|
| Wholesale colocation (legacy) | $80-120 |
| Retail colocation | $120-180 |
| AI-optimized facility | $150-250+ |
| Hyperscaler self-build | $50-80 effective |

### Crusoe's Energy Advantage Through This Lens

Traditional operator at $0.10/kWh: 1 kW continuous for a month = $72 just in electricity. Out of $150/kW/month price, nearly half is power.

Crusoe at $0.03/kWh: Same 1 kW = $22/month. That's ~$50/kW/month advantage.

At 100 MW facility (100,000 kW): $50/kW/month advantage = **$5M/month = $60M/year** in structural cost savings.

---

## 9. Why Smaller Models Process Tokens Faster

### Every Token Requires Full Forward Pass

Llama 3.1 8B: 32 layers, 8 billion parameters.
Llama 3.1 405B: 126 layers, 405 billion parameters.

For every token, 405B model performs ~50x more matrix operations.

### Memory Bandwidth Is the Bottleneck

During decode, GPU loads model weights from HBM for each token.

- 8B model: ~16 GB weights at FP16. H100 streams through in ~4.8ms
- 405B model: ~810 GB weights. Even across 8 GPUs, ~30ms per token

Generating one token on 405B takes roughly 6x longer just from weight streaming.

### Multi-GPU Communication Overhead

8B fits on single GPU. Zero inter-GPU communication.
405B split across 8+ GPUs. 126 layers × all-reduce at each = 126 synchronization points per token. Pure penalty that doesn't exist for smaller model.

### Larger KV Cache = Less Batching

405B's larger hidden dimensions → larger KV cache per token → less memory for batch slots. 8B might batch 64 requests; 405B might batch 8-16.

### Compounding Effect

| Factor | 8B | 405B | Penalty |
|---|---|---|---|
| Weight data per token | ~16 GB | ~810 GB | ~50x |
| GPUs required | 1 | 8-16 | 8-16x hardware cost |
| Communication overhead | Zero | 126 all-reduce ops | Pure penalty |
| Batch size | 64+ | 8-16 | 4-8x less throughput/GPU |
| Cost per million tokens | ~$0.03-0.05 | ~$0.50-1.00 | 10-20x more expensive |

---

## 10. HBM (High Bandwidth Memory)

### What It Is

Memory chips stacked vertically (8-12 layers of DRAM) connected by through-silicon vias (TSVs), placed directly on the same silicon package as the GPU die via silicon interposer.

Analogy: DDR5 is a single 8-lane highway connecting a distant warehouse to a factory. HBM is building the warehouse adjacent to the factory with a 1,024-lane highway.

### Key Specs

| Spec | DDR5 (Laptop) | HBM2e (A100) | HBM3 (H100) | HBM3e (H200/B200) |
|---|---|---|---|---|
| Bandwidth | ~50-100 GB/s | ~2,000 GB/s | ~3,350 GB/s | ~4,800-8,000 GB/s |
| Capacity/GPU | N/A | 80 GB | 80 GB | 141-192 GB |

H100's 3,350 GB/s is 40-60x faster than DDR5.

### Why Both Bandwidth AND Capacity Matter

**Bandwidth → token generation speed.** Each token requires streaming through model weights. Higher bandwidth = lower inter-token latency.

**Capacity → what fits on one GPU.**
- H100 (80 GB): 70B model at FP16 (140 GB) doesn't fit → needs 2+ GPUs → communication overhead
- H200 (141 GB): Same model fits on one GPU → no communication, simpler, cheaper

H200 was a big deal for inference despite identical compute specs to H100. More memory at higher bandwidth.

### Manufacturing

Only three producers: SK Hynix (dominant, supplies NVIDIA), Samsung, Micron. Extremely complex manufacturing — stacking and connecting 8-12 silicon layers with thousands of vertical interconnects.

---

## 11. The Full Inference Pipeline

### Step 1: API Gateway & Request Routing

**1a. Authentication & Validation** (1-5ms)
- Validate API key, check tier/quota, validate request structure
- Optional content moderation before spending GPU compute

**1b. Rate Limiting & Queuing**
- Per-customer rate limits (requests/sec, tokens/min) map to pricing tier
- Queue vs. reject during spikes — queue adds latency, reject loses revenue
- Rate limits are pricing levers, not just technical constraints

**1c. Model Routing**
- Determine which physical GPUs serve the request
- Hot models: load-balance across replicas considering queue depth, batch occupancy, memory
- Cold models: cold-start (30-120 seconds for large models) or pre-warmed standby
- Multi-LoRA: base model stays loaded, adapters swapped per-request
- Router efficiency = utilization rate = most important unit economics driver

### Step 2: Tokenization

**How it works:** BPE or SentencePiece splits text into subword units → mapped to integer IDs.

- "How does inference work?" → [2347, 1721, 45892, 1736, 30] = 5 tokens
- ~4 characters per token (English), ~3 for code, worse for CJK languages
- Different models use different tokenizers → same text = different token counts
- Tokenization time: <1ms, negligible

**Why tokens matter:** Fundamental unit of cost and latency. Input tokens determine prefill time and KV cache size. Output tokens determine decode time. Most providers charge separately because different cost profiles.

**PM decision:** Japanese users pay ~2x more per word due to tokenizer inefficiency. Price uniformly (simpler) or adjust (fairer)?

### Step 3: Prefill Phase (Compute-Bound)

**What happens:** Process entire input prompt in one forward pass. For each layer:
1. Attention: every input token attends to every other (matrix-matrix multiplication, O(n²))
2. KV cache creation: Key and Value vectors stored for each token at each layer
3. Feedforward: two large matrix multiplications per layer

**KV Cache — the most important data structure:**
- Stores "memory" of processed tokens so model doesn't recompute during decode
- Size grows linearly with sequence length: ~1.2 MB/token for 70B model
- 4K prompt = ~4.8 GB KV cache. 128K context = ~153 GB
- Competes with batch slots for GPU memory

**Prefix caching:** If many requests share same system prompt, KV cache for that prefix computed once and reused. Fireworks charges ~50% less for cached input tokens.

**TTFT (Time to First Token):**
- Short prompt, 8B model: 50-200ms
- Short prompt, 70B model: 200-800ms
- 100K+ context, 70B: 2-10 seconds
- Prefill is compute-bound (GPU ALUs are the bottleneck, not memory bandwidth)

**Disaggregated prefill/decode:** Run prefill on compute-optimized hardware, decode on memory-bandwidth-optimized hardware. Added complexity, better performance for latency-sensitive, high-volume customers.

### Step 4: Decode Phase (Memory-Bandwidth-Bound)

**The autoregressive loop — generates tokens one at a time:**
1. Take most recently generated token
2. Pass through every layer, using KV cache to attend to all previous tokens
3. Produce probability distribution (logits) over entire vocabulary (~32K-128K)
4. Sample one token
5. Append token's K and V vectors to KV cache
6. Repeat until stop condition

**Cannot be parallelized.** Token N+1 depends on token N. Sequential dependency is why decode is slower than prefill and why output tokens cost more.

**Why memory-bandwidth-bound:** Each token requires matrix-vector multiplication (one token × weight matrix). Compute is minimal but GPU must load entire weight matrix from HBM. For 70B at FP16: load ~140 GB from HBM at 3.35 TB/s → ~42ms minimum per token → ~24 tokens/sec. GPU compute cores mostly idle, waiting for data.

**Arithmetic intensity problem:** Compute operations ÷ bytes loaded is abysmal for decode. GPU has ~990 TFLOPS but can only use fraction because starved for data. Batching fixes this by amortizing weight loading across multiple tokens.

**TPOT/ITL (Time Per Output Token):**
- 8B model: 10-20ms
- 70B model: 30-60ms
- <60ms target for "real-time" feel
- Total generation latency = TTFT + (TPOT × output tokens)

**Multi-GPU decode:** Tensor parallelism requires all-reduce at every layer. 405B on 8 GPUs = 126 synchronization points per token. Each adds ~5-20 microseconds over NVLink. Doesn't exist for single-GPU models.

### Step 5: Sampling

**Temperature:** Scales logits before softmax. T=0 → deterministic (always highest prob). T=1.0 → model's distribution. T>1 → flatter, more "creative."

**Top-k:** Keep only k highest-probability tokens, zero rest, renormalize to sum to 1.
- Problem: Fixed k doesn't adapt to distribution shape. Too loose when model is confident, too tight when uncertain.

**Top-p (nucleus):** Keep smallest set of tokens whose cumulative probability exceeds p, renormalize.
- Adapts to distribution: high confidence → few tokens. Low confidence → many tokens.
- Generally preferred, industry default.

**Both require renormalization** after filtering — the kept tokens don't sum to 1 until renormalized. This subtly concentrates probability mass onto fewer tokens.

**In practice:** Most systems combine both (top-k as hard ceiling, top-p within filtered set). Most developers only adjust temperature.

**Structured output / constrained decoding:** Uses finite state machines to mask invalid tokens at each step. Tracks current state of output (e.g., position in JSON grammar), zeros out tokens that would produce invalid next characters. FSMs are O(1) per token — microseconds of overhead. Actually uses pushdown automata (FSMs with a stack) to handle nesting.

**Stop conditions:**
- EOS (end-of-sequence) token
- Max tokens parameter
- Custom stop sequences
- Context window exhaustion

### Step 6: Detokenization & Streaming

**Detokenization:** Token IDs → text. Complication: subword fragments ("infer" + "ence") must sometimes be buffered. CPU-bound, <0.1ms per token.

**SSE (Server-Sent Events):** Server pushes tokens over single long-lived HTTP connection.
- Client makes one request, connection stays open
- Each token sent as `data: {...}` event
- `data: [DONE]` signals completion
- Unidirectional (server → client only), simpler than WebSockets
- Why SSE not WebSockets: inference is unidirectional. SSE works over plain HTTP, automatic reconnection, works through proxies/CDNs, simpler to implement

**Operational nuance:** Connection stays open 10-30 seconds for long responses. Load balancers/proxies may have idle timeouts that kill connections before generation completes. Manifests as mysterious truncated responses.

**Multi-turn conversations:** Each turn is completely separate HTTP request. SSE connection closes after [DONE]. Server retains no state between turns. Client assembles full conversation history and sends entire thing as prompt in each new request. Every turn gets more expensive — entire history re-sent as input tokens.

**Usage tracking:** Token counts (input, output, cached), latency telemetry, GPU time consumed, model/parameters used. Revenue recognition differs for per-token vs. per-GPU-hour billing.

### Step 7: Serving Optimizations

(See Section 17 for detailed breakdown of each optimization)

---

## 12. Sampling — Top-k vs. Top-p

### Top-k Pros and Cons

**How it works:** Always keeps exactly k tokens regardless of probability distribution.

**Problem — fixed k ignores distribution shape:**
- High confidence (95% sure next token is "Paris"): k=50 keeps 49 near-zero-probability tokens. Adding noise
- Low confidence (200 plausible next words in creative fiction): k=50 arbitrarily throws away 150 reasonable options. Constraining creativity
- Same k value, too loose in one scenario, too tight in the other

**Pros:** Simple, deterministic candidate count, computationally trivial.

### Top-p Pros and Cons

**How it works:** Keeps smallest set of tokens whose cumulative probability exceeds p. Number of tokens varies dynamically.

**Why generally better — adapts to distribution:**
- High confidence: Top-p=0.95 keeps 1-2 tokens. Tight, decisive
- Low confidence: Top-p=0.95 keeps 200 tokens. Wide, exploratory
- Respects model's own uncertainty

**Downsides:**
- Less predictable (unknown candidate set size) — harder to debug
- Edge case sensitivity at extreme p values
- Slightly more compute (sort + cumulative sum vs. just top-k)

### Renormalization

Both top-k and top-p require renormalization after filtering. Kept tokens don't sum to 1 — you divided by their sum. This concentrates probability mass, making outputs slightly more "confident" than raw distribution.

### Industry Practice

Most systems combine both. Top-k as hard ceiling, top-p within filtered set. Industry converged on top-p as default. Most developers never tune these — only adjust temperature.

---

## 13. Finite State Machines

### What They Are

System with fixed number of states, transitions between them based on inputs following defined rules.

Turnstile example: Two states (locked, unlocked). Two inputs (push, insert coin). Clear transition rules.

### Application to Constrained Decoding

FSMs enforce valid output format (JSON, specific schema) at the sampling step.

FSM tracks position in grammar:
- START → only `{` valid
- EXPECTING_KEY → only `"` valid
- INSIDE_KEY_STRING → any char or `"` to close
- EXPECTING_COLON → only `:` valid
- EXPECTING_VALUE → `"`, digit, `{`, `[`, `t`, `f`, `n` valid

At each decode step, before sampling, system checks "given current FSM state, which tokens produce valid next characters?" Invalid tokens get probability zeroed out.

**Performance:** O(1) per token — microseconds of overhead. Critical since running for every token at 30-60ms each.

**Limitation:** FSMs enforce regular grammars (structural patterns, not semantic meaning). For nesting (JSON braces/brackets), actually need pushdown automata (FSM + stack). Everyone calls it "FSM-based" colloquially.

---

## 14. Server-Sent Events

### Why SSE Exists

Normal HTTP: client sends request, server does all work, sends complete response. For LLM inference, that means user stares at blank screen for 5-10 seconds. With SSE, server sends each token as generated. Perceived latency drops from total generation time to TTFT.

### How It Works

Server responds with `Content-Type: text/event-stream`, keeps connection open. Each chunk prefixed with `data:`, separated by blank lines. `[DONE]` signals completion.

### SSE vs. WebSockets

SSE: unidirectional (server → client), plain HTTP, automatic reconnection, works through proxies/CDNs, simpler.
WebSockets: bidirectional, separate protocol (ws://), doesn't work through many proxies, requires explicit connection management.

Inference streaming is unidirectional — SSE fits perfectly. Every major inference API uses SSE.

### Multi-Turn Conversations Are Stateless

Each turn is separate HTTP request. Server retains no state between turns. Client assembles full conversation history in each request's `messages` array. Each turn's entire history re-processed in prefill, KV cache rebuilt (unless prefix caching).

---

## 15. KV Cache Persistence Between Requests

### Within a Single Request: Append Only

During decode, model generates token N, computes K and V vectors, appends to existing cache. No rebuilding — the whole point of the KV cache.

### Between API Requests: Design Choice

**Naive stateless serving (default):** KV cache discarded after request completes. GPU memory freed. Next turn re-prefills entire history. Wasteful but simple — no state management, no orphaned caches.

**Prefix caching (optimized):** Server keeps recent KV caches in GPU memory for some time window. When next turn arrives with identical prefix, reuses cached KV entries, only prefills new tokens. "Just append" approach.

### Why Not Always Cache?

**Memory pressure:** 70B model's KV cache at 128K = ~153 GB. 1,000 concurrent users holding caches = enormous idle memory. Each cached conversation = memory that can't serve active requests.

**Uncertainty about return:** User might send next message in 2 seconds, 2 minutes, or never. Classic cache eviction problem — LRU, TTL-based, or predictive eviction.

**Routing complexity:** KV cache lives on specific GPUs. Next request must route to same GPU(s) — creates session affinity, hot spots, reduced utilization.

**Correctness edge cases:** User edits previous message? Cached KV is stale. Must detect prefix divergence and invalidate.

**Fireworks prices cached tokens at ~50% discount** — signals customers to structure requests with consistent prefixes, aligning customer behavior with the optimization that makes caching work.

---

## 16. The GPU Memory Hierarchy

### Full Path: Disk → CPU RAM → HBM → SRAM → Compute Cores

**Disk (NVMe SSD):** Model weights stored here permanently. Read speed ~7 GB/s. 70B model at FP16 (140 GB) takes 20-30 seconds to load. This is the "cold start."

**CPU RAM (System Memory):** Transit point. PCIe 5.0 transfer to GPU: ~64 GB/s.

**HBM (GPU Memory):** 80 GB on H100, 141 GB on H200. This is where model weights live during serving. Bandwidth: 3,350 GB/s (H100). Loaded once at model startup, stays resident.

**SRAM (On-Chip Cache):** ~50 MB on H100. Directly accessible to compute cores. Extremely fast. But tiny — can hold ~0.036% of a 70B model.

### The Per-Token Weight Loading Problem

For every output token, GPU processes model layer by layer:
- Load Layer 1 weights from HBM → SRAM → compute → evict Layer 1
- Load Layer 2 weights from HBM → SRAM → compute → evict Layer 2
- ...repeat for all layers

**Cannot keep weights on-chip between tokens.** They physically don't fit. Even a single layer (~1.7 GB for 70B model) exceeds SRAM (~50 MB) by 34x — loaded in ~34 tiles.

**Prefetching:** While computing on Layer N's weights, memory controller prefetches Layer N+1's weights. Overlaps data movement with computation, reduces idle time. Optimization at margins — doesn't change fundamental constraint.

### Why Batching Fixes This

Batch of 32 requests: load weights once per layer, perform 32 matrix-vector operations (effectively matrix-matrix). 32x more useful compute for same memory bandwidth cost. Weight-loading cost amortized across batch.

At batch size 1: GPU ~2-5% compute utilized (starved for data).
At batch size 32: GPU ~60-80% utilized (same weights, dramatically more work per byte).

---

## 17. Why the Same Model Has Massive Performance Variance

### Naive Baseline (~300 tokens/sec system throughput)

HuggingFace Transformers with defaults: FP16 weights, static batching, standard attention, no KV cache optimization, no speculative decoding. Basic tensor parallelism.

### Optimization Stack (Cumulative Multipliers)

**Layer 1: Continuous Batching (~2.5x)**
- Replace static batching: slots freed immediately when requests finish
- GPU utilization: ~30% → ~60-70%
- Throughput: ~300 → ~750 tok/s
- Requires custom serving scheduler — meaningful engineering

**Layer 2: PagedAttention (~1.7x)**
- Reduce KV cache memory waste ~55%
- Fit 30-40 requests in batch instead of 16
- Throughput: ~750 → ~1,275 tok/s
- Compound with continuous batching: batching creates demand for slots, PagedAttention creates memory to fill them

**Layer 3: FlashAttention (~1.3x)**
- Prefill: 2-4x faster (TTFT 800ms → 300ms)
- Decode: ~1.2-1.3x (more modest — vector-matrix, not matrix-matrix)
- Enabling: makes 128K+ context windows possible at all
- Throughput: ~1,275 → ~1,658 tok/s

**Layer 4: Quantization (~1.8x)**
- FP16 → FP8: model weights halved, may fit on 1 GPU instead of 2
- Eliminates inter-GPU communication, halves HBM bandwidth requirement
- H100 has native FP8 tensor core support (2x FLOPS of FP16)
- Accuracy loss typically <1% at FP8
- Throughput: ~1,658 → ~2,984 tok/s

**Layer 5: Custom CUDA Kernels (~1.3x)**
- Fused operations: combine attention projection + computation + output into single kernel, keep intermediates in SRAM
- Optimized memory access patterns: coalesced reads, bank-conflict-free shared memory
- Reduced kernel launch overhead: fewer launches, ~5-10μs saved each
- Architecture-specific: exploit MoE routing, grouped-query attention, etc.
- Hardest to replicate, most durable competitive advantage
- Fireworks' FireAttention: custom kernels delivering 300+ tok/s on Mixtral 8x7B
- Throughput: ~2,984 → ~3,879 tok/s

**Layer 6: Speculative Decoding (~1.3x for latency)**
- Small draft model generates candidates, large model verifies in parallel
- Mathematical guarantee: exact same output distribution, zero quality loss
- Best at small batch sizes (GPU has spare capacity for draft model)
- Helps latency-sensitive use cases most (chatbots, code completion)
- Throughput: ~3,879 → ~5,043 tok/s

### Total: ~15-17x Improvement on Identical Hardware

From ~300 tok/s naive to ~5,000 tok/s fully optimized.

### Why This Creates 10x+ Price Variance

**Provider A (minimal optimization):** 1,000 tok/s → cost $0.69/M tokens → charges $1.00-1.50

**Provider B (fully optimized):** 5,000 tok/s → cost $0.14/M tokens → charges $0.40 at 65% gross margin

Provider B charges less than half while making better margins. When providers subsidize for market share, range reaches 10x+.

### Why the Gap Persists

Anyone can turn on vLLM defaults (continuous batching + PagedAttention). Custom CUDA kernels that outperform open-source stack require deep GPU programming expertise — years of accumulated engineering. Fireworks' team built PyTorch at Meta. This optimization gap is the core moat of inference platforms.

---

## 18. CapEx vs. OpEx — The Strategic Framework for AI Infrastructure

### It's Not a Preference — It's a Portfolio Decision

Crusoe needs both in different ratios for different parts of the business. The question isn't "which is better" but "which resource allocation maps to which type of spending."

### Why CapEx Is Structurally Advantaged for Crusoe's Core Infrastructure

Crusoe's competitive thesis is vertical integration and energy cost advantage. That thesis **requires** owning assets. You can't have a structural energy cost advantage renting someone else's data center on someone else's power.

**Cost of capital favors ownership.** Crusoe finances infrastructure CapEx with cheap project-finance debt (7-9%) secured against long-term contracts. Renting equivalent capacity means paying the landlord's margin on top of their cost of capital. When Crusoe builds a data center, all-in cost might be $80/kW/month. Renting equivalent from Equinix costs $150-250/kW/month.

**Depreciation creates tax shield.** $2B data center depreciated over 15-20 years generates $100-133M/year in non-cash expense reducing taxable income. Combined with interest deductibility on the debt, effective after-tax cost of ownership is substantially lower than nominal cost. At Crusoe's ~$1B revenue (2025), these shields are material.

**Asset appreciation in supply-constrained market.** Permitted, powered, connected data center capacity is scarce. Assets built today may be worth more than construction cost because permitting, power interconnection, and construction timelines create a multi-year barrier to replication. (See Section 19 for full explanation.)

**Contracted revenue justifies it.** 15-year Abilene lease converts speculative CapEx into project finance. Cash flows are predictable enough to underwrite long-tenor debt. Without ownership, there's nothing to contract against.

### Where CapEx Becomes Dangerous

**Technology obsolescence — the critical split.** Data center infrastructure (building, power, cooling, networking) has 15-25 year useful life. GPUs inside them have 3-5 year economic lives that are shrinking. These are two separate CapEx categories with fundamentally different depreciation profiles and risk.

Crusoe buys 10,000 H100s for $280M. Two years later, B200s deliver 3-5x performance per dollar. H100s still work, but customers can get equivalent compute elsewhere for a fraction. Accounting says 3 more years of depreciation; economics say the asset is impaired.

**Demand uncertainty on uncontracted capacity.** CapEx is irreversible. Build 500 MW capacity but fill only 300 MW → 200 MW burning cash on power delivery, cooling, and financing costs whether GPUs are running or not. OpEx (renting) would let you scale down.

**Capital intensity limits growth velocity.** Every CapEx dollar is unavailable for software platform, engineering talent, customer acquisition, and geographic expansion.

### Where OpEx Makes More Sense

**Software platform layer:** Crusoe Cloud, Managed Inference, Intelligence Foundry — primarily engineering talent (salaries = OpEx). No physical asset to depreciate or finance.

**GPU fleet flexibility at the margin:** Own 80% of GPU fleet (base load, contracted). Rent 20% as flex layer. If customer churns or demand shifts to different GPU type, return leased GPUs without stranded asset. (See Section 20 — Barbell Approach.)

**New market entry:** Expanding to new geography (Iceland, Norway) — rent initially to validate demand, then commit CapEx once customer pipeline is proven. OpEx as scouting function, CapEx as scaling function.

### The Optimal Mix at Crusoe's Stage

**Heavy CapEx on long-lived, defensible infrastructure:** Data centers, power delivery, cooling, networking. These are the moat. Finance with project debt against long-term contracts. Depreciate over 15-20 years. Value doesn't erode with GPU generations because the shells are hardware-agnostic.

**Moderate CapEx on GPUs with aggressive depreciation:** Own base-load fleet serving contracted customers. Depreciate over 3 years (realistic given technology cycles). Accept uncertain residual value. Finance with equipment-backed facilities, not project debt.

**OpEx on everything above infrastructure:** Software development, sales, marketing, customer success, spot/flex GPU capacity, new market exploration.

### The Investor and IPO Lens

**CapEx-heavy companies trade at lower revenue multiples.** Pure SaaS growing 50% trades at 15-20x revenue. Infrastructure company growing 50% with 60% CapEx intensity trades at 5-8x.

**But recurring infrastructure revenue has its own premium.** Long-term contracted data center revenue is valued almost like a bond — highly predictable, low churn. Digital Realty and Equinix trade at 20-30x AFFO because revenue is contracted and recurring.

**Crusoe's IPO narrative challenge.** Michael Gordon (ex-MongoDB CFO, led 2017 IPO) was hired partly to craft this story. If positioned as "we spend billions on data centers" → infrastructure multiples. If positioned as "we have $1.5B in contracted cloud ARR growing 5x with managed inference software on top" → blended infrastructure + software multiples.

**PM implication:** Product mix decisions (GPU rental vs. managed inference vs. platform services) directly affect valuation multiple. Every dollar shifted from "infrastructure rental" to "software-delivered inference" potentially increases the company's multiple. Managed inference matters beyond its margin profile — it affects how the market values the entire company.

### One-Line Summary

CapEx on the moat (energy, data centers). OpEx on everything else. GPU CapEx sits uncomfortably in between — own the contracted base load, rent the flex.

---

## 19. Why CapEx Is Value-Accretive in Supply-Constrained Markets

### The Default Mental Model Is Wrong Right Now

Default CapEx assumption: spend $100, asset depreciates, worth less than $100 the moment it's operational. True for most assets in most markets. **AI data center infrastructure breaks this model.**

### The Supply-Demand Imbalance

**Demand side:** Every major enterprise, AI lab, and government scrambling for GPU compute. Global AI infrastructure investment projected $5-7 trillion by 2030. Stargate alone is $500B.

**Supply side — a cascade of sequential bottlenecks, each with multi-year lead times:**

**Power interconnection (3-5 year queue).** Connecting 500 MW to the grid requires utility-scale transmission infrastructure — substations, high-voltage lines, transformer capacity. Can't buy way to front of queue. Crusoe's Abilene exists because they secured power access early — that specific grid interconnection point can't be replicated without years of waiting.

**Permitting (12-18 months, highly variable).** Environmental review, zoning approval, water rights, air quality permits. Single legal challenge adds 12-18 months. Some jurisdictions imposing moratoriums (Northern Virginia has restricted new facilities in some areas due to grid strain).

**Skilled construction labor (fully booked).** Building 140 kW/rack facilities with liquid cooling and redundant power is not standard commercial construction. The contractor pool is small and fully committed. Crusoe's 12-month construction timeline is a competitive advantage because most operators can't match it.

**Equipment lead times (12-24 months).** Electrical switchgear, backup generators, cooling distribution units, power transformers — all stretched as every hyperscaler orders simultaneously.

**GPU supply (allocation-constrained).** NVIDIA allocation preferentially directed to large, committed buyers. 50,000 B200s requires deep relationships and massive purchase commitments.

**Constraints are sequential:** Can't order cooling until building is permitted. Can't energize until grid interconnection is complete. **Total time from "decide to build" to "serving customers" is 2-4 years for most operators.**

### Why Existing Assets Exceed Construction Cost

A completed, operational, fully-powered AI data center represents the crystallized result of navigating every bottleneck. A competitor starting today faces 2-4 years and significant execution risk. Meanwhile demand is here now and growing exponentially.

**Replacement cost exceeds book value.** If it costs $2B and takes 3 years to build 200 MW, and a customer needs 200 MW today, the operational facility is worth more than $2B — because the alternative is waiting 3 years, losing market position, revenue, and competitive advantage.

### The Real Estate Parallel

San Francisco house costs $400K to build but sells for $1.2M. The delta isn't the physical structure — it's the embedded scarcity of entitled, permitted, connected land. Zoning restrictions and building moratoriums constrain supply.

Crusoe's data centers follow the same dynamic. The building may depreciate, but power rights, grid interconnection, permits, and location appreciate as the supply-demand gap widens. Crusoe could theoretically sell Abilene for more than construction cost — the buyer purchases 3+ years of time they don't have to wait.

### How This Manifests Financially

**Balance sheet:** Facility carried at historical cost minus depreciation. If replacement value exceeds book value, balance sheet understates true economic value. Common in infrastructure — Equinix's ~$80B market cap far exceeds book value of property because market prices in scarcity premium.

**M&A and partnerships:** Oracle/Stargate chose Abilene partly because Crusoe had a facility operational in 12 months. Alternative was building from scratch — adding 2-3 years. Time premium gave Crusoe pricing power and favorable lease terms.

**Revenue pricing:** Crusoe can charge above pure cost-plus because customer's alternative isn't "build it cheaper myself" — it's "wait 3 years or pay someone else who also has a scarcity premium."

### When This Dynamic Reverses

**Supply catches up to demand:** Hyperscalers complete massive buildouts, power grid expands, permitting streamlines. Scarcity premium erodes. The current ~$5-7 trillion investment wave is specifically aimed at closing the gap.

**Demand plateaus:** AI scaling laws hit diminishing returns, enterprise adoption slows, regulation constrains compute. Existing capacity becomes abundant.

**Technology shifts:** Inference becomes dramatically more efficient, models shrink, on-device inference captures share. Demand for centralized compute declines.

**The PM's job:** Understand where we are in this cycle. Right now every megawatt of permitted, powered, operational capacity is worth more than construction cost. That's the window Crusoe is racing to exploit — build as much as possible while scarcity premiums persist, use contracted revenue to lock in value before market rebalances.

**The durable question:** Does Crusoe's energy-first approach create structural advantage that persists even after scarcity fades? If yes — because cheap power remains valuable regardless of supply/demand balance — then CapEx is doubly protected: scarcity premium today, cost advantage forever.

---

## 20. The Barbell Approach — Resource Allocation Across the Business

### The Core Principle

Go heavy on both extremes, stay light in the middle. Own the things you're highly certain about (low cost, low flexibility). Rent the things you're uncertain about (high cost, high flexibility). Avoid the middle ground of moderate commitment with moderate flexibility — paying a premium for the worst of both worlds.

Nassim Taleb popularized this in portfolio construction: 90% in ultra-safe treasuries, 10% in highly speculative options. The middle (corporate bonds, balanced funds) gives mediocre returns with hidden tail risk. The barbell gives safety plus asymmetric upside.

For infrastructure businesses, the same logic applies across almost every resource allocation decision.

### GPU Fleet (Clearest Application)

**Owned base load (80%):** GPUs serving contracted customers on 1-3 year commitments. Demand is known. Utilization guaranteed. Finance with equipment-backed debt at 7-9%. Depreciate over 3 years. Run at 85%+ utilization for entire economic life.

**Rented flex layer (20%):** Leased GPUs from hyperscalers or neoclouds. Serve on-demand and spot customers. Scale up for demand spikes. Scale down if demand softens. Return if GPU generation becomes uncompetitive. Pay premium per hour, but zero depreciation risk, zero stranded asset risk.

**The middle to avoid:** Buying GPUs on speculation that demand will materialize. Paying ownership costs (depreciation, cost of capital, power) without revenue certainty. If demand doesn't arrive, burning cash on idle hardware. If demand exceeds purchase, still capacity-constrained. Neither the cost advantage of committed ownership nor the flexibility of rental.

### Data Center Capacity (Crusoe)

**Owned extreme:** Facilities like Abilene with 15-year signed lease. Build, own, finance with project debt. Maximum cost efficiency, maximum capital structure benefit. Zero flexibility needed because demand is contractually guaranteed.

**Rented extreme:** Colocation space in new markets. Lease from existing operator, install own GPUs, validate customer demand. If market works, convert to owned facility. If not, walk away at lease expiry. Colo rent is the cost of optionality.

**Middle to avoid:** Building 500 MW owned facility in market with 100 MW contracted demand and "belief" that 400 MW will come. Committed CapEx without revenue certainty. If only 200 MW materializes, 300 MW of powered, cooled, empty space burning cash.

### Power Sourcing (Crusoe)

**Owned extreme:** Stranded natural gas assets, owned geothermal/hydro capacity. Cost is near-zero marginal (gas was being flared anyway). Supply reliable for life of resource.

**Contracted extreme:** Long-term PPAs (power purchase agreements) with utilities or renewable developers. Not owned but locked in at favorable rates for 10-20 years. Predictable cost, no commodity exposure.

**Flex layer:** Spot grid power for peaks. More expensive per kWh, but available immediately without infrastructure investment.

**Middle to avoid:** Building owned power generation for uncertain demand. A 200 MW gas turbine costs $150-300M. If compute demand doesn't materialize, you're a power plant operator without customers — completely different business.

### Engineering Talent (Both Companies)

**Core team (owned):** Full-time engineers on durable competitive advantages — custom CUDA kernels at Fireworks, data center design at Crusoe, core platform. Deep institutional knowledge. The moat. High fixed cost, but value compounds over years.

**Flex layer (rented):** Contractors for surge capacity — major product launch needs 20 extra frontend engineers for 3 months, new data center build needs specialized construction management team for 12 months. Expensive per hour, zero commitment post-project.

**Middle to avoid:** Hiring 50 full-time engineers for product initiative that might be deprioritized in 6 months. Fixed cost (salary, benefits, equity) without certainty work justifies long-term headcount. Layoffs are expensive (severance, morale, recruiting reputation). Neither deep expertise of long-tenured core team nor flexibility of contractors.

### GPU Capacity Sourcing (Fireworks — Asset-Light)

Fireworks doesn't own data centers, so the barbell manifests differently:

**Committed base:** Long-term reserved capacity from GPU cloud providers (CoreWeave, Crusoe, hyperscalers) at significant discounts. Locked in 1-2 years. Covers predictable base load from established enterprise customers. Enables Fireworks to guarantee SLAs.

**Spot/on-demand flex:** Burst capacity from hyperscaler spot markets. Pay 2-3x more per GPU-hour, but only when needed. Handles spikes from product launches, new model releases, unexpected customer growth.

**Middle to avoid:** 6-month reservations across multiple providers at moderate discounts. Not deep enough discount for long-term commitment benefit, not flexible enough for spot advantage. Managing contracts across too many providers. Complexity without proportional benefit.

### Model Catalog (Fireworks)

**Core models (heavily invested):** Llama 3.1 family, DeepSeek R1, Mixtral, Whisper — the 10-15 models driving 80%+ of token volume. Deep optimization (custom CUDA kernels, architecture-specific tuning), multiple replicas, aggressive caching, premium support. Heavy per-model investment, but volume justifies it.

**Long-tail catalog (light touch):** 80+ additional models with minimal optimization beyond vLLM defaults. May not be loaded until requested (cold start). Exist for breadth and niche use cases.

**Middle to avoid:** Moderate optimization across 50 models. Engineering time on low-traffic models that generate minimal revenue, while under-investing in high-volume models where optimization improvements have 100x more margin impact.

### Customer Segments (Fireworks)

**Self-serve developers (volume, low touch):** Free tier, automatic onboarding, documentation-driven, community support. Cost to acquire near-zero. Most never pay much individually, but create ecosystem gravity and some convert to enterprise.

**Enterprise (high value, high touch):** Dedicated sales, solutions engineering, custom SLAs, compliance, contract negotiation. 6-12 month sales cycle per customer, but yields $500K-5M+ annual contracts with 120%+ net dollar retention.

**Middle to avoid:** Mid-market sales motion with inside sales reps, $50-200K deals, significant negotiation each, but customers without volume to justify sales investment. CAC-to-LTV ratio often worst — too expensive for self-serve, too small for enterprise sales.

### Pricing Strategy (Fireworks)

**Aggressive serverless pricing (acquisition):** Price popular models at or below cost for small-volume users. Loss leader — goal is developer acquisition and ecosystem lock-in. Once developers build on your API, switching costs are real.

**Premium enterprise pricing (monetization):** Custom deployments, guaranteed SLAs, dedicated capacity, multi-LoRA hosting, compliance. Price at 40-60% gross margin. Value is reliability, customization, and support — not raw tokens.

**Middle to avoid:** Moderate pricing across all segments that doesn't attract developers (not cheap enough) and doesn't capture enterprise value (not differentiated enough). Subsidizing enterprise customers who could pay more while failing to compete for developers who need cheaper.

### Why the Barbell Works Especially Well in AI Infrastructure

**Extreme demand uncertainty.** AI adoption curves are steeper and less predictable than most technology. A single model release can shift demand by orders of magnitude in weeks. Need both stable base and rapid flex.

**Rapid technology obsolescence.** GPU generations turn over every 18-24 months with 2-5x performance improvements. Heavy commitment to one generation is risky. Sitting on sidelines misses revenue today. Barbell: commit to proven technology for known demand, maintain flexibility to adopt next-gen for emerging demand.

**Power law economics.** Small number of models, customers, and use cases drive vast majority of revenue and margin. Investing equally across all destroys capital efficiency. Barbell: invest heavily in winners (high-volume models, proven enterprise customers, contracted capacity), maintain cheap options on everything else.

**Unifying principle across all applications:** Commit heavily where you have high certainty. Maintain cheap optionality where you don't. Avoid the expensive middle ground of moderate commitment with moderate uncertainty.

---

## 21. Key Concepts for Interview Readiness

### For Fireworks AI Interviews

**Strategic & Business:**
1. Core thesis: "Inference is the new runtime" — battle shifted from training biggest model to serving cheapest/fastest
2. Software optimization on commodity hardware creates structural margin advantage (15-17x throughput variance on identical hardware)
3. Multi-LoRA serving as key differentiator — hundreds of adapters on single base model
4. Product-model co-design: enterprise data → fine-tuning → product improves → more data → flywheel moat
5. Statistical multiplexing across customers enables 80-90% utilization — the insurance pooling analogy
6. Pricing as land-and-expand weapon: aggressive serverless for acquisition, premium enterprise for monetization
7. Barbell approach to model catalog: deep optimization on top 15 models, light touch on long-tail 80+
8. Barbell approach to GPU sourcing: committed base from cloud providers, spot flex for spikes

**Technical Fluency:**
1. Explain full inference pipeline from API request to streamed token
2. Articulate why decode is memory-bandwidth-bound (weight loading from HBM every token, arithmetic intensity problem)
3. Explain how each optimization compounds: continuous batching → PagedAttention → FlashAttention → quantization → custom kernels → speculative decoding
4. Why output tokens cost more than input tokens (sequential decode vs. parallel prefill)
5. KV cache as the central resource constraint: grows linearly with context, competes with batch slots for GPU memory
6. Prefix caching economics: why cached tokens are discounted 50%
7. SSE streaming and stateless multi-turn conversations

**Financial / Unit Economics:**
1. Cost per token = GPU cost per hour ÷ tokens served per hour
2. GPU utilization is the single most important driver of unit economics
3. Throughput optimization is literally margin expansion
4. Customer segmentation and willingness-to-pay: developer vs. startup vs. enterprise vs. batch
5. Net dollar retention as the key growth metric for inference platforms

### For Crusoe Interviews

**Strategic & Business:**
1. Core thesis: "Energy-first AI infrastructure" — bottleneck is energy, not chips
2. Vertical integration from energy → data center → GPU → cloud platform
3. Energy cost advantage ($0.03 vs $0.10/kWh) flows to margin or competitive pricing: $60M/year advantage at 100 MW
4. Capital structure mastery: project finance (cheap debt against contracts) + venture equity (for software platform)
5. Committed/reserved contracts enable debt capacity — pricing strategy is capital structure enabler
6. Longer tenor from contracted revenue: $127M/year less debt service on $1B borrowed (12yr vs. 5yr)
7. Managed inference as higher-margin layer on top of GPU rental (cannibalization tradeoff)
8. CapEx is value-accretive right now: supply-constrained market means operational facilities worth more than construction cost
9. Barbell approach: own base-load infrastructure (CapEx), rent flex (OpEx), scout new markets with colo before committing
10. IPO positioning: product mix affects valuation multiple (infrastructure vs. software multiples)

**Technical Fluency:**
1. Data center economics: $/kW/month pricing, PUE, liquid cooling requirements for Blackwell-class hardware
2. GPU memory hierarchy: disk → CPU RAM → HBM → SRAM → compute cores
3. Why HBM bandwidth and capacity both matter (H200 vs. H100 for 70B models)
4. NVLink vs. InfiniBand: intra-node (900 GB/s) vs. inter-node (400 Gbps). GB200 NVL72 eliminates inter-node for 405B models
5. Managed inference economics: MemoryAlloy claims (9.9x TTFT, 81% cost reduction) and what they imply about optimization depth

**Financial / Capital Structure:**
1. Reserved contracts as fixed-rate swaps on GPU compute prices (not put options)
2. How contracted revenue enables debt: higher capacity, lower rate, looser covenants, longer tenor
3. DSCR math: how contracted cash flows size debt (1.3-2.0x coverage required)
4. CapEx vs. OpEx framework: CapEx on moat (data centers, energy), OpEx on software and flex capacity
5. Technology obsolescence: data center shell (15-25yr life) vs. GPUs (3-5yr economic life) as separate CapEx categories
6. Depreciation tax shield mechanics: $2B facility over 15-20yr = $100-133M/year non-cash expense
7. The scarcity premium cycle: when it persists, when it fades, and whether Crusoe's energy advantage is durable regardless

### Cross-Cutting PM Skills

1. Connect technical optimization to business metrics (throughput improvement → margin expansion → pricing flexibility)
2. Model unit economics from first principles: cost per token, cost per GPU-hour, breakeven utilization
3. Customer segmentation and willingness-to-pay analysis across developer, mid-market, enterprise segments
4. Capacity planning: demand forecast → GPU requirements → CapEx plan → power requirements → debt sizing
5. Cross-functional fluency: engineering (optimization priorities), finance (pricing, capital structure, unit economics), GTM (packaging, segmentation, competitive positioning)
6. Barbell resource allocation: identify where certainty is high (commit heavily) vs. uncertain (maintain cheap optionality)
7. Product decisions as capital structure decisions: contract design, commitment tiers, and pricing directly affect debt capacity and cost of capital
8. Understand the full chain: technical improvement → throughput gain → unit economics improvement → pricing flexibility → competitive positioning → market share → contracted revenue → debt capacity → lower WACC → further pricing flexibility (the flywheel)
