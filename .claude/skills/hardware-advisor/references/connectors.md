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
page icon to 🖥️. Keep the returned page URL — the notification links to it.

## Notification — phone push (primary)

Notify Joost with a **phone push** via the `PushNotification` tool (status `proactive`). This is the
chosen mechanism: zero setup, no credentials, and it works **unattended in routines** (unlike the
connectors, which may be absent when a scheduled run fires). Push it right after the Notion page is
created so the message can reference it.

- **message (< 200 chars, one line, no markdown):** GPU pick + total + a one-line "what changed".
  Include the Notion page URL if it fits.
- Example: `HW Advisor Run 003: pick RX 7900 XTX 24GB, ~€1,830–2,350. Held on value rule. See Notion.`

Note: `PushNotification` reaches the phone only when Remote Control is connected, and is skipped as
redundant when Joost is actively at the terminal (a "not sent" result there is expected, not a
failure). For scheduled/unattended runs he's away, so it delivers.

## Why not SMTP / auto-send email

Claude Code on the web only allows outbound **HTTPS through the agent proxy** — raw SMTP ports
(465/587) are firewalled, so App-Password / SMTP "gmail-send" skills do **not** work here. True
auto-send email would need an HTTPS path with a stored secret (Resend API, or the Gmail API via a
stored OAuth refresh token). Push was chosen instead. If email is ever wanted, wire one of those.

## Gmail — optional draft (interactive runs only)

The managed Gmail connector is **draft-only** (`create_draft`, no send). Optional nicety on an
interactive Claude Code run: also drop a ready-to-send draft to `joost@barnebies.com` (subject
`Hardware Advisor — Run NNN (YYYY-MM-DD): <pick>`, terse body + Notion link) that he can tap Send on.
Skip silently if the connector isn't attached — the phone push is the real notification.
