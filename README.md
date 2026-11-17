# board-video

A skill that produces a ~3-minute board-level demo video explaining what a project or codebase does. Built with [Remotion](https://www.remotion.dev/), with a narrated walkthrough of the real product.

## What it does

Given a project path or URL, it reads the codebase, writes a brief, drafts a script and storyboard, builds the animation in Remotion, records TTS narration, and renders a final MP4 — all optimised for a smart, non-technical executive audience.

## Usage

```
/board-video [path or URL] [options]
```

| Option | Default |
|---|---|
| `--duration <s>` | 180 (allowed 60–300) |
| `--format landscape\|vertical\|square` | landscape 1920×1080, 30fps |
| `--voice <tts>` | ElevenLabs, OpenAI, or local TTS; prompts if none found |
| `--brand <colours, font, logo path>` | read from the project; prompts if not found |

Output lands in `board-video/` in the current directory (timestamped if the folder already exists). Intermediate files go in `board-video/work/`.

## Gates

The skill stops for approval at three points:

1. **Brief and facts** — after reading the project
2. **Script and storyboard** — before writing any animation code
3. **Stills** — before the full render

Pass `--no-gates` to skip all three (placeholders are still listed in the final message).

## Output files

| File | Description |
|---|---|
| `board-video.mp4` | Main render (H.264, AAC) |
| `board-video.jpg` | Poster frame |
| `script.md` | Final narration / speaker notes |
| `subtitles.srt` | Subtitle file |
| `chapters.txt` | Chapter timecodes for YouTube / SharePoint |
| `facts.md` | Every on-screen number with its source |
| `board-video-silent.mp4` | No audio, burned subtitles (for boardroom screens) |

## Video structure (180s default)

| # | Scene | ~s |
|---|---|---|
| 1 | Title / hook | 8 |
| 2 | Problem | 20 |
| 3 | What was built | 18 |
| 4 | How it works | 30 |
| 5 | Demo: journey 1 | 30 |
| 6 | Demo: journeys 2–3 | 30 |
| 7 | What it changes | 22 |
| 8 | Safeguards | 12 |
| 9 | Recap and close | 10 |

No ask, no CTA, no roadmap pitch. The product is the star.

## Setup

### 1. Install dependencies

```bash
node -v        # must be 18+
npm -v         # must be 8+
```

Install Remotion in an empty directory:

```bash
npx create-video@latest --blank board-video-work
cd board-video-work
npm install
```

### 2. Configure a TTS provider

Set one of the following environment variables before running:

```bash
# ElevenLabs (recommended)
export ELEVENLABS_API_KEY=your_key_here

# OpenAI
export OPENAI_API_KEY=your_key_here

# Local (Coqui, Piper, etc.) — set a command the skill can shell out to
export TTS_COMMAND="piper --model en_US-lessac-medium --output_file"
```

If none is set the skill will prompt you to choose.

### 3. Install ffprobe

macOS:
```bash
brew install ffmpeg
```

Ubuntu / Debian:
```bash
sudo apt install ffmpeg
```

Windows: download from [ffmpeg.org](https://ffmpeg.org/download.html) and add `bin/` to PATH.

### 4. Install this skill

Place `SKILL.md` in your skills directory (the exact path depends on your agent harness). Then invoke it with `/board-video`.

---

## Process

```
/board-video [path or URL]
        │
        ▼
  1. Intake ──────── reads codebase, docs, routes, metrics
        │
        ▼
  Gate 1 ─────────── brief + facts, missing numbers → you approve
        │
        ▼
  2. Structure ───── scenes and timings locked from approved brief
        │
        ▼
  3. Script + storyboard
        │
        ▼
  Gate 2 ─────────── script (word count + runtime) + storyboard → you approve
        │
        ▼
  4. Build in Remotion
        │   ├─ scaffold project (if none exists)
        │   ├─ one component per scene
        │   ├─ capture real UI (render / headless browser / screenshots)
        │   └─ generate TTS narration, sync durations
        │
        ▼
  5. Check ──────────  stills at every scene midpoint + transition
        │              contrast, overflow, sensitive data, subtitle sync
        ▼
  Gate 3 ─────────── contact-sheet stills → you approve
        │
        ▼
  6. Render ──────── board-video.mp4 + all output files delivered
```

Each gate is a hard stop — the skill waits for your reply before continuing. Pass `--no-gates` to run straight through (placeholders are still flagged in the final message).

---

## Requirements

- Node.js 18+
- Remotion (`npx create-video@latest`)
- A TTS provider (ElevenLabs, OpenAI, or local)
- `ffprobe` on PATH for audio duration measurement

## License

MIT
