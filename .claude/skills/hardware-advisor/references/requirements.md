# Requirements — the fixed spec

This is the thing being priced. Hard constraints are non-negotiable unless Joost changes them
here. Update this file (and note it in BUILD-LOG.md) whenever a constraint changes.

## Roles (why this machine exists)
1. **Bare-metal Linux host** — daily driver + gaming + local inference, all native. Runs long and
   often; efficiency and silence matter, but it is not a thin always-on appliance.
2. **Network-isolated KVM VM** — the Claude/agent worker for longer API-driven jobs. Needs CPU +
   RAM + egress-only network, **never the GPU**. A real VM (not a container) for code-execution
   isolation off the LAN.
3. **Local inference** — runs **natively on the host** (full ROCm, full VRAM), not in the VM.
4. **Gaming** — native on the host. **Target: QHD (1440p) ~100 FPS. 4K is NOT a target.** This
   matters: at 1440p/100, essentially any modern upper-mid GPU (9070 XT / 7900 XTX / 5070 Ti-class)
   is more than enough, so **gaming no longer constrains the GPU choice** — the GPU is chosen almost
   entirely on VRAM-for-inference + value. Don't pay for 4K-class gaming muscle that goes unused.
   Shares the one GPU with local inference by time (don't run heavy inference mid-game).

## Hard constraints
- **CPU: AMD, socket AM5, thread-heavy.** Fat, fast, lots of threads — 16c/32t class. The host
  juggles games + local inference + the isolated worker, so cores are wanted. (AM5 committed
  through 2027+, so the platform carries forward.)
- **GPU: AMD *or* NVIDIA.** No longer AMD-exclusive — it's a single-dGPU bare-metal host, so the
  Linux/passthrough reason for AMD-only is gone (Pop!_OS ships NVIDIA proprietary drivers
  first-class). CUDA is still more mature than ROCm for local inference, so NVIDIA is genuinely back
  on the table — **but** see the value rule; in the current market NVIDIA's >16GB cards are all
  poor value.
- **RAM: 64 GB DDR5 as 2×32 GB**, on a **4-DIMM board**, running EXPO DDR5-6000 CL30. The 2 spare
  slots are *flexibility, not the upgrade plan* — see the DDR note below.
- **Motherboard: 4× DIMM slots** and **≥2.5GbE**. No IOMMU requirement (no passthrough in this
  design) — don't pay up for it.
- **OS target: Linux** host (Pop!_OS / Ubuntu 24.04 — also what ROCm officially supports for native
  local inference).

### DDR note — why NOT to fill all 4 slots
4 slots do **not** disable anything and do **not** add channels (AM5 is dual-channel regardless;
4 slots = 2 channels × 2). Populating 4 DIMMs just doubles the electrical load per channel, and
DDR5 clocks are high enough that stable speed drops hard: 2× DIMMs do DDR5-6000 CL30, but 4× DIMMs
(esp. dual-rank 32GB) realistically fall to ~3600–4800, sometimes on a latency-hurting 1:2 ratio.
So the path to 128 GB at speed is a **2×64 GB swap** (or 2×48=96 GB), staying at 2 sticks — *not*
adding a second pair. Buy a 2×32 kit now without over-valuing the empty slots.

## Soft preferences (Joost-coded defaults)
- **Air cooling over AIO** for a 24/7 box — silent, no pump wear. X3D chips run hot but air
  handles them in eco / curve-optimizer.
- **Efficient PSU** (Gold/Platinum, good low-load efficiency, ATX 3.1) — idle efficiency matters
  more here than headline wattage.
- **Case: Fractal North family** (wood front, quiet, airflow) — established aesthetic. Check GPU
  length clearance on the regular North; North XL removes the worry.
- **First-time-right, auditable, not over-engineered.** Prefer proven parts with good Linux track
  record (Phoronix-validated) over bleeding edge.
- **Best price is a genuine objective.** If a component (usually RAM) is spiking, saying "wait"
  is a valid recommendation.

## Settled: CPU
Not a fork anymore. **Ryzen 9 9950X3D (16c/32t)** — the thread count for host + games + inference +
worker, with X3D on the gaming CCD so gaming isn't sacrificed. The 8-core 9800X3D is out (Joost
chose the thread-heavy direction). The 9850X3D (~3% faster gaming, premium) isn't worth it; the
halo 9950X3D2 Dual Edition (~$899-class) is a mention-only escape hatch. Threadripper is the true
"lots of threads" ceiling but wrong socket / no gaming value / multiples of the price — ruled out.

## The GPU rule (explicit — apply every run, both vendors)
Gaming never dominates (little time to game) and the target is only **QHD ~100 FPS, not 4K** — so
every candidate here clears the gaming bar and gaming does not drive the choice. Local inference is
a real native-host workload; and **do not overpay for VRAM** (no ~€2000 card just because it has
lots of RAM). So the preference is lexicographic:
1. **Maximize VRAM within a sane budget.** Prefer **last-gen + high VRAM** over **new-gen + lower
   VRAM**. (24 GB beats 16 GB even if the 24 GB card is a generation older.)
2. **At equal VRAM, prefer the newer generation.**
3. **Hard budget instinct:** reject cards that are expensive *because* of VRAM (RTX 5090 32 GB at
   €2,500+; EOL-scarce RTX 4090). 32 GB only via something sensibly priced (e.g. R9700 workstation)
   if models ever outgrow 24 GB.

CUDA vs ROCm still matters for inference, but note the current-market reality: NVIDIA's only >16 GB
consumer cards (3090 / 4090 / 5090) are all bad value right now (used 3090 has *risen*, 4090 is EOL-
scarce, 5090 is over budget), so the rule usually lands on **24 GB AMD (RX 7900 XTX)** despite the
CUDA edge. Re-verify each run — if a cheap 24 GB CUDA card appears, the rule may flip to it.

## Output format (the final result Joost wants)
Deliver **clear judgement tables of price + performance**, not prose walls. At minimum:
- A **GPU judgement table**: Card | VRAM | Gen (year) | ~NL price (new / used) | Gaming (raster) |
  Local-LLM fit | Software (CUDA/ROCm) | Rule verdict. Show the rejected options too so the
  reasoning is visible, not just the pick.
- A **full-build table** with per-line price and a total, plus any GPU scenario split.
- **Real price links, Tweakers Pricewatch preferred.** On a Claude Code rerun (full web access),
  attach exact `tweakers.net/pricewatch/...` deep-links per component. If Pricewatch can't be
  reached in a given environment, use real fetched alternatives (gputracker.eu, pangoly NL,
  bestvaluegpu EU) and say so — **never fabricate a Pricewatch URL.**

## Log entry format (append to BUILD-LOG.md)
```
## Run NNN — YYYY-MM-DD
**Objective this run:** <one line: what was being optimized / what triggered the run>
**Configuration(s) priced:** <e.g. Value/gaming, Do-everything, Local-AI-max>

| Component | Pick | Price (EUR) | Rationale |
|---|---|---|---|
| CPU | ... | ... | ... |
| GPU | ... | ... | ... |
| Mobo | ... | ... | ... |
| RAM | ... | ... | ... |
| Storage | ... | ... | ... |
| PSU | ... | ... | ... |
| Cooler | ... | ... | ... |
| Case | ... | ... | ... |
| **Total** | | **...** | |

**Idle estimate (always-on state):** ~XX W
**Changed vs previous run:** <price moves, silicon changes, picks that flipped + why>
**Open / waiting on:** <e.g. hold RAM until DDR5-6000 32GB kit < €X>
**Snapshot:** builds/YYYY-MM-DD-run-NNN.md
```
