# Changelog

All notable progress for **Notele** 🗂️ — a personal automation assistant built with n8n, as part of my 30-Day Build Challenge.

Format inspired by [Keep a Changelog](https://keepachangelog.com/).
Status legend: ✅ Done · 🚧 In progress · ⏳ Planned · ❌ Blocked

---

## Day 1 — 6.7.2026

| Task | Status |
|------|--------|
| Set up GitHub repo and README | ✅ Done |
| Planned overall architecture | ✅ Done |
| Installed Docker | ✅ Done |
| Deployed n8n self-hosted (local) | ✅ Done |

**Notes:**
<details>
<summary>Details</summary>

- Created the repo and drafted the initial README.
- Installed Docker Desktop/Engine on the local machine.
- Ran n8n via a Docker self-hosted setup and confirmed the UI is accessible.
- Next: connect Telegram Bot API and test message ingestion.

</details>

---

## Day 2 — 7.7.2026

| Task | Status |
|------|--------|
| Connect Telegram Bot API | ⏳ Planned |
| Test message ingestion into n8n | ⏳ Planned |
<details>

Connected Telegram Bot API | ✅Done
Faced problems with n8n tunnel!
Working to solve it.
</details>

## Day 4 _ 9.7.2026

<details>
<summary> Debugging log</summary>

- **Problem 1:** Container kept disappearing between sessions.
  **Cause:** Used the `--rm` flag, which auto-deletes the container on stop.
  **Fix:** Rebuilt with `-d --restart unless-stopped` instead — container now persists and survives reboots.

- **Problem 2:** Telegram rejected the webhook with "HTTPS URL required."
  **Cause:** `localhost` isn't reachable from the public internet; Telegram requires a real public HTTPS address.

- **Problem 3:** Tried n8n's built-in `--tunnel` flag — no tunnel URL ever appeared in logs.
  **Cause:** Discovered n8n officially discontinued their built-in Tunnel Service (March 2026). The flag is deprecated/non-functional now.
  **Fix:** Switching to Cloudflare Tunnel instead (free, and gives a permanent URL tied to my own domain, unlike ngrok's free tier, which changes on every restart).

- **Decision:** Going with Cloudflare Tunnel over ngrok since I already own a domain — avoids the "URL changes every restart" issue and stays free long-term.

-**Results:** Registered a free domain, DigitalPlat Domains, connected it to Cloudflare, and installed it to expose n8n securely.


## Day 5 10.7.2026

| Task | Status |
|---|---|
| Diagnose Cloudflare named tunnel setup | ✅ Done |
| Create missing `config.yml` for tunnel | ✅ Done |
| Route custom domain to tunnel via DNS | ✅ Done |
| Fix broken `credentials-file` path in config | ✅ Done |
| Successfully run tunnel and confirm Telegram message reaches n8n | ✅ Done |
| Connected Notion with the workflow|  ✅ Done |
| A new page was created in the Database of Notoin| ✅ Done |
<details>
<summary>Notes</summary>

- Tunnel was created earlier, but `config.yml` was never generated — had to create it manually with `tunnel`, `credentials-file`, and `ingress` fields.
- Learned that `cloudflared tunnel route dns <name> <hostname>` must be run once to link the custom domain to the tunnel in Cloudflare DNS.
- Hit a `credentials-file doesn't exist` error caused by a leftover placeholder in `config.yml` instead of the real tunnel ID — fixed by matching the filename in the `.cloudflared/` folder exactly.
 

</details>
-->

## Day 6  11.7.2026

| Task | Status |
|---|---|
| Fix Notion field mapping, so Telegram message text saves correctly | ✅ Done |
| Map Telegram message date (Unix timestamp) into Notion Date field | ✅ Done |
| Fix incorrect date conversion (timezone/format issue) | ✅ Done |
| End-to-end test: Telegram → n8n → Notion with correct text + date | ✅ Done |

<details>
<summary>Notes</summary>

- Notion page was created, but message text field was empty — turned out the field wasn't in "expression" mode in the Notion node, so it wasn't pulling `{{ $json. message.text }}` dynamically.
- Telegram sends dates as Unix timestamps (seconds), which Notion can't read directly — had to convert using `new Date($json.message.date * 1000).toISOString()`.
- Ran into a timezone mismatch (date showing off by a day/hours) — fixed by explicitly converting to local timezone using `toLocaleString()` with `timeZone: 'Africa/Cairo'`.
- ✅ Big milestone: full pipeline works — Telegram message → n8n → correctly saved in Notion with accurate text and timestamp.
- Next up: add categorization logic (keyword-based first, then AI) so messages sort automatically instead of landing uncategorized.

</details> 

## Day 7 12.7.2026

## Planning for the next phase.


## Day 8  July 13, 2026

| Task | Status |
|---|---|
| Debug Code node error (undefined `message.text` on non-text updates) | ✅ Done |
| Debug Notion "type null not assignable" error on Category property | ✅ Done |
| Implement prefix-based categorization (S/O/T/I) with keyword fallback | ✅ Done |
| Plan dynamic Categories lookup via Notion database | 🔜 Planned for tomorrow |

<details>
<summary>Notes</summary>

- Code node was crashing on Telegram updates without a `message` field (e.g., edited messages, channel posts). Fixed by adding a guard clause that skips/returns `null` for those instead of erroring.
- Notion rejected page creation with `type null is not assignable to type` — root cause was the Category select property missing an option that the workflow was trying to send. Fixed by ensuring all category names exist as options in the Notion select field.
- Switched primary categorization method from pure keyword-matching to prefix shortcuts (e.g., `S` for Scholarship, `O` for Opportunity) for more reliable, deterministic sorting. Keyword matching was kept as a fallback for messages sent without a prefix.
- Next step: move the prefix → category mapping out of the Code node and into a small Notion "Categories" database, so new categories can be added without editing the workflow. Design is planned; implementation tomorrow.

</details>

## Day 9  14.7.2026
| Task | Status |
|---|---|
| Dynamic category lookup (Notion-based, no more hardcoded letters) | 🟡 In Progress |

<details>
<summary>Notes</summary>

Replaced hardcoded if-else letter mapping in the categorization Code node
With a dynamic lookup from a new "Sortify Categories" Notion database.
Adding a new category now means adding a row in Notion — zero n8n edits
Needed once this is fully working.

</details>
