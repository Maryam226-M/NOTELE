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
