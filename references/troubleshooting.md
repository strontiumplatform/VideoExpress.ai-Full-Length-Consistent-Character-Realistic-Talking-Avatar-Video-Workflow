# Troubleshooting — VideoExpress.ai video build

Read this when something in the run does not behave as expected. Every entry is
**Symptom / Cause / Fix**. Selectors and labels come from `RECON.md`; anything not verified there
is explicitly marked **UNVERIFIED** and must be handled by screenshot → look → adapt.

---

## Operating rules while troubleshooting

These override the instinct to stop and check in.

| Rule | Detail |
|---|---|
| **Never ask a third question** | You asked exactly two questions at the start (reference photo vs. AI character + description, and topic). Recovery is autonomous. Make the reasonable default choice and keep going. |
| **Screenshot before you adapt** | If the DOM does not match `RECON.md`, take a screenshot, read what is actually on screen, then act on coordinates. Do not guess a selector. |
| **Budget every retry** | Max 2 regenerations per scene. Max 3 drag attempts per clip. Max 2 full re-tries of a failed generation job. When a budget is exhausted, continue the run with what you have and list the shortfall in your final summary. |
| **Never invent affordances** | If you need a control that is not in `RECON.md`, look for it on screen first. If it is not there, work around it — do not click something that "looks like" it. |
| **Untick the gallery checkbox every time** | `Share this in the public gallery` (`input[name="shared"]`) is **CHECKED BY DEFAULT**. Actively untick before every generation, re-untick after any modal reset, and verify by screenshot. |
| **Log as you go** | Keep a running scene → clip manifest (scene number, image prompt, video prompt, generated clip identifier). Almost every ordering failure below is fixed instantly if you have this and unfixable if you don't. |

---

## Quick triage

