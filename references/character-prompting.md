# Character Prompting — Keeping One Person Looking Like One Person

This file is for the **agent**, not the customer. The customer answers two questions and never sees any
of this. Everything here is about what you type into the two textareas in the
**Create Video From Prompt** modal so that six separately-generated clips look like six shots of the
same human being in the same room on the same afternoon.

The backend is a diffusion-transformer latent video engine that renders picture and audio together in one pass. Never name or speculate about the specific model to a customer. It has no memory between generations. Every clip is generated from
scratch. The *only* things carrying identity forward are:

1. The **Reference Photo** (one saved image, reused for every scene) — locks the face.
2. The **character bible text** (one block of words, reused verbatim for every scene) — locks
   everything the reference photo can't hold on its own: wardrobe, room, lighting, framing.

Drop either one and the character mutates between scenes. Customers notice instantly and it is the
single loudest signal of "AI slop."

---

## 1. The character bible concept

Write the physical description **once**. Store it in your working notes as a fixed string. Paste that
exact string into the **Image Prompt** field for every single scene, byte for byte. Do not retype it.
Do not paraphrase it. Do not "improve" it on scene 4 because you thought of a better word.

The bible is a **frozen block**. Only one line changes per scene — the action line, appended after the
block.

```
IMAGE PROMPT = [ CHARACTER BIBLE — identical every scene ] + [ ACTION LINE — the only variable ]
```

Two hard rules:

- **The bible never shrinks.** A reader might find it repetitive. The model does not. Re-stating
  "charcoal-grey crew-neck sweater" in scene 6 is what prevents scene 6 from inventing a blazer.
- **The bible describes a close-up.** Verified in the app: ticking **Use Consistent Character**
  surfaces the hint *"For best results, please only use close up shots."* Build the bible around
  shoulders-up framing and it will hold. Build it around a wide shot and it will not.

### Where the bible comes from

You get exactly one input from the customer about the character, in Q1. Two paths:

| Q1 answer | How you build the bible |
|---|---|
| **"I'll upload my own photo"** | After the photo is in the library, screenshot it in the browser, look at it, and write the bible *describing what you actually see* — age range, build, skin tone, hair, facial hair, eye colour, distinguishing marks, exact garment and colour. Then invent and freeze a location, lighting and framing (the photo may not show a usable one). |
| **"Generate a character"** + short description | Expand their short description into a full bible. Fill **every** slot below with a specific concrete choice. You are allowed to invent everything they didn't specify — that is expected. Never leave a slot vague or open. |

Either way you write the bible yourself and you never go back to the customer to ask about it.

---

## 2. Character bible template (fill in every blank)

Copy this, fill it, then never touch it again for the rest of the video.

```
Close-up portrait of a [AGE]-year-old [GENDER], [BUILD] build, [SKIN TONE] skin,
[HAIR LENGTH + STYLE + COLOUR], [FACIAL HAIR or "clean-shaven"], [EYEBROW DESCRIPTION],
[EYE COLOUR] eyes, [ONE DISTINGUISHING MARK], wearing [EXACT GARMENT + COLOUR + NECKLINE]
[SECOND LAYER or "no second layer"], [ACCESSORIES or "no jewellery, no glasses"].
[PRONOUN] is [SEATED / STANDING] in [LOCATION] with [WHAT IS DIRECTLY BEHIND THE HEAD]
and [ONE BLURRED BACKGROUND OBJECT + WHERE IT SITS IN FRAME].
[LIGHT DIRECTION + QUALITY + COLOUR TEMPERATURE], [WHERE THE SHADOW FALLS].
Shallow depth of field, shoulders-up framing, camera at eye level, static camera.
```

Slot-filling rules:

| Slot | Rule |
|---|---|
| Age | Give a number, not a range. "34-year-old", not "in his thirties". |
| Build | One word: slim / medium / athletic / broad / heavy-set. |
| Hair | Length **and** style **and** colour. "Short black hair with a neat fade" beats "dark hair". |
| Garment | Name the item, the colour, and the neckline. Colour must be a plain word — "charcoal-grey", not "slate ombré". |
| Distinguishing mark | Exactly one, small, and on a specific side. A mole, a scar, freckles across the nose. This is a cheap, high-value anchor. |
| Location | One room. Name what is *directly behind the head* — that is the pixel region the model most often reinvents. |
| Background object | One, blurred, with a stated frame position. Two or more objects give the model more to get wrong. |
| Lighting | Direction + quality + temperature + where the shadow lands. "Soft warm key light from the front-left, gentle shadow on the right side of his face." |
| Framing | Always end with the close-up/static-camera clause. Verbatim, every scene. |

