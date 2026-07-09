# Connectors — Notion + Gmail publish/notify targets

After each run (once the log + snapshot are committed) the skill publishes the full result to Notion
and drafts a terse email. Both are done by calling the connector MCP tools **during the run** — they
are not something plain `git`/CI can trigger. So this only happens on a Claude Code run where the
Notion and Gmail connectors are attached (web / desktop / GitHub app). If a connector isn't present,
skip that step and say so in the report — never fail the run over it.

## Notion — full results (one page per run)

- **Database:** `Hardware Advisor — Build Runs`
- **Database URL:** https://app.notion.com/p/56a581bb53e94fb1ad641cfebf3eb9ac
- **data_source_id:** `65de7c0c-e1e0-4383-9c0d-194b6f131195`  ← create pages under this

Create ONE page per run with `notion-create-pages`, parent `{"data_source_id": "65de7c0c-e1e0-4383-9c0d-194b6f131195"}`.

Properties (match names exactly):
| Property | Type | Value |
|---|---|---|
| `Run` | title | `Run NNN — <one-line objective>` |
| `Run #` | number | the run number |
| `date:Date:start` | date | run date, `YYYY-MM-DD` |
| `GPU pick` | text | e.g. `RX 7900 XTX 24 GB (~€675 used / ~€950+ new)` |
| `Total (EUR)` | text | the build total(s), e.g. `~€1,830–2,050 (used GPU) / ~€2,100–2,350 (new)` |
| `What changed` | text | one/two lines vs the previous run |
| `Commit` | url | link to this run's commit on the branch |

Page **content** (Notion-flavored markdown) = the full run: the GPU judgement table, the full-build
table with totals, "Changed vs previous run", and "Open / waiting on". This is the same material as
the `builds/` snapshot — Notion is the shareable mirror, git is still the source of truth. Set the
page icon to 🖥️. Keep the returned page URL — the email links to it.

## Gmail — terse notification (DRAFT only)

**Important limitation:** this Gmail connector exposes `create_draft` only — there is **no send
tool**. So the skill prepares a ready-to-go draft to Joost; he opens Gmail → Drafts and taps Send.
It cannot fire mail unattended. (True auto-send would need a Gmail connector with send scope.)

Call `create_draft`:
- **to:** `joost@barnebies.com`
- **subject:** `Hardware Advisor — Run NNN (YYYY-MM-DD): <GPU pick short>`
- **body (terse):** pick + total + one-line change, then the Notion page URL, then the DB URL.

Keep the email short by design — the full tables live in Notion, not the email.
