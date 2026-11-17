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

## Requirements

- Node.js 18+
- Remotion (`npx create-video@latest`)
- A TTS provider (ElevenLabs, OpenAI, or local)
- `ffprobe` on PATH for audio duration measurement

## License

MIT