Things that must **not** appear in the bible: brand names, camera model names, lens focal lengths,
anything that changes over time ("halfway through the day"), and any plural or unspecified noun
("some plants", "books").

---

## 3. The two prompt fields

The modal has two textareas and they do different jobs. Verified placeholders tell you exactly how
the platform expects them to be used:

| Field | Verified placeholder | What you put in it |
|---|---|---|
| **Image Prompt** | "A man drinking coffee in a rainy cafe" | The frozen character bible + the one-line action/expression for this scene. **No speech. No audio. No dialogue.** |
| **Video and Audio Prompt** | "He takes a sip of coffee. Include the sound of raindrops and him saying 'delicious' under his breath" | The motion, the spoken line in quotes, the accent and tone, and the room sound. |

### Image Prompt template

```
[CHARACTER BIBLE — pasted verbatim]
[ACTION LINE: one sentence. Expression and micro-movement only.]
```

Good action lines — note they change nothing structural:

- `He looks straight into the camera with a slight frown, head tilted a few degrees to the left.`
- `He raises his eyebrows and gives a small open-mouthed laugh.`
- `He leans a few inches toward the camera, expression serious, jaw set.`
- `He nods once, mouth closed, a small confident smile forming.`

Bad action lines — every one of these breaks the shot:

- `He stands up and walks to the window.` (leaves the framing, reveals the room)
- `He holds up his phone to show the screen.` (introduces an unspecified object)
- `Cut to him later that evening.` (changes lighting)
- `He gestures widely with both hands.` (pulls hands into a shoulders-up frame; hands are where
  generative video fails most visibly)

### Video and Audio Prompt template

```
[MOTION: one short sentence describing how he moves while speaking.]
He says, "[SPOKEN LINE — 20-25 words max for a 10-second clip]"
[VOICE: accent + tone + pace.]
[ROOM SOUND: one clause.]
```

Filled example:

```
He speaks directly to the camera, small natural head movements, hands stay out of frame.
He says, "Your morning routine isn't broken. You just built it for somebody else's life."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

Do not repeat the character bible in this field. It is the image that carries appearance; this field
carries performance.

---

## 4. Specifying voice and accent in text

There is no voice-picker dropdown in this modal. Voice is specified **in words**, inside the
**Video and Audio Prompt**, right after the spoken line. Always give three things — accent, tone,
pace — in that order, and use the identical wording in every scene. A voice that shifts between
clips is as jarring as a face that shifts.

| Want | Write |
|---|---|
| Default / broadest reach | `Neutral American English accent, warm confident tone, natural conversational pace.` |
| Authority, expert framing | `Neutral American English accent, calm measured tone, deliberate unhurried pace.` |
| Energetic hook or ad read | `Neutral American English accent, bright energetic tone, brisk pace.` |
| British | `Standard British English accent (Southern England), warm confident tone, natural conversational pace.` |
| Australian | `Australian English accent, friendly relaxed tone, natural conversational pace.` |
| Softer, intimate | `Neutral American English accent, low warm tone, slow measured pace, speaking quietly.` |

Rules:

- Pick one line from the table (or write one in the same shape) and reuse it **verbatim** in all six
  scenes. It is part of the frozen set, like the bible.
- Never write a gendered voice descriptor that contradicts the character in the image.
- Never name a real person's voice, a celebrity, or a public figure. Describe the sound, not a human.
- Keep spoken lines to **20-25 words** per clip. That is roughly 10 seconds at conversational pace,
  which is the engine's hard per-clip ceiling. Over-writing the line is the most common cause of a
  clip that cuts off mid-sentence.
- End with a room-sound clause (`Quiet indoor room tone, no music.`). Silence is not a default; if
  you don't specify the acoustic, scenes can arrive with mismatched ambience.

For talking scenes, tick **Lipsync HD Video**. Its verified in-app hint: *"works best for Human,
Photorealistic (Cinematic), or 3D images that include a person"* — which is exactly the configuration
this workflow uses (Image Type: `human`). Do not tick **Video Only (No Sound)** for any scene where
the character speaks.

---

## 5. Why close-ups win

The platform tells you directly. Ticking **Use Consistent Character** displays:

> "For best results, please only use close up shots"

Take that literally. Beyond it being the vendor's own guidance, the mechanics are simple: a
shoulders-up frame gives the model a small, tightly-constrained set of pixels to keep stable — face,
hair, collar, a soft wall. A medium or wide shot adds hands, torso proportions, full garment cut,
furniture, floor, and room geometry, and every one of those is a fresh chance to differ from the last
clip. Vertical 9:16 compounds the benefit: the frame is already narrow, so a close-up fills it and
there is almost no background left to drift.

Practical consequences:

- Every scene ends with `shoulders-up framing, camera at eye level, static camera`.
- Hands stay out of frame. Say so in the video prompt when there's any risk.
- No camera moves. No pans, dollies, zooms, orbits, or handheld. A camera move is a request to render
  background that was never described — see the drift table below.
- If you genuinely need visual variety, get it from **expression and head angle**, not from framing.
  A slight frown, a raised brow, a lean-in, a single nod, a small laugh. Six scenes of the same shot
  with six different expressions reads as intentional; six different framings reads as broken.

---

## 6. Worked example — 6 scenes, ~60 seconds

Topic: *why your morning routine isn't working*. Vertical 9:16, Image Type `human`, Use Consistent
Character ON, Reference Photo = the saved hero image, Lipsync HD Video ON, Manual Video Length 10s,
public gallery OFF.

### The frozen character bible

Written once. Pasted verbatim as the opening of all six Image Prompts.

```
Close-up portrait of a 34-year-old man, medium build, warm olive skin, short black hair with a neat
fade, trimmed dark beard, thick dark eyebrows, brown eyes, a small mole on his left cheek, wearing a
charcoal-grey crew-neck sweater over a white t-shirt collar, no jewellery and no glasses. He is
seated in a small home office with a plain warm-grey wall directly behind his head and a blurred
brass floor lamp glowing in the top-left of the frame. Soft warm key light from the front-left,
gentle shadow on the right side of his face. Shallow depth of field, shoulders-up framing, camera at
eye level, static camera.
```

### The frozen voice line

```
Neutral American English accent, warm confident tone, natural conversational pace.
```

### Scene 1 — hook

**Image Prompt**
```
[BIBLE VERBATIM]
He looks straight into the camera, eyebrows slightly raised, expression direct and open.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, small natural head movements, hands stay out of frame.
He says, "Your morning routine isn't broken. You just built it for somebody else's life. Here's what actually changed mine."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Scene 2 — the confession

