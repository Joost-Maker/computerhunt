# Sources — where to look and what to ask

Fetch fresh every run. Prices and (increasingly) available silicon change month to month.
Prefer primary sources; synthesise, don't quote at length.

## Primary NL anchor — Tweakers
- **Tweakers Best Buy Guide / "Best Buy systemen"** — the monthly configuraties. The **high-end
  game-pc** tier is the anchor Joost asked for; read its current CPU/GPU/board/RAM picks and use
  them as the sanity check ("is my pick roughly what Tweakers would put in a high-end AM5 build
  this month?"). Search: `tweakers best buy guide desktop` / `tweakers best buy systemen`.
- **Tweakers Pricewatch** — live EUR price + availability per component. This is the price of
  record. Search the exact model, take the lowest reputable-shop price incl. BTW.
- Cross-check retail: Megekko, Azerty, Alternate (sometimes beat Pricewatch's headline shop).

## GPU + Linux/ROCm (only when a GPU is in play)
- **ROCm compatibility matrix + release notes** (rocm.docs.amd.com) — which RDNA gen is officially
  supported this release, and any card-specific gotchas. RDNA4 (gfx1201) support and the
  rocBLASLt lookup bug are the current watch-items; recheck each run.
- **Phoronix** — Linux-side behaviour, idle power, driver/kernel state for the specific card.
- Community inference benchmarks (llama.cpp/Ollama tok/s per card) if the local-LLM angle is being
  weighted this run.

## Memory-market macro (every run — this is the current buy-signal)
The DDR5/NAND shortage is the thing gating the whole build, so read the macro each run and give a
one-line **buy signal**. What's driving it: AI data-center demand (HBM + server DRAM) is soaking up
wafer capacity, pulling consumer DDR5/NAND prices up. The buy signal is when that eases — HBM demand
cooling, DRAM makers (Samsung/SK hynix/Micron) shifting capacity back to consumer, or contract/spot
prices rolling over.
- **TrendForce / DRAMeXchange** — DRAM & NAND contract + spot price trend, and capacity/allocation
  news. The canonical supply-side read. Search: `TrendForce DRAM contract price <month year>` /
  `TrendForce NAND spot price`.
- **Tweakers Pricewatch price history** on the reference kit (2×32 DDR5-6000 CL30) — the retail proxy
  for the macro. Is the curve still climbing, flat, or rolling over month-over-month?
- Headlines on **HBM demand / Samsung / SK hynix / Micron capacity** — a signal that AI memory demand
  is peaking or that consumer allocation is returning. Weigh against hype; look for actual price/
  capacity moves, not forecasts.
- Report as: current trend (↑/→/↓), the one thing that moved this month, and **BUY / WAIT** with a
  one-line why. This is the line Joost is watching — lead the "Open / waiting on" with it.

## CPU / GPU performance + power
- **GamersNexus**, **Hardware Unboxed** — perf, thermals, and (important for the always-on role)
  **power/idle** numbers. Use for any "did the pick flip?" judgement.
- AMD official product pages — spec confirmation (cores, TDP, iGPU presence).

## Query discipline
- Include the current year in time-sensitive queries; verify anything surprising against a second
  source before logging it as fact.
- If Tweakers content doesn't surface in web search, fetch the Tweakers BBG URL directly, or use
  the Megekko "Tweakers Best Buy systemen" mirror as a fallback list, then price on Pricewatch.
- Don't let one perishable data point (a single shop's flash price) flip a pick — note it, but
  weigh availability and Joost's "first-time-right" preference.
