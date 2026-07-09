---
name: hardware-advisor
description: Rerunnable AMD+Linux gaming / local-inference / isolated-agent-VM PC build advisor for Joost. Fetches the live Tweakers Best Buy Guide and Pricewatch, re-prices a build against fixed requirements (AMD AM5, thread-heavy CPU, AMD or NVIDIA GPU, 64GB DDR5 on a 4-DIMM board, bare-metal Linux host running games + native local inference + one network-isolated KVM VM for the Claude worker), and appends every decision with rationale to a persistent build log. Use this whenever Joost says "re-run the build", "check the PC build", "what's the best AMD build now", "reprice the workstation", "hardware advisor", "spec my machine", or asks for the current best-price component pick for the gaming / AI box. Also trigger when he mentions the Tweakers Best Buy Guide in the context of building a PC, or wants to log a component decision. Do NOT use for laptops or for non-AMD builds.
---

# Hardware Advisor

A rerunnable decision tool for one specific machine: Joost's always-on home box that is
a bare-metal Linux (a) daily driver + gaming rig, (b) native local-inference machine, and (c)
host for one network-isolated KVM VM running the Claude/agent worker. No hypervisor distro, no
GPU passthrough. It re-prices against fixed requirements, records every choice + rationale, and
shows what changed since last run.

The point is repeatability: prices (especially DDR5) move constantly, and Tweakers refreshes
its Best Buy systemen monthly. So **never trust prices baked into old runs** — fetch fresh
every time, then diff against the last logged run.

## Config block

```
# STATE_DIR resolution (in priority order):
#  1. If running inside the hardware-advisor repo (a BUILD-LOG.md exists at the repo root that
#     contains this skill under .claude/skills/) -> STATE_DIR = repo root.  ← Claude Code web / GitHub
#  2. Else (personal install) -> STATE_DIR = ~/.claude/skills/hardware-advisor
#  3. If that's read-only -> ~/cowork/hardware-advisor/ (and say so).
LOG:        {STATE_DIR}/BUILD-LOG.md            # append-only decision log (git-versioned in the repo)
BUILDS:     {STATE_DIR}/builds/                  # one dated snapshot per run
CURRENCY:   EUR, NL retail (incl. BTW)
RETAILERS:  Tweakers Pricewatch primary; Megekko / Azerty / Alternate for cross-check
GIT:        if STATE_DIR is a git repo, commit the new log entry + snapshot after each run
            (message: "run NNN: <one-line objective>"); push if an 'origin' remote is set.
NOTION:     after the commit, publish the full run as one page in the "Hardware Advisor — Build Runs"
            database (data_source_id 65de7c0c-e1e0-4383-9c0d-194b6f131195). See references/connectors.md.
EMAIL:      then draft a terse summary email to joost@barnebies.com linking the Notion page.
            NOTE: the Gmail connector is DRAFT-ONLY (no send) — it lands in Drafts, Joost taps Send.
CONNECTORS: the Notion/Gmail steps run only when those connectors are attached (Claude Code web/
            desktop/GitHub). If one is absent, skip it and say so — never fail the run over it.
```

## When you run this skill

Do these in order. It is a research + judgement + logging loop, not a lookup.

### 1. Load the fixed inputs
Read `references/requirements.md` (the hard constraints + soft prefs + role definitions) and
`references/architecture.md` (the bare-metal-host + isolated-VM design that is already
decided and should NOT be re-litigated each run). These change rarely; if Joost states a new
constraint, update the reference file and note it in the log.

### 2. Read the last run
Open `BUILD-LOG.md` and the newest file in `builds/`. Note the last chosen parts and prices so
you can diff. If there is no prior run, this is run 001.

### 3. Fetch live data
Follow `references/sources.md`. At minimum, this run must actually fetch:
- The current **Tweakers Best Buy Guide / Best Buy systemen** (the high-end game-pc tier is the
  anchor). This is the NL-specific "what's sensible right now" reference Joost asked to anchor on.
