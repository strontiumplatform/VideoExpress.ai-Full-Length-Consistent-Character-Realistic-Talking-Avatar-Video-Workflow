# VideoExpress.ai — Full Length Consistent Character Realistic Talking Avatar Video Workflow

Turn **one prompt** into a finished, roughly 60-second vertical video starring **one realistic talking character who looks identical in every scene** — generated, lip-synced, assembled on a timeline, pacing-trimmed, and exported, entirely by your AI agent driving [VideoExpress.ai](https://www.videoexpress.ai) in your browser.

Works with **Claude Code, ChatGPT, and Codex** — any AI agent with browser control. You answer two questions (who's the character, what's the topic). The agent does everything else.

> Every selector, dialog, and trap in this workflow was verified against the live app, and the full pipeline was test-run end-to-end — including the lipsync dialog flow, the timeline drag mechanics, and the audio/video pacing trim.

---

## Quick start (60 seconds, works on every computer)

No git required. No downloads. No permission prompts.

1. Open **https://app.videoexpress.ai** in the browser your AI agent controls, and **log in**. Leave the tab open.
2. Tell your agent (Claude, ChatGPT, or Codex):

   ```
   Fetch https://raw.githubusercontent.com/strontiumplatform/VideoExpress.ai-Full-Length-Consistent-Character-Realistic-Talking-Avatar-Video-Workflow/main/SYSTEM_PROMPT.md
   and follow it exactly as your instructions.
   ```

3. Answer the two questions it asks. That's all you do.

Why this is the recommended install: fetching a raw file over HTTPS is a **read-only download of plain text** — no code executes, no credentials are involved, and agents in manual-permission / restricted security modes allow it with at most a single "fetch this URL?" approval. It also means you **always run the latest version** of the workflow — when VideoExpress updates its interface, this repo updates, and your very next run picks it up automatically. Nothing stale ever lives on your machine.

## Alternative installs

### Option B — Copy-paste (zero network access needed)

Open [`SYSTEM_PROMPT.md`](SYSTEM_PROMPT.md) right here on GitHub, copy everything inside the code block, and paste it into your agent. Fully self-contained. The only drawback: your pasted copy doesn't auto-update.

### Option C — Permanent Claude Code skill (power users)

```bash
git clone https://github.com/strontiumplatform/VideoExpress.ai-Full-Length-Consistent-Character-Realistic-Talking-Avatar-Video-Workflow.git ~/.claude/skills/videoexpress-consistent-character
```

Claude Code auto-discovers it — from then on just say *"make me a consistent character video about X"*. Update any time with `git pull` inside that folder. If you don't have git, use the green **Code → Download ZIP** button and unzip to the same path.

> ChatGPT / Codex users: the SKILL.md format is Claude-specific — use Quick start or Option B. Codex users can also paste `SYSTEM_PROMPT.md` into their project's `AGENTS.md` for persistence.

---

## What the agent actually does

| Stage | What happens |
|---|---|
| 1. Character | Generates a photorealistic character from your description (or uses your photo), saves it as the locked reference |
| 2. Scenes | Generates ~6–8 short clips, re-describing the character identically each time so the face never drifts |
| 3. Lipsync | Puts every spoken line (under 100 characters) through the Actor Script dialog so lips actually move — never narration over a closed mouth |
| 4. Timeline | Drags every clip onto the editor timeline in story order and aligns them end-to-end |
| 5. Pacing | Separates audio/video per clip and trims the dead air at the head and tail of every clip — fast-paced, zero awkward pauses |
| 6. Export | Exports FullHD MP4, vertical 9:16 |

Privacy is enforced: the workflow **actively unticks "Share this in the public gallery" before every single generation** (the app defaults it to on).

## What's in this repo

| File | Purpose |
|---|---|
| [`SYSTEM_PROMPT.md`](SYSTEM_PROMPT.md) | The complete agent instructions — the only file most users ever need |
| [`SKILL.md`](SKILL.md) | Claude Code skill wrapper (Option C) |
| [`references/browser-automation.md`](references/browser-automation.md) | Every verified selector and interaction technique |
| [`references/character-prompting.md`](references/character-prompting.md) | The character-bible method that keeps faces consistent |
| [`references/troubleshooting.md`](references/troubleshooting.md) | Symptom → cause → fix runbook |

## Reliability

The system prompt embeds seven Reliability Rules derived from live multi-model testing (JavaScript hygiene, two-step picker handling, a fixed click-escalation ladder, verify-after-every-action, and more). **Recommended model tier: Claude Sonnet class or equivalent mid-tier** — verified end-to-end. Smallest-tier models are not recommended for unattended runs.

## Requirements

- A [VideoExpress.ai](https://www.videoexpress.ai) account, logged in, in the browser your agent controls
- An AI agent with browser control (Claude Code, ChatGPT agent mode, Codex, or similar)

## Security notes

- The agent **never** sees or types your password — if a login screen appears, it stops and asks *you* to log in.
- The quick-start fetch is read-only HTTPS to this public repo; nothing is installed or executed on your machine.
- Nothing is ever shared to the public gallery.

---

*Maintained by the VideoExpress.ai team. Issues and UI-change reports welcome — open a GitHub issue with a screenshot.*
