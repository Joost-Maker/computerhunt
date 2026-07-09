# CLAUDE.md — hardware-advisor

This repo is a **rerunnable AMD-AM5 PC build advisor** for one machine: a bare-metal Linux host that
is a daily driver + gaming rig (QHD ~100 FPS, not 4K) + native local-inference box, plus one
network-isolated KVM VM for a Claude/agent worker. No Proxmox, no GPU passthrough.

## How to run it
The logic lives in the **project skill** at `.claude/skills/hardware-advisor/`. It auto-triggers on
requests like "re-run the build", "reprice the box", "what's the best GPU now", or invoke it
explicitly with `/hardware-advisor`.

When you run it, follow the skill's procedure: load the fixed spec + architecture, read the last run
in `BUILD-LOG.md`, **fetch live data** (Tweakers Best Buy Guide + Pricewatch, ROCm notes), produce
the **judgement tables** (GPU table + full-build table with a total) with **real price links
(Tweakers Pricewatch preferred)**, then append the decision + rationale to `BUILD-LOG.md`, write a
snapshot to `builds/`, and **commit** (`run NNN: <objective>`; push if `origin` is set).

Then, **when the Notion + Gmail connectors are attached** (Claude Code web/desktop/GitHub): publish
the full run as one page in the **Hardware Advisor — Build Runs** Notion database, and draft a terse
summary email to joost@barnebies.com linking that page. The Gmail connector is **draft-only** (no
send) — the mail lands in Drafts and Joost taps Send. If a connector is missing, skip that step and
say so; never fail the run over it. IDs + exact fields are in
`.claude/skills/hardware-advisor/references/connectors.md`.

## Where state lives (persistent, git-versioned)
- `BUILD-LOG.md` — append-only decision log (newest at top). The audit trail.
- `builds/YYYY-MM-DD-run-NNN.md` — full snapshot per run.
- Notion **Hardware Advisor — Build Runs** — shareable mirror, one page per run (git stays the truth).
Git history IS the persistence layer — every rerun is a commit.

## Fixed facts (don't re-litigate)
- CPU: AMD AM5, thread-heavy (Ryzen 9 9950X3D, 16c/32t).
- GPU: AMD **or** NVIDIA now (single dGPU, bare-metal host). Value rule: last-gen+high-VRAM >
  new-gen+low-VRAM; equal VRAM → newer; never a ~€2000 card just for VRAM. Current pick: RX 7900 XTX
  24 GB (see `.claude/skills/hardware-advisor/references/requirements.md`).
- RAM: 64 GB as 2×32 DDR5-6000 CL30 on a 4-DIMM board. Do NOT fill all 4 slots (speed drops);
  128 GB later = 2×64 swap.
- Design details in `.claude/skills/hardware-advisor/references/architecture.md`.

Prices go stale fast (DDR5 + shortage-era GPUs). Never trust logged prices — re-fetch every run.
