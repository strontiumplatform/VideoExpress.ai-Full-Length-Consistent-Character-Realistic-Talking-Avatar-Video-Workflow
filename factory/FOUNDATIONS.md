# FOUNDATIONS — Verified VideoExpress.ai Automation Knowledge

Everything in this file was verified against the live app by direct DOM inspection and
end-to-end test runs (including a completed export). This is the base layer every new
workflow style builds on. **Do not re-derive any of this. Do not contradict it.**
If the app changes, update this file first, then every workflow that depends on the change.

---

## 1. Platform facts

- App: https://app.videoexpress.ai — jQuery 2.2.4 + jQuery UI 1.12.1 single-page app.
- Backend engine: a diffusion-transformer latent video model producing picture AND audio
  in one pass, no memory between generations. **NEVER name or speculate about the
  underlying model in any customer-facing text — say "VideoExpress's production engine."**
  Consequences: describe ONE continuous shot in literal present-tense visual language;
  every clip starts from scratch (hence the bible doctrine, §5); ~10s per clip is
  architectural.
- Clip length: `input[name="video_duration"]` range **min 3 / max 10 / default 5** —
  hidden behind Advanced Mode.
- Actor speech scripts: hard cap **100 characters total** per clip. Platform rejection:
  "Sorry, the number of characters in the actors scripts cannot exceed 100 characters."
- Files hosted ~60 days. Parallel generations: backend runs several at once, but the
  generator modal is effectively one-submit-at-a-time (button disables while in flight).
- Media library sort options: name / newest / oldest (`select.select-order`).

## 2. The generator ("Create Video From Prompt") — verified selectors

| Control | Selector | Default |
|---|---|---|
| Image Prompt | `#opt_prompt` / `textarea[name="prompt"]` | empty |
| Video and Audio Prompt | `#opt_video_prompt` / `textarea[name="video_prompt"]` | empty |
| Image Type | `select[name="select-type"]` → `human` / `2d` / `3d` / `photorealistic` / `other` | `human` |
| Auto-enhance image prompt | `input[name="auto_enhance_prompt"]` | checked |
| Use Consistent Character | `input[name="use_consistent_character"]` | varies |
| Lipsync HD Video | `input[name="talking_video"]` | unchecked |
| Narration Video | `input[name="narration_video"]` | unchecked |
| Video Only (No Sound) | `input[name="video_only"]` | unchecked |
| **Share this in the public gallery** | `input[name="shared"]` | **CHECKED — resets to ON on every modal reopen (verified repeatedly)** |
| Advanced Mode | `input[name="advanced_mode"]` | unchecked |
| Manual Video Length | `input[name="manual_video_length"]` + `input[name="video_duration"]` | hidden until Advanced Mode |
| Reference Photo / Photo 2 | `button.button-reference-photo2` **index 0 / index 1** (identical class!) | |
| Create Image | `button.button-generate-image-submit` | |
| Create Video (normal) | `button.button-generate-video-submit` | |
| **Create Video (lipsync mode)** | `button.button-generate-talking-video` — the normal one gets `.hidden` when `talking_video` is ticked | |
| Save Image (on hover over result) | `button.button-save-image` — **icon-only, `title="Save Image"`, no text** | |
| Generated image carousel | `.swiper-slide-pair-item`, selected = `.selected` — **JS click does not select; needs real/coordinate click** | |

Aspect: modal buttons `Landscape 16:9` / `Vertical 9:16` (with space). The preview-canvas
control elsewhere uses `Landscape16:9`/`Square1:1`/`Vertical9:16` (no space) — different control.

Interlocks (verified): ticking `talking_video` HIDES the Advanced Mode/length controls →
**set duration FIRST, tick lipsync LAST**. `Create Video` stays `disabled:true` until a
carousel image is selected. `use_consistent_character` + `Create Image` with no reference
attached → error "Please select a reference photo." → untick it for the very first
character/style image, re-tick after attaching.

## 3. The lipsync dialog ("Create Lipsync Audio") — verified

Opens AFTER clicking Create Video with `talking_video` ticked + image selected.
- Its own `textarea[name="prompt"]` = actor identification line ("Actor 1 is the …").
  **ID COLLISION: `#opt_prompt` matches the generator's field first — scope from
  `#opt_prompt_actor1` via `.closest()` or coordinates.**
- `#opt_prompt_actor1` = Actor 1 Script — THE speech field, ≤100 chars, live
  "Total characters" counter below.
- "Add Actor 2" reveals `#opt_prompt_actor2` (two-character scenes).
- Hidden voice panel exists (146 languages, emotion, speed, pitch, voice cloning) —
  UNVERIFIED how it is revealed; do not promise it.
- Submit = "Create" button INSIDE this dialog.

## 4. Media library, timeline, trim, export — verified

