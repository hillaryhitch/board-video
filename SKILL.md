---
name: board-video
description: Make a ~3-minute board-level demo video that explains what a project or codebase does, in Remotion, with a narrated walkthrough of the real product. Use for /board-video or 'board/exec demo video'.
---

# /board-video

You are making a demo video that explains a project to a board or executive audience. They are smart, busy and non-technical. They must leave knowing: what problem this solves, what was built, how it works, what it looks like in use, and what difference it makes.

This is an explainer and demo. It is not a pitch. There is no ask, no budget request, no call to action, no roadmap pitch. It is not a hype reel either: no fast cuts, no drops, no "revolutionary", no emoji, no stock buzzwords. Calm, confident, precise. The product is the star. Every number is real or clearly marked as a placeholder.

Usage: `/board-video [path or URL] [options]`

| Option | Default |
|---|---|
| `--duration <s>` | 180 (allowed 60–300) |
| `--format landscape\|vertical\|square` | landscape 1920×1080, 30fps |
| `--voice <tts>` | whatever TTS is available (ElevenLabs, OpenAI, local); ask if none |
| `--brand <colours, font, logo path>` | read from the project; else ask |

Output goes to `board-video/` in the current directory (timestamped if it exists). Intermediate files go in `board-video/work/`.

## Gates: never skip

The user approves at three points. Stop and wait at each one.

1. **Brief and facts:** after intake (step 1).
2. **Script and storyboard:** before any animation code (step 3).
3. **Stills:** before the full render (step 5).

If the user explicitly says "no gates" or "just render", go straight through, but still list every placeholder number in the final message.

## 1. Intake

Read the project: README, docs, main routes and screens, key modules, any architecture notes, metrics dashboards and config describing what it does. Skip `node_modules/`, build output, lock files, `.git/`, `.env*`, keys, credentials and secrets.

Then write `brief.md` with:

- **One-sentence what:** what it is, in plain business language.
- **Audience:** who will watch, and what they should understand afterwards.
- **Problem:** the business pain, who feels it, and its cost if known.
- **What was built:** 3–5 capabilities in business terms (not tech stack).
- **How it works:** at most 4–5 boxes, for a non-technical viewer.
- **Demo journeys:** 2–3 real user journeys (entry → key action → result), each tied to a capability. This is the heart of the video.
- **Impact:** results, KPIs, pilots, adoption, accuracy, time saved. Include only if real; otherwise describe the effect in plain words without numbers.
- **Safeguards:** 2–3 points on privacy, security, human oversight or model controls, if relevant.
- **Facts table:** every number the video will show, with its source (file/line, doc, or "user"). Anything you could not source is marked `[TBC]`.

**Never invent numbers, customers, quotes, logos or results.** The code rarely contains business impact. Ask the user for the missing figures in one grouped question. If they don't have them, the video shows a clearly labelled placeholder (e.g. "KES [TBC]") or drops the claim. It never shows a guess.

**Sensitive data:** no secrets, internal hostnames, real customer names, phone numbers, emails or personal data on screen. If real UI shows such data, swap in fictional stand-ins and say so in the brief.

Gate 1: show the brief, the proposed demo journeys and the list of missing facts. Wait.

## 2. Structure

Default shape at 180s. For other durations, scale proportionally; under 120s, use one or two demo journeys and fold safeguards into "How it works".

| # | Scene | ~s | Job |
|---|---|---|---|
| 1 | Title / hook | 8 | Name + one line on what it does, e.g. "Spotting SIM-swap fraud before it happens" |
| 2 | Problem | 20 | The pain, who feels it, what it costs. One big number if real. |
| 3 | What we built | 18 | One sentence, then 3–5 capabilities appearing one by one |
| 4 | How it works | 30 | Box-and-arrow flow building step by step. No code, no vendor soup. |
| 5 | Demo: journey 1 | 30 | The real UI doing the main job, narrated step by step |
| 6 | Demo: journeys 2–3 | 30 | One or two further journeys showing other capabilities |
| 7 | What it changes | 22 | 2–4 impact points or KPIs, each with a label and comparison (before/after) |
| 8 | Safeguards | 12 | 2–3 controls, shown as a calm checklist |
| 9 | Recap and close | 10 | One-sentence recap ("Problem → what we built → what it changes"), then the logo |

There is no ask, CTA, budget or next-steps slide. The video ends on the recap and logo.

**Holding attention for 3 minutes:**
- Change the visual mode at least every 15–20s: a big number, a diagram, the product, a chart, a checklist. Never two text-only scenes in a row.
- Show a chapter marker in the corner (e.g. "04 · How it works") so viewers always know where they are.
- At least half the runtime shows the real product (scenes 4–6 plus product snippets elsewhere).
- Each demo journey opens with a one-line caption saying what the user is trying to do.

## 3. Script and storyboard

Write `script.md` first, then `storyboard.md`.

**Script (the voiceover) leads everything.**
- About 140–150 words per minute: ~420–450 words for 180s.
- Short declarative sentences. Active voice. Say "we".
- Plain words. Define any term a board member would not know, or cut it.
- In demo scenes, narrate what the user does and why it matters ("The agent flags the swap request and holds it for review"), not which buttons are clicked.
- Every claim in the script appears in the facts table.
- Leave a 1–2s pause of silence at each chapter change, while the visual lands.
- Write numbers the way they're spoken ("two point four billion shillings") in the narration file, and as digits on screen.

