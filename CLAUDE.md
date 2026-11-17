# CLAUDE.md: video-use

Instructions for coding agents working on this repository. `AGENTS.md` is a
symlink to this file.

## What this is

A Claude Code skill for conversation-driven video editing, plus the Python
helpers it calls. This repository is a fork of `browser-use/video-use`.

Two documents, two purposes:

- `SKILL.md` is the product: the instructions an agent follows while editing
  a user's footage (principles, hard rules, process, EDL format).
- This file is for changing the repository itself.

## Setup and commands

```bash
uv venv .venv --python 3.13
uv pip install --python .venv/bin/python -e .   # requests, librosa, matplotlib, pillow, numpy
.venv/bin/python helpers/render.py --help       # every helper has --help
.venv/bin/python -m py_compile helpers/*.py     # syntax check
```

`ffmpeg` and `ffprobe` must be on `PATH`. `yt-dlp`, `manim` and Remotion are
optional and installed on first use (see `SKILL.md`, "Setup").

## Layout

```
SKILL.md                  the skill (agent instructions for editing video)
helpers/transcribe.py     one ElevenLabs Scribe call per file, cached
helpers/transcribe_batch.py   parallel transcription for multi-take folders
helpers/pack_transcripts.py   transcripts/*.json to takes_packed.md
helpers/timeline_view.py  filmstrip + waveform PNG for a time range
helpers/render.py         EDL to video: extract, concat, overlays, subtitles last
helpers/grade.py          ffmpeg colour grade (presets or raw filter)
skills/manim-video/       vendored skill for Manim animation slots
static/, poster.html      README assets
```

## Rules

- **`SKILL.md` hard rules are production rules.** Do not change helper
  behaviour that a hard rule depends on (subtitles last, audio fades at cuts,
  outputs under `<videos_dir>/edit/`) without updating `SKILL.md` in the same
  change.
- **Keep local changes additive.** This is a fork; edits to upstream files
  make the next sync harder. Prefer new files over rewrites.
- **Secrets:** `ELEVENLABS_API_KEY` comes from `.env` at the project root or
  from the environment. `.env` is gitignored; never commit a key.

## Known limits

- No tests and no CI. Verify by hand: `py_compile`, `--help`, and a real cut
  on a short clip when you touch `render.py` or `grade.py`.
- Dependencies are unpinned and there is no lockfile, so two installs can
  differ.
- Issues are disabled on this fork. Open work is tracked in
  kmt-anatolijw/nova-studio: #80 (pin dependencies), #81 (tests and CI),
  #82 (upstream sync policy).
- Claude Code cloud sandbox: there is no ElevenLabs key, so transcription
  cannot run there. Only installation, `py_compile` and `--help` were
  verified in the sandbox; no render was run.

## Git

Upstream uses short lowercase subjects without a type prefix ("add
what-it-does section to readme"). Follow that style for changes meant to go
upstream. Claude Code cloud sessions work on the branch the session assigns.