| What you see | Go to |
|---|---|
| Person looks like a different human in scene 4 than scene 1 | [1](#1-character-drifts-between-scenes) |
| A dialog with the word "agree" pops up out of nowhere | [2](#2-consistent-character-agreement-dialog-appears-unexpectedly) |
| Reference photo picker says "Empty." | [3](#3-reference-photo-picker-shows-empty) |
| Saved image never lands in `My AI Images` | [4](#4-saved-image-does-not-appear-in-my-ai-images) |
| Nothing is progressing; clip count is not rising | [5](#5-generation-queue-stalls-or-a-job-fails) |
| Dragging a clip to the timeline does nothing | [6](#6-drag-to-timeline-does-nothing) |
| Clips are on the timeline but in the wrong order | [7](#7-clips-land-out-of-order) |
| Black or empty space between clips | [8](#8-clips-have-gaps-on-the-timeline) |
| Banner about hosting files for 60 days | [9](#9-60-day-file-retention-warning) |
| A scene needs more than 10 seconds | [10](#10-a-scene-needs-more-than-10-seconds) |
| Two aspect-ratio controls, unsure which one matters | [11](#11-two-different-aspect-ratio-controls) |
| Wrong image got saved from the carousel | [12](#12-wrong-carousel-slide-was-saved) |
| A second person appears in the shot | [13](#13-unwanted-second-person-in-frame) |
| Nothing works — login, credits, hard failure | [Hard stops](#hard-stops) |

---

## 1. Character drifts between scenes

**Symptom.** The person's face, age, build, hair, clothing, or the room/lighting changes from one
generated clip to the next. The video reads as several different people.

**Cause.** One or more of, in order of likelihood:

1. The Image Prompt for a later scene did not re-state the **full** physical description. Abbreviating
   to "the same man" or "he" gives the model nothing to lock onto — there is no memory between calls.
2. `Automatically enhance my image prompt` is **ON by default**. The enhancer rewrites your prompt and
   can substitute or drop physical details, which silently mutates the character between scenes.
3. `Use Consistent Character` was not ticked for that scene, or the **Reference Photo** was not attached
   (the modal can come back without it after a generation).
4. The shot is not a close-up. The checkbox hint states: "For best results, please only use close up shots."
   Wide shots give the model too much freedom in the face.
5. A different reference image was chosen from the picker (see [7](#7-clips-land-out-of-order) for the
   sort-order trap).

**Fix.**

- Keep a single **canonical character block** in memory from scene 1 and paste it **verbatim** at the top
  of the Image Prompt for every scene: age, build, face, hair, exact clothing, plus the exact same
  location, background and lighting. Only the action/expression sentence changes. Do not paraphrase it,
  do not shorten it, do not let it drift as the video progresses.
- **Untick `Automatically enhance my image prompt`** for every scene after scene 1. Your prompt is already
  specific; the enhancer only adds variance. (Enhancing scene 1 is acceptable — if you do, copy the
  *resulting* description into the canonical block and reuse that verbatim.)
- Before each `Create Video`, confirm on screen: `Use Consistent Character` ticked, **Reference Photo**
  showing an attached image, `Image Type` = `human`, aspect = `Vertical 9:16`.
- Write shots as close-ups or medium close-ups. Avoid "wide shot", "full body", "from across the room".
- If a clip still drifts: regenerate that scene once with the canonical block re-pasted. If it drifts
  again, accept it and note it in the summary — do not burn the whole run on one scene.

**Prevention beats repair.** Drift is by far the most common quality failure and it is almost entirely
caused by shortening the prompt. Restating 60 words per scene is not redundant; it is the mechanism.

---

## 2. Consistent Character agreement dialog appears unexpectedly

**Symptom.** After ticking `Use Consistent Character` (or after clicking the `Consistent Character`
button at the modal foot), a dialog appears and the flow blocks.

**Cause.** The platform shows a one-time agreement/consent dialog on first use of Consistent Character
on an account. The product owner confirms it exists; it did **not** render during recon because the
checkbox was already ticked on that account.

**UNVERIFIED:** the exact wording, buttons, and selector of this dialog. Do not assume an "I agree" label.

**Fix (defensive).**

1. Immediately after ticking `Use Consistent Character`, take a screenshot.
2. If a modal/overlay is present, read its visible text.
3. If the text contains "agree", "terms", "consent", or similar, tick any required checkbox inside it and
   click its affirmative button (whatever it is actually labelled — read it, don't assume).
4. Screenshot again to confirm the dialog is gone and `Use Consistent Character` is still ticked. The
   dialog can reset the checkbox; re-tick it if so.
5. Expect this once per account. If it reappears on a later scene, handle it the same way and carry on.
6. If the dialog's text is anything other than a consent/agreement — for example a payment, upgrade, or
   permission request — **do not accept it**. Close it, continue without Consistent Character if you must,
   and report it in the final summary.

---

## 3. Reference photo picker shows "Empty."

**Symptom.** You open **Reference Photo** → `My AI Images` and the folder body renders the literal text
`Empty.` — no tiles.

**Cause.** Almost always timing: `Save Image` has been clicked but the file has not finished writing to
the library yet. **UNVERIFIED:** whether `Save Image` writes immediately or after a delay. Treat it as
delayed. Secondary cause: you are in the wrong folder (there are ~20 folders in this account, including
several with similar names).

**Fix.**

1. Confirm the folder first. Folder tiles are `div.library-item.library-folder.button-folder` with titles in
   `div.library-folder-title`. You want exactly **`My AI Images`** — not `My AI Videos`, not `Default`,
   not one of the personal media folders.
2. Poll. Click `Back`, re-open `My AI Images`, and re-check. Poll roughly every 5 seconds for up to
   60 seconds before treating it as a real failure. (**UNVERIFIED** timing — this is a defensive budget,
   not a documented SLA.)
3. Note that **folder tiles and image tiles are not exposed in the accessibility tree** — `read_page` will
   only show `Choose` and `Close`. Detect the `Empty.` state and the tiles by screenshot, or by querying the
   verified selectors (`div.modal-library-items.scroll-y` for the item container).
4. If still `Empty.` after the poll window, go to [4](#4-saved-image-does-not-appear-in-my-ai-images).

---

## 4. Saved image does not appear in `My AI Images`

**Symptom.** Polling has expired and the folder is still empty, or the newest item is clearly an older
image rather than the one you just generated.

**Cause.** The `Save Image` click did not register, was clicked while the carousel was mid-transition, or
the save silently failed.

**Fix.**

1. Return to the **Create Video From Prompt** modal. The result area above the form still holds the
   generated carousel (`Zoom Image`, `Save Image`, Previous slide / Next slide).
2. Make sure the correct slide is displayed, then click **`Save Image`** again. Watch for any confirmation
   or state change on screen.
3. Re-open **Reference Photo** → `My AI Images` and poll again per [3](#3-reference-photo-picker-shows-empty).
4. If the carousel is gone (modal was reset), regenerate: paste the canonical character block into the
   **Image Prompt**, click **`Create Image`**, wait for the carousel, click **`Save Image`**.
5. Fallback if the image genuinely will not save: use `Use from Library`
   (`button.button-image-from-library`) to pick an existing suitable image, or — if the user supplied their
   own reference photo at Q1 — use that photo as the reference and skip AI character generation entirely.
   Either way you keep a single locked reference for all scenes, which is what matters.
6. Whichever reference you end up with, **use that same one for every scene**. Never mix references
   mid-video.

---

## 5. Generation queue stalls or a job fails

**Symptom.** `Create Video` does nothing, errors, or the clip count in `My AI Videos` stops rising while
jobs appear to be pending.

**Cause.**

- **Parallel limit.** Up to **5** video generations run in parallel per account. A 6th submission while
  5 are in flight will be rejected or ignored. This is expected, not a bug.
- A single job failed server-side.
- **UNVERIFIED:** the exact completion signal for a video job. Recon could not confirm a reliable
  in-UI job status indicator.

**Fix.**

1. **Respect the batch limit.** Submit exactly 5, wait for that batch to finish, then submit the next.
   With 6–8 scenes that is two batches (e.g. 5 + 3).
2. **Use the library as ground truth.** Poll Media Library → `My AI Videos` and count items. The batch is
   done when the count has risen by the number of jobs you submitted. Poll roughly every 30 seconds, with a
   budget of ~15 minutes per batch. (**UNVERIFIED** — defensive budget.)
3. **Partial batch.** If 4 of 5 land and the count stalls for two consecutive poll windows, treat the
   missing one as failed: re-submit that single scene with the identical Image Prompt (canonical character
   block) and Video and Audio Prompt. Max 2 re-submissions per scene.
4. **Zero progress.** If nothing lands at all after the budget expires, screenshot and read the page for an
   error message, a credit/quota notice, or a session-expired state. See [Hard stops](#hard-stops).
5. **Do not** resubmit blindly while jobs are pending — you will exceed the parallel limit and stall the
   whole queue. Count first, then submit.
6. If one scene is unrecoverable, drop it and build the video from the clips you have. A 7-scene video that
   exports beats an 8-scene video that never finishes.

---

## 6. Drag to timeline does nothing

**Symptom.** You drag a clip from the media library onto the timeline and nothing lands. No error, no clip,
no visual feedback.

**Cause.** This is the single most likely mechanical failure in the whole run, and it has two distinct causes.

1. **Synthetic HTML5 drag events do not work here.** The timeline uses **jQuery UI 1.12.1** droppables
   (`div.track-row.ui-droppable`). jQuery UI listens to **mouse events**, not the HTML5 drag-and-drop API.
   Dispatching `dragstart` / `dragover` / `drop` will do absolutely nothing, silently.
2. **The item is not draggable yet.** Media items only become `.ui-draggable` **after a media folder has
   been opened**. At the folder-list level there are zero draggables — you are dragging a tile that has no
   drag behaviour attached.

**Fix.**

1. **Open the folder first.** Media Library (`a.nav-item.button-open-panel-medialibrary`) → open
   **`My AI Videos`**. Confirm draggables exist before attempting anything:
   `document.querySelectorAll('.ui-draggable').length` should be > 0, and
   `document.querySelectorAll('div.track-row.ui-droppable').length` should be ≥ 1 (2 exist on a fresh project).
   This is inspection only — do not implement the drag in JavaScript.
2. **Use a real mouse sequence.** `mousedown` on the clip → **several** `mousemove` steps across the screen →
   `mouseup` over the target `div.track-row`. In Claude browser tools that means `computer` with
   `left_click_drag`, or a scripted mouse-event sequence with intermediate moves. A single jump from source
   to target often fails — jQuery UI needs the intermediate movement to start and track the drag.
3. **Move slowly and in steps.** If `left_click_drag` fails, retry with an explicit sequence of 5–10
   intermediate `mousemove` points and a short hold before `mouseup`.
4. **Make the target visible.** If the timeline is scrolled or zoomed such that the track row is small or
   off-screen, click `button.timeline-button-zoomout` until the whole track row is comfortably visible,
   and scroll `div.timeline-body` so the drop point is on screen. You cannot drop onto something that
   isn't rendered where you think it is.
5. **Verify after every single drop.** Screenshot and confirm the clip actually appeared on the track before
   dragging the next one. Do not batch drags and check at the end — you lose the ability to tell which one
   failed.
6. Max 3 attempts per clip. If a clip still refuses to land, drag the remaining clips, then retry the
   stubborn one last; a track that already has clips often gives a larger, easier drop area.

---

## 7. Clips land out of order

**Symptom.** The clips are on the timeline but the story plays in the wrong sequence.

**Cause.**

- Dropped in the wrong order. The library sorts **Newest first** (verified for the Select Image modal;
  **UNVERIFIED** for the Media Library panel, but assume newest-first). Newest-first means your library
  listing is the **reverse** of your scene order. Dragging top-to-bottom from the library therefore builds
  the video backwards.
- Auto-generated clip names carry no scene number, so visual identification is unreliable.
- A clip was dropped at a horizontal position that placed it before an earlier clip on the track.

**Fix.**

1. **Keep the manifest.** Record scene number → clip identifier (thumbnail, position in the library grid,
   name) at the moment each generation completes. Do this during generation, not at drag time.
2. **Drag in scene order, left to right.** Scene 1 first, dropped at the far left of the track row; then
   scene 2 immediately to its right; and so on. One at a time, verify each.
3. **Correct the sort assumption.** If the library shows newest first, scene 8 is at the top and scene 1 is
   at the bottom. Read the grid accordingly rather than dragging in listed order. If a `Sort` control is
   available in the panel, you may set it to oldest-first — but verify what it actually did before relying
   on it (**UNVERIFIED** for the Media Library panel).
4. **Repair without redoing everything.** Use `button.timeline-button-copy` / `button.timeline-button-paste`
   to relocate a misplaced clip, or drag it along the track to the correct slot (same real-mouse-sequence
   rule as [6](#6-drag-to-timeline-does-nothing)), then run `Auto Align Clips` to re-close the spacing.
5. Always do a final left-to-right read of the timeline against your manifest before `Export Video`.

---

## 8. Clips have gaps on the timeline

**Symptom.** Black frames, silence, or visible empty space between clips; the exported video is longer than
the sum of the clips.

**Cause.** Drops land wherever the mouse released. jQuery UI does not snap clips together for you, so any
imprecision in the drop x-position leaves a gap.

**Fix.**

1. Click **`Auto Align Clips`** in the timeline toolbar (a text link, not a class-identified button). This
   snaps dropped clips end-to-end with no gaps — it is the intended tool for exactly this and should be run
   as a standard step, not only when you notice a problem.
2. Run it **after all clips are placed**, not between drops.
3. Screenshot and confirm the clips are flush. If gaps remain, drag the offending clip left until it abuts
   its neighbour, then run `Auto Align Clips` again.
4. Only then `Export Video`.

---

## 9. 60-day file retention warning

**Symptom.** A banner appears in the library modal: "For your convenience we host your project's files for
up to 60 days, please be sure to download them."

**Cause.** Informational platform notice about hosted file lifetime. It is not an error and does not block
anything.

**Fix.**

1. Do not treat it as a failure and do not stop the run. Dismiss it if it obstructs a control; otherwise
   ignore it.
2. After `Export Video`, download the finished file so it exists outside the platform. (Downloading requires
   the user's go-ahead in some agent setups — if a download prompt or permission gate appears, surface it in
   your final summary rather than forcing it.)
3. **Always state the retention limit in your final summary**, in plain language: the exported video and the
   source clips are hosted for up to 60 days, so save a local copy. A non-technical user will not read the
   banner and will assume the files live there forever.
4. The project itself can also be preserved via `Save`, `Save Project As`, or `Export Project` in the top
   bar; finished renders are listed under `My Videos`.

---

## 10. A scene needs more than 10 seconds

**Symptom.** A scene's script does not fit in the clip, speech is cut off mid-sentence, or you want a
15–20 second beat.

**Cause.** The backend video engine has a hard architectural limit of roughly **10 seconds per generated clip** — it is not a plan restriction and no setting raises it.
This is a model constraint, not a settings problem. No slider position changes it.
**UNVERIFIED:** whether the `Manual Video Length, sec` slider visually caps at 10 in the UI — the observed
default is 5.

**Fix.**

1. **Split the beat into two scenes.** Two 8-second clips of the same character, same location, same
   clothing, cut back to back, read as one continuous 16-second moment. This is the correct answer almost
   every time.
2. Keep the canonical character block identical across the split so the join is invisible
   (see [1](#1-character-drifts-between-scenes)).
3. **Shorten the speech.** Roughly 20–25 spoken words fit comfortably in 8–10 seconds. If the line is longer,
   cut it, don't compress delivery.
4. If you use `Manual Video Length, sec`, tick the checkbox and set the slider to **10 or below**. If the
   slider allows a higher value, still do not exceed 10 — the model will not honour it.
5. Target shape for the whole video: ~6–8 scenes, each ≤10s, ~60s total. If the script is running long, cut
   scenes rather than stretching them.

---

## 11. Two different aspect-ratio controls

**Symptom.** You set vertical, but generated clips come back landscape — or you cannot tell which of two
similar-looking controls to use.

**Cause.** There are genuinely **two** aspect-ratio controls in the app, with nearly identical labels:

| Control | Labels | What it does |
|---|---|---|
| **Inside the `Create Video From Prompt` modal**, at the top | `Landscape 16:9`, `Vertical 9:16` — **with a space** | **Governs generation.** This is the one that matters. |
| Elsewhere, for the preview canvas | `Landscape16:9`, `Square1:1`, `Vertical9:16` — **no space** | Preview/canvas framing only. Does not control generation. |

**Fix.**

1. **Disambiguate by scope, not by label.** The correct control is the one rendered *inside* the
   Create Video From Prompt modal, above the form. If the control you are looking at is not inside that
   modal, it is the wrong one.
2. **Use the `Square 1:1` tell.** The generation toggle has only two options. If you can see a square
   option, you are on the preview control — go back into the modal.
3. Set **`Vertical 9:16`** in the modal, and confirm it is still selected on **every** scene — the modal can
   reset between generations. Verify it in the same pre-flight check as the Consistent Character checkbox
   and the reference photo.
4. Vertical 9:16 is the default for this workflow (better quality per the product owner) and produces the
   right format for phone-first placement.
5. If clips have already generated in the wrong aspect ratio, they must be regenerated — the preview control
   will not fix them. There is an `Outpainting — Change Aspect Ratio` tool in the Create with AI panel, but
   its behaviour is **UNVERIFIED** for this workflow; regenerating is the reliable path.

---

## 12. Wrong carousel slide was saved

**Symptom.** The reference photo in the picker is not the image you intended — a different pose, framing,
or a visibly different person.

**Cause.** `Create Image` produces a **carousel** of candidates (`Previous slide` / `Next slide`).
**`Save Image`** saves whichever slide is currently displayed. Clicking save immediately, or while the
carousel is mid-transition, saves the wrong candidate.

**Fix.**

1. Step through the carousel with `Next slide` and look at each candidate. Use `Zoom Image` if you need a
   closer look at the face.
2. Choose the best close-up with a clear, well-lit, front-facing face — that is the one Consistent Character
   has the easiest time reproducing.
3. Let the slide settle, then click **`Save Image`** once.
4. If the wrong one saved: go back to the carousel, select the right slide, `Save Image` again, then pick the
   **newest** item in `My AI Images` (sort default is `Newest`, so it is first). Confirm by eye that the tile
   is the image you meant before clicking `Choose`.

---

## 13. Unwanted second person in frame

**Symptom.** A stranger, a hand, or a second face appears in a clip that should show one person.

**Cause.** Either **Reference Photo 2** has an image attached — it is the second character slot for
two-person scenes — or the Image Prompt implies another person ("talking to a customer", "handing her the
cup", "in a busy office").

**Fix.**

1. Confirm **Reference Photo 2** is empty. It is marked Optional; for a single-character video it stays empty.
2. Rewrite the Image Prompt so only one person exists in the frame. Remove implied second parties; describe
   the character speaking directly to camera instead.
3. Prefer close-ups — they physically exclude bystanders.
4. Regenerate that scene with the canonical character block intact.

---

## Hard stops

Stop the run, do not retry, and report clearly in your final summary if you hit any of these. Do not attempt
to resolve them yourself and do not enter credentials or payment details.

| Situation | What to report |
|---|---|
| Logged out / session expired | The account is signed out of app.videoexpress.ai and needs to be signed in again before the video can be built. Say how far the run got. |
| Credit, quota, plan, or payment gate | Quote the on-screen message verbatim, name the step it blocked, and list what was completed. Never accept an upgrade or purchase. |
| A dialog asking for permissions, payment, or terms beyond the Consistent Character consent | Quote the text, name where it appeared, state that you did not accept it. |
| Repeated server errors across multiple scenes | Say which scenes failed, how many attempts each got, and that the platform appears to be failing rather than the prompts. |

In every hard stop, report **what did succeed** — saved reference image, completed clips, clips already on
the timeline — so the work is recoverable rather than lost.

---

## Unverified-behaviour register

Everything in this list must be handled by screenshot → look → adapt. Never hard-code against it.

| Item | Status |
|---|---|
| Consistent Character agreement dialog: wording, buttons, selector | UNVERIFIED — detect any modal containing "agree"/"terms"/"consent" and read it |
| Whether `Save Image` writes to `My AI Images` immediately or after a delay | UNVERIFIED — poll the folder |
| Video generation completion signal | UNVERIFIED — poll the `My AI Videos` item count as ground truth |
| Whether `Manual Video Length, sec` caps at 10s in the UI | UNVERIFIED — never set above 10 regardless |
| Sort order and controls in the Media Library panel (Newest-first is verified only for the Select Image modal) | UNVERIFIED — verify visually before relying on listing order |
| `Outpainting — Change Aspect Ratio` as a fix for wrong-aspect clips | UNVERIFIED — regenerate instead |
| Polling intervals and timeouts quoted above | Defensive budgets, not documented platform behaviour |
