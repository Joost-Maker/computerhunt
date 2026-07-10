# Build Log — bare-metal Linux gaming / local-inference / isolated-worker-VM box

Append-only. Newest run at the top. Prices are NL/EU incl. BTW at run time and go stale fast
(DDR5 + shortage-era GPUs especially) — always re-fetch before trusting. Format defined in
`references/requirements.md § Log entry format`.

---

## Decision — 2026-07-09 — BUY: used pre-built (supersedes HOLD)
Reversed the hold. **Buying a used, near-new ("overcompleet") pre-built** off the NL used market
(Coevorden) instead of a fresh DIY box or the Framework Strix Halo.

**The machine:**
- **CPU:** Ryzen 9 9950X3D (16c/32t) — **new, with invoice** (matches the fixed spec exactly).
- **GPU:** RX 9070 XT 16 GB Steel Legend (RDNA4) — **new, with invoice**. *Interim* card.
- **RAM:** 64 GB DDR5-6000 (2×32 Kingston Fury Renegade) — the shortage part, **bought at used price**.
- **Board:** ASUS ROG Strix X670E-A Gaming WiFi (4-DIMM, 2.5GbE). **PSU:** Cooler Master 1250W (+12VHPWR).
- **Cooling:** Thermalright 360 AIO (accepted deviation from the air-cooling preference).
- **Case:** Fractal Design North (white) — the preferred case.
- **Storage:** 512 GB M.2 (OS) + a **spare 1 TB NVMe** Joost already owns (VM/inference store). No add-on buy.
- **Price:** asking **€2,650**; countering **€2,500** cash + prompt pickup. Effective all-in ~€2,500–2,650.

**Rationale:**
- **Sidesteps the DDR5/NAND shortage** that forced the hold — 64 GB DDR5 (~€1,000 new today) comes
  baked into a used-price whole system. Rebuilding this new today ≈ €3,300; €2,650 ≈ 20% off near-new.
- **Local AI reclassified: "fun but experimental," not the driver.** So VRAM capacity is no longer
  worth paying up for. **16 GB is acceptable now** (≤~30B / 30B-MoE on RDNA4 ROCm). When GPU prices
  cool, **swap to a 24–32 GB card — sell the 9070 XT, write the upgrade off on the business.** The
  1250W + 12VHPWR PSU is sized for any future GPU.
- Gets the **exact spec CPU + preferred case + warranty** on the two new parts, for less than a new
  Framework 64 GB (~€2,849) and far less than the DIY R9700 build (~€4,100 in the shortage).

**Supersedes:** the R9700 32 GB lean and the Framework Strix Halo consideration (both were
inference-first plays; inference is now explicitly secondary). The 7900 XTX "current pick" is retired
in favour of the 9070 XT as the interim card.

**Open / next:** at pickup — verify invoices (warranty transfer), test before paying, confirm EXPO
6000. Going forward the watch flips from "whole-build memory buy signal" to a **GPU-swap buy signal**:
when a 24–32 GB card reaches sane value, that's the upgrade trigger. Retarget the monthly routine to
track that once the purchase closes.

---

## Decision — 2026-07-09 — HOLD the purchase + scope trim
Joost's calls this session:
- **HOLD the build.** DDR5/NAND shortage has 64 GB DDR5 ≈ **€1,000** and NVMe ≈ **€300/TB** — memory +
  storage alone ~€1,900, more than the GPU. A full-spec box is ~€4,100–4,700 right now, so a ~€2,500
  target is off the table. Per the "if RAM is spiking, wait" rule: **wait, watch prices, revisit on a
  future run.** When buying, expand memory in halves (32 GB + 1 TB now, add the rest as prices ease).
- **Preferred GPU when buying = Radeon AI PRO R9700 32 GB** (RDNA4, seen in stock @ **€1,579**,
  Galaxus NL). Rationale: company budget (laptop replacement, so ~€3k+ is justified vs a mobile
  workstation); 32 GB future-proofs local inference (70B-class) and, in a RAM shortage, VRAM
  substitutes for pricey system RAM. Gaming = RX 9070 XT class (same Navi 48 die) — clears QHD ~100
  easily, better RT than 7900 XTX; caveats: workstation blower (louder) + full RDNA4 Linux support
  native in Ubuntu 26.04 (24.04 needs a recent HWE kernel/Mesa). 7900 XTX 24 GB stays the on-budget
  fallback if the ceiling is hard.