**Storyboard:** per scene, give the duration, the narration lines, the on-screen text (max ~12 words per frame), visuals, motion, and the transition. Add a running timecode column and confirm the total is the target ±5s.

Gate 2: show the script (with word count and estimated runtime) and the storyboard. Wait. Revise until approved.

## 4. Build in Remotion

If a Remotion skill (e.g. `remotion-best-practices` from `remotion-dev/skills`) is installed, load it and follow it for API details.

Setup:
- If there is no Remotion project, scaffold one in `board-video/work/remotion` (`npx create-video@latest --blank`). Ask before installing anything beyond Remotion and its dependencies.
- One `<Composition>` at the chosen size and fps. One component per scene in its own file, composed with `<Series>`.
- Shared components for: the chapter marker, KPI tile, flow-diagram box/arrow, subtitle bar, device/browser frame, and an animated cursor.
- All animation is driven by `useCurrentFrame()` with `interpolate`/`spring`. No CSS transitions or timers.
- Load fonts with `@remotion/google-fonts` or a local `staticFile()`, and wait for them before rendering.

Capturing the product for demo scenes, in order of preference:
1. Render the project's real components and styles inside Remotion, with fictional data.
2. Record the running app with a headless browser (e.g. Playwright video or frame screenshots), using fictional data, into `work/capture/`.
3. Real screenshots, animated with a simulated cursor, clicks, typing and gentle zooms.

Voiceover:
1. Generate narration per scene with the chosen TTS into `work/audio/scene-NN.mp3`. Use a calm, measured voice at slightly slower than default speed. Use the same voice and settings for every scene.
2. Measure each clip's length (ffprobe or `getAudioDurationInSeconds`). Set each scene's duration to its narration plus 0.5–1.5s of breathing room. **Audio sets the timing; visuals follow it.** In demo scenes, sync the on-screen actions to the narration lines describing them. If the total drifts more than 10s from the target, tighten or extend the script, not the pacing.
3. Optional music: a very quiet ambient bed, roughly 20–25 dB under the voice, ducked further during speech. No beats, no risers. If you have no licensed or generated bed, use none.
4. Burn in subtitles by default. Boards often watch on mute, on phones, in lobbies.

Visual rules:
- **Brand:** use the project's or org's exact colours, font and logo. Otherwise use a restrained neutral palette (near-white or deep navy background, one accent colour).
- **Space:** generous margins (≥ 8% of width). One idea per frame. Max 3 bullets.
- **Type:** headlines 64–96px, body ≥ 36px at 1080p. Nothing below 28px.
- **Holds:** any text the viewer must read stays fully visible for ≥ 0.4s per word after it settles. KPIs hold ≥ 3s after the count finishes.
- **Motion:** soft. Use ease-out fades, 12–20 frame slides, and staggered reveals (100–150ms apart). Use slow drift or parallax (≤ 3% scale over a scene) on long holds so frames never feel frozen. No spins, shakes, flashes or zoom punches.
- **Transitions:** dip through background or a stagger (old out, then new in). Never crossfade two busy layouts into each other.
- **Demo footage:** show it in a clean browser or device frame. Zoom smoothly (≤ 1.6×) to the area being narrated, with a soft spotlight or dimming of the rest. The cursor moves on eased curves, and clicks show a subtle ripple. Blur or replace any sensitive data.
- **Numbers:** count up with easing, and always pair them with a unit and a comparison ("↓ 38% vs Q1"). Placeholders show as `[TBC]` in a muted colour.
- **Diagrams:** build them as animated SVG/React boxes, not screenshots of architecture slides. Highlight the active box as the narration reaches it.
- **Charts:** if a `dataviz` skill is available, follow its colour and mark rules.

## 5. Check

Render stills with `npx remotion still` at the settled midpoint of every scene and at the middle of every transition, into `work/stills/`. Also render a 10-second low-resolution preview around each chapter change and inside each demo scene, to check audio and action sync.

Check each one for:
- overflow, clipping, or overlapping elements
- contrast (WCAG AA: 4.5:1 for body, 3:1 for large text)
- text readable at phone size (view at 25%)
- muddy double exposure in transitions
- jargon or unsourced numbers that slipped in
- any real personal or sensitive data visible in demo footage
- subtitles in sync with the voice, and demo actions in sync with the narration
- no stretch longer than 20s without a change of visual mode

Fix what you find, then contact-sheet the stills (one image, grid, labelled with timecodes) and show it to the user. Gate 3: wait for approval.

## 6. Render and deliver

Render `board-video/board-video.mp4` (H.264, CRF 18, `-preset veryfast` unless asked for best quality, AAC 192k). Also produce:

- `board-video.jpg`: poster from the strongest settled frame (usually the title or a clean product shot). Bake it in as frame 0 by replacing that frame, not adding one, so audio sync holds.
- `script.md`: the final narration, which doubles as speaker notes for a live demo.
- `subtitles.srt`.
- `chapters.txt`: timecodes and titles for each chapter, for YouTube, SharePoint or a meeting agenda.
- `facts.md`: every number shown and its source, with `[TBC]` items listed first.
- Optional `board-video-silent.mp4` (no audio, burned subtitles) for screens in boardrooms.

Final message to the user: where the files are; the final runtime; one sentence on how the demo tells the story; the list of `[TBC]` placeholders still on screen; an offer to cut a 60–90s version, re-voice, or make a version for a different audience (e.g. technical committee, regulator, partners).