- **Tweakers Pricewatch** for each shortlisted component (live EUR price + availability).
- The **ROCm compatibility matrix / release notes** if a GPU is in play (RDNA4 support and any
  gfx1201-class gotchas move release to release).
Cross-check anything surprising against a second source (GamersNexus / Hardware Unboxed for
perf, Phoronix for Linux behaviour). Don't quote sources at length — synthesise.

### 4. Build the recommendation
Produce a full parts table honouring `requirements.md`. Where there's a real fork, present the
**primary pick + the alternative and the tradeoff** rather than hiding the choice. Always show:
- CPU (and whether the do-everything 16-core or the leaner 8-core is the better fit today)
- GPU (balanced-for-always-on vs local-AI-max vs high-VRAM options — see requirements)
- Motherboard (4-DIMM, ≥2.5GbE; no IOMMU requirement — no passthrough in this design)
- RAM (2×32 DDR5-6000 CL30 EXPO now; note the 4-DIMM speed penalty on future expansion)
- Storage, PSU (efficiency at low load), cooling (air, 24/7 quiet), case
- **Idle-power estimate** for the always-on role, and total EUR (per configuration).

### 5. Diff + log
Append a new dated entry to `BUILD-LOG.md` using the template in `references/requirements.md`
(§ Log entry format). It must capture: date, run number, each component chosen, the price this
run, the **rationale**, and **what changed vs the previous run** (price moves, new silicon, a
pick that flipped and why). Then write the full snapshot to `builds/YYYY-MM-DD-run-NNN.md`.

### 5b. Commit (when STATE_DIR is a git repo)
`git add BUILD-LOG.md builds/` and commit with `run NNN: <objective>`. If an `origin` remote is
set, push. Git history is the persistent, auditable trail of every decision and its rationale —
that's the point of running this in a repo.

### 5c. Publish the full run to Notion (when the Notion connector is attached)
Create ONE page in the **Hardware Advisor — Build Runs** database using `notion-create-pages` with
parent `{"data_source_id": "65de7c0c-e1e0-4383-9c0d-194b6f131195"}`. Set the properties (Run, Run #,
Date, GPU pick, Total (EUR), What changed, Commit) and put the **full run** in the page body — the
GPU judgement table, the full-build table with totals, "Changed vs previous run", and "Open / waiting
on". This is the shareable mirror of the `builds/` snapshot; git stays the source of truth. Keep the
returned page URL for the email. Exact property names + types are in `references/connectors.md`.

### 5d. Draft the notification email (when the Gmail connector is attached)
Call `create_draft` to **joost@barnebies.com** with a **terse** body: the GPU pick, the total, a
one-line "what changed", then the Notion page URL (and the DB URL). The full tables live in Notion —
keep the email short. **The Gmail connector is draft-only (no send):** the message lands in Joost's
Drafts and he taps Send. If Gmail isn't attached, note it in the report instead of failing.

### 6. Report
Lead with the **judgement tables** (GPU table + full-build table with a total) — that's the
deliverable Joost wants. Attach **real price links, Tweakers Pricewatch preferred** (deep-links on a
Claude Code rerun; real fetched alternatives otherwise — never a fabricated Pricewatch URL). Then
the headline pick, the GPU verdict per the value rule, and a one-line "what changed since last time".

## Style
Match Joost: terse, technical, no hand-holding. He's a physicist with a homelab; state the
tradeoff and your pick, don't pad. Flag ROCm/Linux caveats honestly (RDNA4 maturity, idle power,
4-DIMM speed drop). Best-price is a real objective — call out when waiting on a price (esp. RAM)
is the rational move.

## Reference files
- `references/requirements.md` — hard constraints, soft prefs, role definitions, log template
- `references/architecture.md` — bare-metal Linux host + native inference + isolated KVM VM design
- `references/sources.md` — canonical sources and how to query each
- `references/connectors.md` — Notion "Build Runs" DB + Gmail draft targets (IDs, properties, draft-only caveat)