- **Scope trim (token saving):** stop repricing **PSU, cooler, case** — commodity accessories,
  always in stock. Future runs carry them as one flat ~€350 line and price only CPU/GPU/Mobo/RAM/
  Storage. (requirements.md + SKILL.md updated.)

---

## Run 003 — 2026-07-09
**Objective this run:** Routine monthly reprice. Catch price moves / new silicon / ROCm changes
since Run 002; re-apply the GPU value rule; make the buy-now-vs-wait call on the DDR5-6000 CL30
2×32 kit. Tweakers Pricewatch was unreachable from this environment again (same as Run 002) —
priced on Megekko.nl (live-fetched, in-stock, incl. BTW) with Geizhals/GPUTracker/eBay cross-checks
per the fallback rule in requirements.md. No fabricated Pricewatch links.

**GPU judgement table** (NL/EU, live July 2026):

| GPU | VRAM | Gen (yr) | ~Price new / used | Gaming raster | Local-LLM fit | SW | Verdict (per rule) |
|---|---|---|---|---|---|---|---|
| **RX 7900 XTX** | **24 GB** | RDNA3 ('22) | ~€899–1199 new (EU, patchy NL retail stock) / **~€675–820 used** (eBay/GPUTracker) | very high, 4K-capable | ~30B Q4 | ROCm mature (gfx1100) | ✅ **PICK** — still best VRAM/€ |
| RTX 3090 | 24 GB | Ampere ('20) | EOL / ~€900–1000 used (Marktplaats NL) | ~47% < 7900 XTX | ~30B Q4 | CUDA (most mature) | ❌ older, slower, same ballpark price used — CUDA is the only edge |
| RTX 4090 | 24 GB | Ada ('22) | EOL-scarce, well over €1800 | top-tier | 24 GB, CUDA | CUDA | ❌ still absurd scarcity pricing |
| RX 9070 XT | 16 GB | RDNA4 ('25) | ~€719–849 (Megekko, in stock) | excellent 1440p/entry-4K | ≤~20B Q4 | ROCm 7.2.1 — gfx1201 memory-coherency bug **now fixed** | ▲ only if local-LLM stays light |
| RTX 5070 Ti | 16 GB | Blackwell ('25) | ~€1099–1199 (Megekko) — up from ~€700–800 last run | excellent + RT/DLSS4 | ≤~20B, CUDA | CUDA | ▲ 16 GB *and* now pricier — weaker case than last run |
| RTX 5090 | 32 GB | Blackwell ('25) | ~€2500+ | fastest | ~70B-ish, CUDA | CUDA | ❌ over the €2k VRAM cap |
| Radeon AI PRO R9700 | 32 GB | RDNA4 ('26) | **~€1420–1500 new (EU, Geizhals)** — firm price now, was "premium/€1300+" est. last run | fine (not the point) | 32 GB, ROCm | ROCm | △ high-VRAM escape hatch; ~€600–800 premium over the 7900 XTX for 8 GB more — still niche |

**Verdict:** **RX 7900 XTX 24 GB, unchanged.** ROCm 7.2.1 fixed the gfx1201 (RDNA4) memory-coherency
bug, so the 9070 XT's software story keeps improving — but it's still 16 GB, and that's the whole
rule. RTX 5070 Ti got *more* expensive since Run 002 (16 GB for ~€1,150 now), weakening its case
further. RTX 3090 used has drifted down to roughly parity with 7900 XTX used, but it's older,
slower, and CUDA is its only edge — not enough to flip. R9700 32 GB now has solid EU pricing
(~€1,450) instead of an estimate; it's a real, sanely-priced 32 GB option if local models ever
outgrow 24 GB, but paying ~€700 more for 8 GB extra VRAM isn't justified today. Buy used
(~€675–820) for value or new (~€899–1199, shop around EU-wide — NL retail stock is patchy right
now) for warranty.

**Full build (this run, Megekko.nl live prices incl. BTW):**

| Component | Pick | Price (EUR) | Link |
|---|---|---|---|
| CPU | Ryzen 9 9950X3D (16c/32t) | **€609** | megekko.nl/product/8200/183019 (AMD-Ryzen-9-9950X3D-Processor) |
| GPU | RX 7900 XTX 24GB | ~€675–820 used / ~€899–1199 new | gputracker.eu (RX 7900 XTX search); geizhals.eu cross-check — NL retail (Megekko/Azerty) largely out of stock, buy EU-wide |
| Mobo | ASRock B850 Pro-A, ATX, 4-DIMM, 2.5GbE | **€190.95** | megekko.nl/product/8197/262433 (ASRock-B850-Pro-A-moederbord) |
| RAM | Kingston FURY Beast 2×32GB DDR5-6000 CL30 EXPO | **€959** (Corsair CMH64GX5M2B6000Z30 equivalent: €1,079) | megekko.nl/product/7962/962099 (Kingston-...-KF560C30BBEK2-64) |
| Storage | Samsung 990 PRO 1TB + 2TB (heatsink) NVMe Gen4 | €229.90 + €419 = **€649** | megekko.nl/product/5093/293095 ; megekko.nl/product/5093/941458 |
| PSU | Cooler Master MWE Gold 850 V3, ATX3.1 Gold | **€85** | megekko.nl/product/4186/968907 (Cooler-Master-MWE-Gold-850-V3) |
| Cooler | Peerless Assassin 120 SE ARGB | **€40.95** | megekko.nl/product/1994/1083171 |
| Case | Fractal North, Charcoal Black TG Dark | **€119.90** | megekko.nl/product/2013/952740 |
| **Total** | (used GPU) | **~€3,254–3,399** | |
| **Total** | (new GPU) | **~€3,478–3,778** | |

**Idle estimate (always-on state):** ~55–75 W (unchanged from prior directional estimate — no
new idle-power data surfaced this run; still on the Run 002/001 TODO to pull GamersNexus/Phoronix
numbers for the exact B850 + 9950X3D + 7900 XTX combo).

**Changed vs Run 002:**
- **RAM (the volatile line, as flagged):** 2×32 DDR5-6000 CL30 confirmed at **€959–1,079** live on
  Megekko — up **~4×** from the ~€200–270 Run 002 estimate. This is not a shop-specific spike:
  industry reporting (TrendForce, Tom's Hardware, Wccftech) confirms a structural DDR5 shortage
  driven by AI/HBM fab reallocation (Samsung/SK Hynix cutting standard-DRAM output for HBM), with
  consumer DDR5 module prices up ~3.5–4× since early 2025 and **no relief expected before 2027** —
  some analysts expect prices to keep climbing (potentially doubling again) through end of 2026.
  **This inverts the usual "wait it out" instinct: waiting is more likely to cost more, not less.**
  See "buy-now-vs-wait" verdict below.
- **Storage, newly volatile too:** same root cause (NAND flash, not DRAM, but the same AI-driven
  fab-capacity squeeze) hit SSDs. 1TB+2TB Gen4 NVMe now **€649**, vs ~€180 assumed in Run 001/002 —
  a ~3.6× jump. This wasn't flagged as a watch item before; it is now.
- **CPU down slightly:** 9950X3D at €609 (Megekko), down from ~€649–700 in Run 002 — the only line
  moving the right direction.
- **GPU pick unchanged** (RX 7900 XTX 24GB) — price roughly flat to a touch higher; NL retail stock
  for new units got patchier (Megekko/Azerty both showing "not available from any supplier" on
  several SKUs) but the card is readily available EU-wide (GPUTracker, Mindfactory) and on the used
  market, so no availability-driven downgrade needed.
- **ROCm:** 7.2.1 fixed the gfx1201 (RDNA4) memory-coherency bug — worth noting for the 9070 XT
  path, doesn't change the pick.
- **R9700 32GB:** went from a rough "€1,300+" estimate to a firm ~€1,420–1,500 EU street price —
  confirmed still a deliberate-upgrade-only option, not the default.
- **Build total roughly 60–75% higher than Run 002's ~€1,830–2,350** — driven almost entirely by
  RAM + storage (structural component shortages), not by any compute-pick change. CPU/GPU/mobo/
  case/PSU/cooler collectively are flat-to-cheaper than Run 002.

**Buy-now-vs-wait verdict (RAM, the line Joost asked about specifically):** **Buy now if the 64GB
target is genuinely needed soon.** This isn't a transient flash-sale spike where patience is
rewarded — it's a structural AI-driven DRAM/NAND shortage that every tracked forecast (TrendForce,
IDC, Tom's Hardware) expects to *worsen or plateau-at-elevated-levels* through the rest of 2026,
with normalization not expected before 2027. Waiting 3–6 months is more likely to cost *more* than
today's €959–1,079, not less. If the €700+ premium over Run 002's estimate is a hard budget
blocker, the fallback is to **provisionally buy a 2×16GB (32GB) kit now** (roughly a third of the
64GB kit's price) and treat the jump to 64GB as a deferred upgrade once DRAM pricing normalizes —
but that means temporarily missing the 64GB hard constraint, so flagging it as a tradeoff rather
than deciding it unilaterally. Storage: same logic, lower stakes — 1TB alone (€230) covers the host
boot drive; the 2TB VM/bulk drive can be deferred without touching a hard constraint.

**Open / waiting on:** Idle-power numbers for the exact B850+9950X3D+7900 XTX combo (still not
pulled from GamersNexus/Phoronix — carry to next rerun). Decide used-vs-new 7900 XTX (warranty vs
~€300–400 saving, now a bit wider than Run 002's ~€275). Decide RAM capacity now (64GB @ ~€1,000)
vs staged (32GB now, 64GB later) given the shortage outlook above — Joost's call, not resolved by
this run.

**Snapshot:** builds/2026-07-09-run-003.md

---

## Run 002 — 2026-07-09
**Objective this run:** GPU choice reopened to NVIDIA (single-dGPU bare-metal host); apply the
explicit value rule (last-gen+high-VRAM > new-gen+low-VRAM; equal VRAM → newer; no ~€2k VRAM card).
Produce judgement tables with real price links.

**GPU judgement table** (NL/EU, mid-2026; prices directional, tighten on rerun):

| GPU | VRAM | Gen (yr) | ~Price new / used | Gaming raster | Local-LLM fit | SW | Verdict (per rule) |
|---|---|---|---|---|---|---|---|
| **RX 7900 XTX** | **24 GB** | RDNA3 (’22) | ~€950–1240 / **~€675 used** | very high, 4K-capable | ~30B Q4 | ROCm 7.2 mature (gfx1100) | ✅ **PICK** — most VRAM/€, newer & faster than 3090 |
| RTX 3090 | 24 GB | Ampere (’20) | EOL / ~€1150–1250 used | ~47% < 7900XTX | ~30B Q4 | CUDA (most mature) | ❌ older, slower, *pricier* used; only CUDA wins |
| RTX 4090 | 24 GB | Ada (’22) | EOL-scarce ~€1800–2500 | top-tier | 24 GB, CUDA | CUDA | ❌ absurd price (EOL scarcity) |
| RX 9070 XT | 16 GB | RDNA4 (’25) | ~€630–730 | excellent 1440p/entry-4K | ≤~20B Q4 | ROCm 7.2 (gfx1201, maturing) | ▲ only if local-LLM stays light |
| RTX 5070 Ti | 16 GB | Blackwell (’25) | ~€700–800 | excellent + RT/DLSS4 | ≤~20B, CUDA | CUDA | ▲ NVIDIA 16GB w/ CUDA, but still 16 GB |
| RTX 5090 | 32 GB | Blackwell (’25) | ~€2500–3000 | fastest | ~70B-ish, CUDA | CUDA | ❌ over the €2k VRAM cap |
| Radeon AI PRO R9700 | 32 GB | RDNA4 (’26) | ~€1300+ (workstation) | fine (not the point) | 32 GB, ROCm | ROCm | △ high-VRAM escape hatch, niche |

**Verdict:** **RX 7900 XTX 24 GB.** Reopening to NVIDIA did *not* flip the pick — every NVIDIA >16 GB
card is currently bad value (used 3090 has risen above the 7900 XTX; 4090 EOL-scarce; 5090 over
budget). The 7900 XTX gives the most VRAM/€, is newer & faster than the 3090, and ROCm 7.2 is mature
on gfx1100. Buy used (~€675) for value or new (~€950+) for warranty.

**Full build (this run):**

| Component | Pick | ~Price (EUR) | Link |
|---|---|---|---|
| CPU | Ryzen 9 9950X3D (16c/32t) | ~€649–700 | pangoly.com/en/price-history/amd-ryzen-9-9950x3d (set NL) |
| GPU | RX 7900 XTX 24GB | ~€675 used / ~€950+ new | bestvaluegpu.com/en-eu/history/new-and-used-rx-7900-xtx-price-history-and-specs/ ; gputracker.eu (7900 XTX) |
| Mobo | B850 ATX, 4-DIMM, 2.5GbE | ~€180–220 | (rerun: Pricewatch) |
| RAM | 2×32 DDR5-6000 CL30 EXPO | ~€200–270 (volatile) | (rerun: Pricewatch) |
| Storage | 1TB + 2TB Gen4 NVMe | ~€180 | (rerun: Pricewatch) |
| PSU | 850W ATX3.1 Gold/Plat | ~€120–140 | (rerun: Pricewatch) |
| Cooler | Peerless Assassin 120 SE / NH-D15 | ~€40–110 | (rerun: Pricewatch) |
| Case | Fractal North / XL | ~€140–180 | (rerun: Pricewatch) |
| **Total** | (used GPU) | **~€1,830–2,050** | |
| **Total** | (new GPU) | **~€2,100–2,350** | |

**Changed vs Run 001:** GPU constraint reopened to NVIDIA → evaluated, but pick stays RX 7900 XTX
24 GB on the value rule (NVIDIA >16 GB all overpriced in the 2026 shortage). Confirmed 9950X3D now
~€649–700 (down from €800 MSRP). Established the value rule and the tables-with-links output format.

**Open / waiting on:** Tweakers Pricewatch deep-links couldn't be surfaced in the web-chat session
(search didn't return tweakers.net); attach them on the next Claude Code rerun. DDR5 kit price =
buy-now-vs-wait call on rerun. Decide used-vs-new 7900 XTX (warranty vs ~€275 saving).

**Snapshot:** (this table is the snapshot for Run 002)

---

## Amendment 2 — 2026-07-09 — design & spec settled
Converged after Joost worked through the isolation/GPU mechanics:
- **Architecture final:** one **bare-metal Linux host** = daily driver + gaming + **native local
  inference**; plus **one network-isolated KVM VM** for the Claude/agent worker (GPU-less, egress-
  only). **No Proxmox, no GPU passthrough** anywhere. Rationale: a PCI GPU binds to one driver at a
  time; passthrough only pays off if a VM needs the GPU, and the worker never does. Two AI clients,
  split by need: inference → host (owns GPU + ROCm), agent worker → isolated VM (CPU+RAM+net).
- **RAM policy:** virtio-balloon on, VM capped ~8–16 GB; hard-reclaim for big local jobs = shut the
  VM (deterministic; worker state lives in Notion/repo/API, not guest RAM). Most inference memory is
  VRAM anyway.
- **CPU settled:** 9950X3D (16c/32t) — "fat, fast, lots of threads" + X3D gaming. 9800X3D dropped.
- **DDR clarified:** 4 slots don't disable anything and don't add channels (AM5 = dual-channel
  regardless). 4× DIMMs just halve signal integrity headroom → speed falls (6000 → ~3600–4800).
  Path to 128 GB at speed = **2×64 swap**, not filling all four. Empty slots = flexibility, not the
  upgrade plan.
- **GPU rule updated:** local inference is now a confirmed native workload → VRAM weighted up.
  **Default lean = 7900 XTX 24 GB** (mature ROCm); 9070 XT 16 GB only if gaming dominates and
  inference stays light; R9700 32 GB if models outgrow 24. No IOMMU constraint on the board (no
  passthrough) → B850 default.
(architecture.md rewritten; requirements.md + SKILL.md updated; run-001 snapshot relabeled.)

---

## Amendment — 2026-07-09 — architecture default changed
Joost pushed back on the Proxmox-passthrough default. Corrected: the AI VM is a remote agent
client (network+CPU+RAM), not a GPU consumer, so virtualizing the gaming environment buys nothing.
**New default = bare-metal Linux host (native gaming, GPU on host) + AI workload in a KVM VM on an
isolated egress-only libvirt network.** Proxmox+passthrough demoted to a documented alternative for
server-first / Windows-gaming-VM / snapshottable-gaming / peer-OS cases only.
**Parts impact:** "clean IOMMU groups" drops from a hard board constraint → B850 value board is the
sensible default; X870E-Creator now justified only by its dual/10GbE NIC + USB4. All other picks in
Run 001 unchanged. (architecture.md + requirements.md + SKILL.md updated accordingly.)

---

## Run 001 — 2026-07-09
**Objective this run:** Establish the baseline build + rationale from a clean sheet, anchored on
the Tweakers high-end AM5 game-pc tier, Zen 5 X3D CPUs, and the RDNA3-vs-RDNA4 ROCm picture.
**Configuration(s) priced:** Value/gaming, Do-everything, Local-AI-max (GPU swap on the latter).

Prices below are indicative July-2026 NL ranges (to be tightened to exact Pricewatch figures on
the next live rerun); ranges reflect genuine market volatility, esp. DDR5.

| Component | Pick | Price (EUR) | Rationale |
|---|---|---|---|
| CPU (primary) | Ryzen 9 9950X3D | ~700–740 | 16c/32t for concurrent Proxmox host + gaming VM + services; X3D on gaming CCD = top-tier gaming in the passthrough guest. |
| CPU (lean alt) | Ryzen 7 9800X3D | ~470–500 | Best gaming/€, single CCD → lower idle (better 24/7). 8c is tighter under simultaneous host+gaming load. |
| GPU (default) | Radeon RX 9070 XT 16GB (RDNA4) | ~680–730 | Best current AMD gaming card; lower idle than 7900 XTX (matters 24/7); now official in ROCm 7.2. 16GB caps local models ~20B dense/30B MoE Q4; RDNA4 ROCm still maturing (gfx1201 → ROCm Docker / llama.cpp+Vulkan). |
| GPU (local-AI-max alt) | Radeon RX 7900 XTX 24GB (RDNA3) | ~750–900 | Most mature ROCm (gfx1100, no env hacks), 24GB fits bigger models. Downsides: last-gen, higher idle, used-market. Pick only if local-LLM headroom > idle efficiency. |
| GPU (high-VRAM option) | Radeon AI PRO R9700 32GB (RDNA4) | premium | Surface if AI-VM ambition grows: 32GB in x86 without Strix Halo. Workstation card. |
| Mobo (primary) | X870(E) ATX, 4-DIMM, clean IOMMU, 2.5/10GbE | ~250–450 | Passthrough-friendly IOMMU + dual/faster NIC for the LAN-isolation design. ProArt X870E-Creator = top I/O but pricey. |
| Mobo (value) | B850 ATX, 4-DIMM, 2.5GbE (TUF/Steel Legend class) | ~180–220 | Good-enough IOMMU + NIC at half the price; verify groups before buying. |
| RAM | 2×32GB DDR5-6000 CL30 EXPO | ~200–270 (volatile) | AM5/X3D sweet spot (fclk 2000, 1:1). Leaves 2 slots free. Future 128GB via 4 DIMMs drops speed — prefer 2×48/2×64 modules for capacity-at-speed. |
| Storage | 1TB NVMe (host) + 2TB Gen4 NVMe (VM store) | ~60 + ~120 | Split host vs VM store; add capacity as needed. |
| PSU | 850W ATX 3.1 Gold/Platinum | ~120–140 | Low-load efficiency for 24/7; 850W covers 7900 XTX transients too. |
| Cooler | Big air (Peerless Assassin / NH-D15) | ~40–110 | Silent 24/7, no pump wear; air handles X3D in eco/CO. |
| Case | Fractal North / North XL | ~140–180 | Established aesthetic, quiet, airflow. XL if avoiding GPU-length worry. |

**Totals (indicative):**
- Value/gaming (9800X3D + 9070 XT + B850): ~€1,600–1,750
- Do-everything (9950X3D + 9070 XT + X870): ~€2,050–2,300
- Local-AI-max (swap 9070 XT → 7900 XTX 24GB): roughly price-neutral to +€100

**Idle estimate (always-on state, dGPU parked, iGPU host):** ~50–70 W on AM5; ~10–15 W lower with
the 8-core single-CCD 9800X3D and RDNA4 vs RDNA3.

**Changed vs previous run:** n/a (baseline).

**Open / waiting on:**
- DDR5 is the volatile line — on the next live rerun, pull exact Pricewatch price for a 2×32
  DDR5-6000 CL30 kit and decide "buy now vs wait".
- Confirm exact X870/B850 board with verified clean IOMMU groups (Phoronix/passthrough reports).
- Decide the CPU fork (16-core do-everything vs 8-core lean) — depends on how heavy the concurrent
  gaming-while-host-busy scenario really is for Joost.
- Decide GPU fork by weighting: idle efficiency (→9070 XT) vs local-LLM headroom (→7900 XTX 24GB /
  R9700 32GB).

**Snapshot:** builds/2026-07-09-run-001.md
