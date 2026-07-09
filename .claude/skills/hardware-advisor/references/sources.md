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
