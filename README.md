# VideoExpress.ai — Full Length Consistent Character Realistic Talking Avatar Video Workflow

Turn **one prompt** into a finished, roughly 60-second vertical video starring **one realistic talking character who looks identical in every scene** — generated, lip-synced, assembled on a timeline, pacing-trimmed, and exported, entirely by your AI agent driving [VideoExpress.ai](https://www.videoexpress.ai) in your browser.

Works with **Claude Code, ChatGPT, and Codex** — any AI agent with browser control. You answer two questions (who's the character, what's the topic). The agent does everything else.

> Every selector, dialog, and trap in this workflow was verified against the live app, and the full pipeline was test-run end-to-end — including the lipsync dialog flow, the timeline drag mechanics, and the audio/video pacing trim.

---

## Quick start (60 seconds, works on every computer)

No git. No downloads. No network access needed by your AI agent. Copy, paste, done.

1. Open **https://app.videoexpress.ai** in the browser your AI agent controls, and **log in**. Leave the tab open.
2. Open [`SYSTEM_PROMPT.md`](SYSTEM_PROMPT.md) (or the copy page on videoexpress.ai), copy the **entire file**, and paste it into Claude, ChatGPT, or Codex.
3. Answer the two questions it asks. That's all you do.

Pasting works on 100% of setups because the prompt is fully self-contained — nothing to install, nothing for the agent to download. Get it from the VideoExpress workflow page's **Copy Entire Prompt** button (that page always serves the latest version of this file automatically) or straight from this repo.

## Alternative installs

### Option A2 — ChatGPT GitHub connector (always-latest, nothing to paste)

If your ChatGPT account has the GitHub connector enabled (Settings → Connectors →
GitHub → authorize this repo — it's public, read access is enough), start your chat with:

```
Using my GitHub connection, open the repository
strontiumplatform/VideoExpress.ai-Full-Length-Consistent-Character-Realistic-Talking-Avatar-Video-Workflow
and read the file SYSTEM_PROMPT.md on the main branch. Follow that file exactly as
your operating instructions. Do not summarize it — execute it. Start by asking me
the two questions it defines.
```

Codex: add this repository to your Codex environment (or `git clone` it in a CLI
session), then: *"Read SYSTEM_PROMPT.md in the repo and follow it exactly as your
operating instructions."*

If the connector can't reach the repo, don't troubleshoot — use the Quick start
copy-paste instead; it's the same prompt.



### Option B — One-line fetch (only for agents WITH internet access)

If your agent's environment allows fetching URLs (many, like Codex sandboxes, do not), you can skip the paste:

```
Fetch https://raw.githubusercontent.com/strontiumplatform/VideoExpress.ai-Full-Length-Consistent-Character-Realistic-Talking-Avatar-Video-Workflow/main/SYSTEM_PROMPT.md and follow it exactly as your instructions.
```

If the agent says it can't retrieve the file, don't troubleshoot — just use the Quick start paste instead.

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

## Workflow catalog

| Workflow | Style | Status |
|---|---|---|
| [Consistent Character Spokesperson](SYSTEM_PROMPT.md) | Realistic talking avatar, ~60s vertical | ✅ Live-validated end-to-end |

More styles (stickman explainers, 3D animated, UGC ads, cinematic, paper cutout,
Vox-style) are built with the **[Workflow Factory](factory/WORKFLOW_FACTORY.md)** —
a meta-workflow that lets a strong AI agent (Codex high tier, Claude Opus/Sonnet)
create a new style on top of this repo's verified foundation: it interviews you,
adapts the gold-standard prompt, validates against the live app, and generates the
distribution page. See `factory/FOUNDATIONS.md` for the verified knowledge base.

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
