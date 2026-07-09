# hardware-advisor

A rerunnable, self-logging PC-build advisor packaged as a **Claude Code project skill**. It prices a
specific machine against fixed requirements, produces price/performance judgement tables with live
links, and records every decision + rationale to a git-versioned log.

**The machine:** a bare-metal Linux host that is simultaneously a daily driver + gaming rig
(QHD ~100 FPS target, not 4K) + native local-inference box, plus one network-isolated KVM VM for a
Claude/agent worker. AMD AM5, thread-heavy CPU, AMD-or-NVIDIA GPU, 64 GB DDR5 on a 4-DIMM board.
No Proxmox, no GPU passthrough.

## Run it (Claude Code — web, desktop, or GitHub)

1. Push this repo to GitHub.
2. Open it in Claude Code (web or desktop) or connect it via the Claude Code GitHub app.
3. Say `re-run the build` / `reprice the box` / `what's the best GPU right now`, or invoke
   `/hardware-advisor`.

Because the skill lives in `.claude/skills/`, Claude Code discovers it automatically from the repo —
nothing to install. The root `CLAUDE.md` gives Claude the always-on context.

Each run: fetches the live Tweakers Best Buy Guide + Pricewatch (and ROCm notes), builds the
judgement tables, then appends to `BUILD-LOG.md`, writes a `builds/` snapshot, and commits. **Git
history is the audit trail** of every choice and why.

**Notion + email (optional, connector-driven).** When run in Claude Code with the Notion and Gmail
connectors attached, each run also publishes the full result as one page in a **Hardware Advisor —
Build Runs** Notion database and drafts a terse summary email (linking that page) to the owner. The
Gmail connector is **draft-only** — the mail is prepared in Drafts to send with one tap; it is not
sent unattended. Missing connectors are skipped, never fatal. See
`.claude/skills/hardware-advisor/references/connectors.md`.

## Layout

```
hardware-advisor/
├── CLAUDE.md                     # always-on project context
├── README.md
├── BUILD-LOG.md                  # append-only decision log (newest first) — the audit trail
├── builds/                       # one dated snapshot per run
└── .claude/
    └── skills/
        └── hardware-advisor/
            ├── SKILL.md          # the run procedure + config
            └── references/
                ├── requirements.md   # fixed spec, value rule, output format
                ├── architecture.md   # bare-metal host + isolated VM design
                ├── sources.md        # where to fetch prices/specs
                └── connectors.md     # Notion "Build Runs" DB + Gmail draft targets
```

## Personal install (optional)

To use it outside this repo on your own machine, copy the skill folder to your personal skills dir:

```bash
cp -r .claude/skills/hardware-advisor ~/.claude/skills/
```

It then works in any project; state falls back to `~/.claude/skills/hardware-advisor/` (see the
config block in `SKILL.md`).

## Notes

Prices go stale fast (DDR5 + the 2026 GPU/VRAM shortage). The skill always re-fetches; never trust
the euros in an old log entry. Tweakers Pricewatch deep-links are attached on runs with full web
access (Claude Code); if a run can't reach Pricewatch it uses real fetched alternatives and says so.