- Library nav: `a.nav-item.button-open-panel-medialibrary`. Folder tiles
  `.library-item.library-folder`; platform folders always present: **My AI Images**
  (Save Image target), **My AI Videos** (finished clips), plus My AI Audio / myvoices /
  Default. Tiles are NOT in the accessibility tree — click via JS on these selectors or
  by screenshot coordinates.
- Media tile (verified): `.library-item.show-on-hover-parent.timeline-source-item.library-item-draggable.ui-draggable`
  — **tile captions contain the generation prompt text** (truncated) → identify clips by
  caption; `data-ident` attribute ascending = creation order.
- Reference picker ("Select Image"): tile must be CLICKED and show selection
  (checkmark/`.selected`) BEFORE "Choose" does anything.
- Timeline drop targets: `div.track-row.ui-droppable`. **HTML5 drag events do nothing**
  (jQuery UI): real mousedown → ≥8 mousemove steps → mouseup, `bubbles:true`, real
  coordinates, `buttons:1`. Clips land as `div.brick.video`; playback order = drop order.
- Toolbar: Auto Align Clips (snaps end-to-end), Undo `button.button-undo`, zoom
  `button.timeline-button-zoomout/in`.
- Right-click a brick → context menu (verified items): Properties, Resize and move,
  Voice Changer, Save Audio, **Separate Audio and Video** (toast: "Audio and video have
  been separated"), Transition In/Out/Between, Add voiceover, Delete. Escape does NOT
  close the menu — click empty canvas.
- Trimming: bricks are `ui-resizable`. If handle mouse-events silently fail, drive the
  widget: `jQuery(brick).data('ui-resizable')._mouseDown/_mouseMove/_mouseUp` — **never
  `_mouseStop`** (strands module-level `mouseHandled=true`, blocks ALL drags page-wide).
  Audio layer renders a waveform — flat sections = silence boundaries.
- Export: top bar "Export Video" (NOT "Export Project") → File name / Quality (take
  High) / Resolution (FullHD) / Format (mp4) → Create. Renders server-side; result under
  "My Videos".
- Save/Export dialogs STACK duplicate DOM nodes on reopen — always target the LAST match.
- Screenshots can render stale vs the DOM — trust JS reads when they disagree.

## 5. The Bible Doctrine (works for ANY style, not just humans)

The engine has no memory. Whatever must stay visually identical across scenes gets
written as ONE frozen paragraph (40–70 words) — the **bible** — and re-pasted VERBATIM
into every scene's Image Prompt, with only the action/expression clause changing.

- Realistic human → **character bible**: age+gender+build, face/skin, hair, EXACT
  clothing, EXACT location/background, EXACT lighting.
- Stickman / cartoon / paper cutout / 3D → **style bible**: art style, line weight,
  palette, character design marks, background treatment, camera. Same freeze rule.
  (Stickman is production-proven: Image Type **other** + style bible + ≤100-char scripts.)
- One changed word = a different-looking result. Never write "same as before".

## 6. Distribution doctrine (learned the hard way — do not regress)

- **Copy-paste is the primary install path.** Agent-side URL fetching fails in Codex
  sandboxes (verified in production). The WEBSITE fetches the latest prompt from GitHub
  raw (browser has open CORS + normal network); the USER pastes; the AGENT needs no
  network. A fetch instruction may exist only as an optional extra.
- Every system prompt must be 100% self-contained, and must contain the rule:
  if any optional fetch fails, say nothing and proceed; NEVER ask the user to paste,
  upload, or fix networking.
- Never expose the backend model name. Never ship account-specific folder names.
- Public-gallery unticking is a privacy requirement in every workflow, every generation.

## 7. Model tiers (live-tested)

| Tier | Result |
|---|---|
| Claude Sonnet 5 (mid) | 8/8 mechanics + 9/9 full run incl. export — recommended default |
| Claude Haiku 4.5 (small) | 0/3 full runs — improvises destructively when stuck |
| GPT small tier ("Luna Light") | Failed to recover from a recoverable session reset |

Every shipped prompt recommends mid-tier and embeds Reliability Rules R1–R8 (canonical
text lives in the root `SYSTEM_PROMPT.md` — copy them verbatim into new workflows;
R8 = session loss is never fatal, resume from server-side library state).

## 8. Known error strings → meanings

| App message | Meaning / fix |
|---|---|
| "Please select a reference photo." | Consistent Character ticked with empty slot — untick for first image, or attach photo |
| "Sorry, the number of characters in the actors scripts cannot exceed 100 characters." | Actor script(s) too long — shorten below 100 total |
| "Please create scripts for the actors" | Lipsync submit with empty Actor 1 Script |
| "saved in your Media Library in the category 'My AI Images'." | Save Image succeeded |
| "Audio and video have been separated" | Separate Audio and Video succeeded |
