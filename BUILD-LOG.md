# Build Log — bare-metal Linux gaming / local-inference / isolated-worker-VM box

Append-only. Newest run at the top. Prices are NL/EU incl. BTW at run time and go stale fast
(DDR5 + shortage-era GPUs especially) — always re-fetch before trusting. Format defined in
`references/requirements.md § Log entry format`.

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
