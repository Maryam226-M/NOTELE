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

              Updated n8n's webhook URL to the new public address and reactivated the Telegram trigger.

              Sent a test message from Telegram. It arrived successfully in n8n's execution log.🤭
</details>
-->