**Image Prompt**
```
[BIBLE VERBATIM]
He gives a small tired half-smile, head tilted a few degrees to the right.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, a small shake of the head, hands stay out of frame.
He says, "I woke up at five for a year. Cold plunge, journal, gratitude list. I was exhausted by ten every single morning."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Scene 3 — the real problem

**Image Prompt**
```
[BIBLE VERBATIM]
He leans a few inches toward the camera, expression serious, jaw set.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, leaning in slightly, hands stay out of frame.
He says, "The problem was never the wake-up time. It was that I spent my sharpest two hours answering other people's email."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Scene 4 — the change

**Image Prompt**
```
[BIBLE VERBATIM]
He nods once, mouth closed, a small confident smile forming.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, one firm nod, hands stay out of frame.
He says, "So I flipped it. First thing I touch is the one task that scares me. No phone, no inbox, no exceptions."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Scene 5 — the proof

**Image Prompt**
```
[BIBLE VERBATIM]
He raises his eyebrows and gives a small open-mouthed laugh, eyes creasing.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, brief light laugh, hands stay out of frame.
He says, "It took about nine days to feel normal. By week three I was finishing the hard thing before most people started."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Scene 6 — the call to action

**Image Prompt**
```
[BIBLE VERBATIM]
He looks straight into the camera, expression calm and steady, chin slightly lowered.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, still and steady, hands stay out of frame.
He says, "Pick one task tonight. Do it first tomorrow, before anything else. That's the whole routine. Try it for nine days."
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### What changed across the six scenes

| Frozen (identical in all 6) | Variable (changes each scene) |
|---|---|
| Entire character bible | The single action/expression line at the end of the Image Prompt |
| Location, wall, lamp, lighting, shadow | The motion sentence in the video prompt |
| Framing and camera clause | The spoken line |
| Accent / tone / pace line | — |
| Room-sound clause | — |
| Reference Photo | — |

Six scenes at ~10s each lands at roughly 60 seconds. If the target runtime shifts, change the number
of scenes, not the per-clip length — the 10s ceiling is a model limit, not a preference.

---

## 7. Drift triggers — the things that break the character

Every row below is a real failure mode. Scan your six prompts for all of them before you queue the
first batch.

| Trigger | What actually happens | Fix |
|---|---|---|
| **Changing wardrobe words** — "sweater" in scenes 1-3, "jumper" or "top" or "pullover" later | The garment re-renders as a different item, often a different colour | One noun, one colour, chosen once. Copy-paste, never retype |
| **Dropping a wardrobe detail** — omitting "over a white t-shirt collar" in one scene | The neckline changes; the collar vanishes or becomes a v-neck | The bible never shrinks. Every scene gets the full block |
| **Changing location words** — "home office" becomes "study" or "room" | New furniture, new wall colour, new depth | One location noun for the whole video |
| **Vague pronouns and unnamed nouns** — "the light", "some plants", "his desk" | The model invents a fresh instance each time | Every noun gets a describing adjective and, for background objects, a frame position |
| **Camera moves that reveal background** — pans, dollies, orbits, zoom-outs, "handheld" | The model must render room it was never given, and improvises differently each clip | `static camera` in every Image Prompt. No motion verbs applied to the camera |
| **Standing up, walking, turning away** | Leaves the close-up; body proportions and full garment appear and diverge | Action lines describe expression and head movement only |
| **Hands entering frame** — gesturing, pointing, holding an object | Hands are the highest-variance region; count and shape wobble between clips | `hands stay out of frame` in the video prompt |
| **Time-of-day language** — "later", "that evening", "the next morning" | Lighting and shadow direction flip | Keep the same lighting clause. Never reference elapsed time in the prompt |
| **Adding props mid-sequence** — a mug, headphones, a phone | The prop persists into later clips inconsistently, or displaces the framing | No props. If a prop is essential it goes in the bible from scene 1 |
| **Rewriting the bible for "variety"** | Paraphrase is a new description. The model reads it as a new person | The bible is copy-paste only. Variety comes from the action line |
| **Changing the accent/tone line between scenes** | Audible voice shift mid-video — as damaging as a face shift | Freeze the voice line alongside the bible |
| **Letting the spoken line run long** | The clip exceeds ~10s and truncates mid-sentence | 20-25 words per clip, counted |
| **Leaving prompt auto-enhancement on for scene prompts** | The enhancer rewrites your text before generation, which can substitute its own wardrobe and background wording | See the note below |

### Note on the auto-enhance checkboxes (partly unverified)

Two verified controls exist: **Automatically enhance my image prompt** (verified ON by default) and
**Automatically enhance my video prompt**. Auto-enhance is useful for the *first* hero image, where
you want the model to embellish freely. It works against you once the character is locked, because an
enhancer that rewrites your frozen block is by definition producing a different block each time.

**Unverified:** the exact rewriting behaviour of these enhancers, and whether turning them off
measurably reduces drift, were not observed in recon. Handle defensively at runtime:

1. Leave enhancement **on** for the hero-image generation.
2. Before generating scene videos, screenshot the modal, locate the two enhance checkboxes, and
   untick them.
3. If the checkboxes are not present or not clickable in the current build, proceed anyway and
   compare scene 1 and scene 2 outputs; if the character visibly differs, note it and continue —
   do not stop to ask the customer.

### Runtime consistency check (defensive, unverified signals)

After the first batch of 5 completes, look at the results. If a clip shows a different garment,
different wall, or a visibly different face, regenerate that one clip with the identical prompt
before continuing — the fault is usually sampling variance, not the prompt. Do not edit the bible in
response to one bad clip. There is no verified in-app "consistency score" or similar signal; judge by
eye from a screenshot.

---

## 8. Two-person scenes

**Reference Photo 2** exists in the modal and is marked Optional — it is the second character slot.
This workflow does not use it by default. If a video genuinely needs two people, both characters need
their own full bible, both bibles go in the same Image Prompt, and the framing clause changes to a
two-shot — which contradicts the close-up guidance and will drift more. Default to one character.

---

## 9. Pre-flight checklist

Before queuing any batch, confirm all of these against your six prompt pairs:

- [ ] The character bible string is **byte-identical** in all six Image Prompts
- [ ] Only the final action line differs between Image Prompts
- [ ] No Image Prompt contains speech, dialogue, or audio words
- [ ] Every Video and Audio Prompt has: motion sentence, quoted line, voice line, room-sound clause
- [ ] The voice line is byte-identical in all six
- [ ] Every spoken line is 20-25 words
- [ ] No camera-move verbs anywhere
- [ ] No hands, no props, no standing, no walking
- [ ] Aspect is **Vertical 9:16**, Image Type is **human**
- [ ] **Use Consistent Character** is ticked and the Reference Photo is set to the saved hero image
- [ ] **Share this in the public gallery** is **unticked**
