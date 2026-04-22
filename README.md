# claude-keepalive

Tired of sending one message to Claude and then having to wait **4h 59m** before your 5-hour usage window resets? This repo fixes that.

It fires a tiny "ping" to Claude every ~5 hours via GitHub Actions, so your rolling limit window is **always active** — started on a schedule, not on your first real message of the day.

## How it works

A GitHub Action runs `claude -p "..."` on a cron schedule, authenticated with a long-lived Claude Code OAuth token tied to your Pro/Max subscription. The request is minimal (one word in, one word out), so it barely touches your quota — but it keeps the 5h window rolling so it never "ambushes" you when you actually want to work.

## Setup (2 minutes)

1. **Fork this repo.**

2. **Generate a Claude Code OAuth token** on your local machine:
   ```bash
   npm install -g @anthropic-ai/claude-code
   claude setup-token
   ```
   Copy the token it prints.

3. **Add it as a secret** in your fork:
   `Settings → Secrets and variables → Actions → New repository secret`
   - Name: `CLAUDE_CODE_OAUTH_TOKEN`
   - Value: *(paste the token)*

4. **Enable Actions** on the fork (`Actions` tab → enable workflows).

Done. The workflow runs every 5 hours automatically. You can also trigger it manually from the Actions tab via "Run workflow".

## Caveats (please read)

- **Subscription only.** This uses your Claude Pro/Max quota, not the paid API.
- **Each ping uses a small slice of your window.** You're trading "window starts when I first send a message" for "window is always active but very slightly smaller." That's the whole point, but be aware.
- **Cron drifts.** GitHub Actions cron can fire 5–15 min late. The schedule is set to every ~5h at fixed UTC hours to stay safe.
- **ToS gray area.** Automating pings purely to manipulate your rate window isn't an explicitly sanctioned use of the OAuth token. Anthropic could disapprove. Use at your own risk; don't run this on an account you can't afford to lose.

## License

MIT — see [LICENSE](LICENSE).
