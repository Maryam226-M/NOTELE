# Changelog

All notable progress for **Notele** 🗂️ — a personal automation assistant built with n8n, as part of my 30-Day Build Challenge.

Format inspired by [Keep a Changelog](https://keepachangelog.com/).
Status legend: ✅ Done · 🚧 In progress · ⏳ Planned · ❌ Blocked

---

## [Unreleased]

### Planned
- Node code export to GitHub repository
- Enhanced error handling for edge cases

---

## [v1.0.0] - 2026-08-04

### Added
- **Complete end-to-end Telegram → n8n → Notion pipeline**
  - Batch-processes messages sent to Telegram Bot
  - Creates formatted, categorized pages in Notion database
  - Supports text messages and URLs

- **Prefix-Based Categorization System**
  - Uses letter prefixes (S=Scholarship, O=Opportunity, T=Task, I=Info)
  - Dynamic category lookup via Notion database
  - Adding new categories requires zero workflow changes
  - Keyword fallback for messages without prefixes

### Fixed
- **Telegram Offset State Management**
  - Implemented persistent offset tracking via Notion database
  - Prevents duplicate processing across container restarts
  - Uses `$getWorkflowStaticData('global')` to bypass Notion node data overwrite
  - Solves the "Docker isn't 24/7" problem elegantly

- **Notion API Flattening Issues**
  - Fixed category matching errors caused by n8n's property renaming
  - Updated all references to use flattened keys (`property_letter`, `property_category_name`)
  - Resolved "type null is not assignable" errors with proper select field options

- **Code Node Execution Modes**
  - Corrected `$input.item.json` vs `$input.first().json` confusion
  - Standardized per-item processing with single-object returns
  - Removed redundant "Split Out" node blocking the flow

- **Timezone and Date Handling**
  - Converted Telegram Unix timestamps to ISO strings
  - Applied Africa/Cairo timezone for local date display
  - Fixed day-off-by-one errors

### Changed
- **Categorization Logic**: Moved from hardcoded if-else to dynamic Notion database lookup
- **State Management**: Switched from volatile `getWorkflowStaticData` to Notion persistence (survives container restarts)
- **Telegram API Calls**: Changed `timeout=0` to prevent long-polling freezes on manual execution
- **Notion Update Node**: Isolated to "Run Once for All Items" to prevent infinite workflow loops

### Removed
- Hardcoded category mappings
- Deprecated n8n built-in tunnel (discontinued March 2026)
- ~~Google Drive File Upload Feature~~ (Removed after OAuth implementation difficulties)

### Security
- **Cloudflare Tunnel**: Secured local n8n instance with HTTPS and custom domain

## Development Log

### Week 1: Foundation & First Hurdles (Days 1-7)

<details>
<summary><b>Day 1 — 6.7.2026 — "Let's Build Something"</b></summary>

**Tasks:**
- ✅ Set up GitHub repo and README
- ✅ Planned overall architecture
- ✅ Installed Docker
- ✅ Deployed n8n self-hosted (local)

**Notes:**
The first day was all about laying the groundwork. Got Docker running, n8n UI accessible, and the initial repo structure in place. Felt like a clean start — little did I know what was coming.

**Next:** Connect Telegram Bot API and test message ingestion.
</details>

<details>
<summary><b>Day 2 — 7.7.2026 — "Telegram Tango Begins"</b></summary>

**Tasks:**
- ✅ Connected Telegram Bot API 
- ⏳ Test message ingestion into n8n

**Notes:**
Got the bot token configured, but the n8n tunnel decided to play hard to get. Spent the day wrestling with connectivity — this is where I learned that the built-in tunnel was officially deprecated. Fun times.
</details>

<details>
<summary><b>Day 4 — 9.7.2026 — "Debugging Day from Hell"</b></summary>

**Debugging Log:**

**Problem 1: Container Vanishing Act**
- **Issue:** Container kept disappearing between sessions
- **Cause:** Used the `--rm` flag, which auto-deletes containers on stop
- **Fix:** Rebuilt with `-d --restart unless-stopped` — now survives reboots

**Problem 2: Telegram Webhook Requirements**
- **Issue:** "HTTPS URL required" error
- **Cause:** `localhost` isn't publicly reachable; Telegram requires HTTPS
- **Fix:** Needed a tunneling solution (enter Cloudflare)

**Problem 3: Dead n8n Tunnel**
- **Issue:** No tunnel URL appeared despite using `--tunnel` flag
- **Cause:** n8n officially discontinued their built-in Tunnel Service (March 2026)
- **Fix:** Switched to Cloudflare Tunnel

**Decision Point:**
> "Going with Cloudflare Tunnel over ngrok. I already own a domain — avoids the 'URL changes every restart' issue and stays free long-term."

**Results:**
✅ Registered a free domain via DigitalPlat Domains  
✅ Connected it to Cloudflare  
✅ Installed Cloudflare Tunnel to expose n8n securely
</details>

<details>
<summary><b>Day 5 — 10.7.2026 — "Tunnel Breakthrough"</b></summary>

**Tasks:**
- ✅ Diagnose Cloudflare named tunnel setup
- ✅ Create missing `config.yml` for tunnel
- ✅ Route custom domain to tunnel via DNS
- ✅ Fix broken credentials-file path in config
- ✅ Successfully run tunnel and confirm Telegram message reaches n8n
- ✅ Connected Notion with the workflow
- ✅ First Notion page created!

**Notes:**
🎉 **BIG WIN!** The tunnel was created earlier, but `config.yml` was never generated — had to create it manually with tunnel, credentials-file, and ingress fields.

**Key Learnings:**
- `cloudflared tunnel route dns <name> <hostname>` must be run once to link the custom domain
- Credentials-file errors are usually path mismatches — fixed by matching the filename in `.cloudflared/` folder exactly
</details>

<details>
<summary><b>Day 6 — 11.7.2026 — "First End-to-End Pipeline"</b></summary>

**Tasks:**
- ✅ Fix Notion field mapping (message text saves correctly)
- ✅ Map Telegram message date (Unix timestamp) into Notion Date field
- ✅ Fix incorrect date conversion (timezone/format issue)
- ✅ End-to-end test: Telegram → n8n → Notion with correct text + date

**Notes:**
🚀 **MILESTONE ACHIEVED!** Full pipeline works — Telegram message → n8n → correctly saved in Notion with accurate text and timestamp.

**Lessons Learned:**
- Notion pages were created but message text field was empty — the field wasn't in "expression" mode, so it wasn't pulling `{{ $json.message.text }}` dynamically
- Telegram sends dates as Unix timestamps (seconds) → Notion can't read directly → converted using `new Date($json.message.date * 1000).toISOString()`
- Timezone mismatch fixed by converting to `Africa/Cairo` using `toLocaleString()`

**Next:** Add categorization logic (keyword-based first, then AI).
</details>

<details>
<summary><b>Day 7 — 12.7.2026 — "Planning Phase"</b></summary>

**Notes:**
Took time to plan the categorization architecture. Decided on prefix-based shortcuts (S/O/T/I) for deterministic sorting, with keyword matching as fallback.
</details>

---

### Week 2: Refinement & Categorization (Days 8-14)

<details>
<summary><b>Day 8 — 13.7.2026 — "Category System Takes Shape"</b></summary>

**Tasks:**
- ✅ Debug Code node error (`undefined message.text` on non-text updates)
- ✅ Debug Notion "type null not assignable" error on Category property
- ✅ Implement prefix-based categorization (S/O/T/I) with keyword fallback
- 🔜 Plan dynamic Categories lookup via Notion database (tomorrow)

**Notes:**
The Code node was crashing on Telegram updates without a `message` field (e.g., edited messages, channel posts). Fixed with a guard clause that skips/returns `null` for those.

Notion rejected page creation with `type null is not assignable to type` — root cause was the Category select property missing an option that the workflow was trying to send. Fixed by ensuring all category names exist as options in Notion.

**Decision:**
> "Switched primary categorization method from pure keyword-matching to prefix shortcuts (e.g., S for Scholarship, O for Opportunity). Keyword matching kept as fallback for messages without a prefix."
</details>

<details>
<summary><b>Day 9 — 14.7.2026 — "Dynamic Categories Go Live"</b></summary>

**Tasks:**
- ✅ Dynamic category lookup (Notion-based, no more hardcoded letters)

**Notes:**
🎉 Replaced hardcoded if-else letter mapping with dynamic lookup from a new "Categories" Notion database. Adding a new category now means adding a row in Notion — **zero n8n edits needed**.
</details>

<details>
<summary><b>Day 10-12 — 15-17.7.2026 — "Pipeline Troubles"</b></summary>

**Notes:**
- **Day 10:** Link pulling problems discovered
- **Day 11:** Pipeline instability detected
- **Day 12:** Root cause identified
</details>

<details>
<summary><b>Day 13 — 18.7.2026 — "Major Debugging Session"</b></summary>

**Debugging Log:**

**Problem 1: Empty API Responses**
- **Issue:** Telegram API kept returning empty arrays `[]`
- **Cause:** `getUpdates` requires an explicit `offset` parameter to know which messages to send next; without persistent memory, it gets stuck
- **Fix:** Added n8n `getWorkflowStaticData` nodes to remember the highest processed `update_id` between Docker restarts

**Problem 2: Code Node Misconfiguration**
- **Issue:** Only processed the first message in a batch; threw `'json' property isn't an object` errors
- **Cause:** Wrong Code Node modes. Used `.first()` and returned arrays `[{json}]` in "Run Once for Each Item" mode, breaking n8n's execution loop
- **Fix:** Standardized node modes — used `$input.item.json` and returned single objects `{json}` (no brackets) for per-item processing

**Problem 3: Category Matching Failure**
- **Issue:** Always defaulted to "Uncategorized"
- **Cause:** Code was looking for deep nested Notion API structures (`properties.Letter.rich_text`), but n8n outputs flattened JSON objects
- **Fix:** Inspected raw node output and updated matching logic to use exact flat keys (`property_letter` and `property_category_name`)

**Problem 4: Notion Page Creation Error**
- **Issue:** `Can't determine which item to use` error
- **Cause:** Tried using complex static referencing syntax to grab raw Telegram text
- **Fix:** Mapped the node directly to clean variables outputted by the final Code node (e.g., `{{ $json.cleanText }}`), which n8n loops for multiple items automatically

**Results:**
✅ Fully working 9-node batch-processing workflow. Can now send formatted messages (e.g., "T buy groceries") all day while offline, boot up Docker, and have n8n perfectly process everything. **No 24/7 webhooks or Cloudflare tunnels needed for the Telegram side.**
</details>

<details>
<summary><b>Day 14 — 19.7.2026 — "Planning Next Feature"</b></summary>

**Notes:**
Took time to plan the next major feature — initially considered file upload support, but later decided against it.
</details>

---

### Week 3: Deduplication & Date Fixes (Days 15-22)

<details>
<summary><b>Day 15-16 — 20-21.7.2026 — "New Feature Problems"</b></summary>

**Notes:**
Started working on duplicate prevention. Encountered issues with the new logic.
</details>

<details>
<summary><b>Day 17 — 22.7.2026 — "Offset Tracking Implemented"</b></summary>

**Feature: Prevent Duplicate Telegram Message Processing via Offset Tracking**

**The Problem:**
Because n8n runs locally via Docker and isn't active 24/7, the workflow relies on manual execution to batch-process messages. Telegram's `getUpdates` API returns all pending messages from the last 24 hours, causing duplicates every time the workflow was triggered.

**The Solution:**
Implemented Telegram's native `offset` parameter with a strict stateful loop:

1. **Read:** Fetch last processed `update_id` from Notion config database
2. **Fetch:** Request updates from Telegram using that specific offset (`?offset=45`)
3. **Process:** Parse, format, and create Notion pages for new messages
4. **Update:** Calculate highest `update_id` + 1, update Notion config database

**Why Notion for Persistence?**
> "Docker containers are ephemeral in this setup. Notion ensures the offset survives container restarts."

**Obstacles Overcome:**

- **Notion API Data Flattening:** n8n automatically flattens properties, renaming `Offset` to `property_offset`. Updated code to look for the flattened key.
- **Node Data Overwrite:** The "Notion Create Page" node overwrites incoming JSON. Solved by using `$getWorkflowStaticData('global')` to temporarily store the new offset in memory.
- **API Hanging on Manual Execution:** Telegram's `getUpdates` defaults to long-polling (`timeout=30`). Resolved by passing `?timeout=0`.
- **Loop Prevention:** Ensured final "Update Notion" node executes exactly once using "Run Once for All Items" setting.

**Result:**
✅ Workflow is now **idempotent**. Can start and stop arbitrarily without creating duplicate entries. Seamlessly picks up exactly where it left off.
</details>

<details>
<summary><b>Day 18-19 — 23-24.7.2026 — "Date Problems"</b></summary>

**Notes:**
- **Day 18:** Working on date formatting issues
- **Day 19:** Date handling issues resolved ✅
- **Next:** Planning next phase
</details>

<details>
<summary><b>Day 20-22 — 25-27.7.2026 — "Category Bug Discovery & Fix"</b></summary>

**Notes:**
- **Day 20:** New bug in categories system discovered 🚧
- **Day 22:** Categories bug fixed ✅

> "Imagine what the bug was.... The 'Notion Flattening' Trap, just one little change in the name of the database."

The categories broke because Notion property names changed. A single underscore difference caused everything to fail silently. Debugging this was... humbling.
</details>

---

### Week 4: Final Polish & Completion (Days 23-30)

<details>
<summary><b>Day 23-27 — 28.7-1.8.2026 — "Feature Exploration"</b></summary>

**Notes:**
Explored adding file upload support via Google Drive Middleman. After implementing 50% of the feature and encountering OAuth 401 errors, made the decision to remove it.

**Why It Was Removed:**
- OAuth implementation through Cloudflare Tunnel proved overly complex
- Local file storage alternative was simpler and met the core need
- Focus shifted to polishing the existing text/URL pipeline
</details>

<details>
<summary><b>Day 28-29 — 2-3.8.2026 — "The Home Stretch"</b></summary>

**Notes:**
- **Day 28:** Final testing and bug fixes
- **Day 29:** Building Notele is Done ✅

The 30-day challenge is officially complete! Notele is a fully functional personal automation assistant.
</details>

<details>
<summary><b>Day 30 — 4.8.2026 — "Victory Lap"</b></summary>

**Notes:**
Nothing to do today — yaaay! 🎉

Will upload the code of the nodes to GitHub soon. **🙊**
</details>

---

## Technical Debt & Future Improvements

### Known Issues
- Files are stored locally (volume mounted) — no cloud backup yet
- No error notifications if the pipeline fails

### Planned Enhancements
- [ ] Add AI-based categorization using OpenAI/Claude
- [ ] Implement error notifications via Telegram
- [ ] Create n8n workflow export for easy deployment
- [ ] Add Docker Compose production setup
- [ ] Explore local file storage as an alternative to Google Drive

---
## Acknowledgments

- **n8n** — The automation backbone
- **Cloudflare** — Tunnel and domain management
- **Notion** — The "second brain" storage layer
- **Telegram** — The input interface

---

* My first Project using **n8n** Built with ❤️ and lots of ☕ during a 30-Day Build Challenge.*
