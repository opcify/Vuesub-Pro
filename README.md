# Vuesub

**Audio to subtitles, on your Mac, offline.**

Vuesub turns audio and video files into accurate, timestamped subtitles using
[OpenAI Whisper](https://github.com/openai/whisper) running entirely on your
machine. No accounts, no uploads, no usage limits.

> **Download for macOS (Apple Silicon):**
> [Latest release](https://github.com/opcify/Vuesub-Pro/releases/latest)
>
> **Landing page:** https://opcify.github.io/Vuesub-Pro/

This repo hosts the GitHub Pages landing site and the macOS release binaries.
The application source is in a separate private repository.

---

## What it does

Drop an audio or video file in, pick a Whisper model, click **Transcribe**,
and Vuesub produces a fully-edited subtitle track. From there you can:

- **Edit each line in place** — click any segment in the transcript and start
  typing. Auto-saves as you go.
- **Drag the timing** — every caption clip on the waveform is draggable;
  drag the body to move the whole clip, drag either edge to resize. Adjacent
  clips can't overlap.
- **Insert / delete / merge** — empty gaps over 100 ms get a `+` button to
  drop a new caption in. Hover any row to reveal a `×` delete. Shift-click
  adjacent rows then `⌘J` to merge into one.
- **Follow the audio** — the playhead scrolls the transcript automatically
  while you listen, so you always see what's currently playing.
- **Export to your tool of choice** — SRT, VTT, plain text, or **FCPXML**
  for Final Cut Pro at any resolution / frame rate.

The interface borrows the design language of pro-app editors (Final Cut Pro,
Logic Pro): dark chrome, dense panels, monospaced timecodes, a waveform
timeline with zoom (`⌘+` / `⌘−` / `⌘0`) and horizontal scroll for long
files. `Space` plays / pauses, `⌘Z` undoes the last destructive edit.

## Why offline matters

Cloud transcription services upload your audio. Vuesub never does. Your
recordings stay on your disk — useful for confidential interviews,
unreleased music, NDA'd content, or anything you'd rather not hand to a
third-party server. The Whisper model runs locally via
[`faster-whisper`](https://github.com/SYSTRAN/faster-whisper), which is
about 4× faster than the original Whisper at the same accuracy.

## Models

Whisper models download automatically on first use and cache to
`~/.cache/huggingface/`. Pick the smallest that gives you the accuracy you
need:

| Model      | Disk   | RAM   | Notes                                    |
| ---------- | ------ | ----- | ---------------------------------------- |
| `tiny`     |  75 MB | ~1 GB | Very fast, lower accuracy                |
| `base`     | 145 MB | ~1 GB | Sensible default                         |
| `small`    | 488 MB | ~2 GB | Better accuracy                          |
| `medium`   | 1.5 GB | ~5 GB | Strong accuracy on most audio            |
| `large-v3` | 3.0 GB | ~10 GB| Best quality; needs a beefy machine      |

## Supported file types

| Extension | Format                    | Common source                              |
| --------- | ------------------------- | ------------------------------------------ |
| `.mp3`    | MPEG-1 Audio Layer III    | Podcasts, downloads                        |
| `.wav`    | Waveform Audio File       | Recording sessions, lossless masters       |
| `.m4a`    | MPEG-4 Audio (AAC/ALAC)   | Apple Voice Memos, Music exports           |
| `.flac`   | Free Lossless Audio Codec | Lossless archives                          |
| `.ogg`    | Ogg Vorbis / Opus         | Open-source apps, web                      |
| `.mp4`    | MPEG-4                    | Most cameras, Zoom recordings              |
| `.mov`    | QuickTime Movie           | macOS screen recordings, iPhone video      |
| `.mkv`    | Matroska                  | Anime, archival, screen captures           |
| `.webm`   | WebM                      | Browser-recorded video, OBS                |

For video files, Vuesub extracts the audio track and ignores the video.

## System requirements

- **macOS 11 Big Sur** or later
- **Apple Silicon** Mac. The Apple Silicon DMG also runs on Intel Macs via
  Rosetta 2, just slower at transcription.
- Roughly **1 GB free disk** for the app, plus the size of whichever Whisper
  model you choose.

## First launch

Because Vuesub isn't yet signed with an Apple Developer ID, macOS Gatekeeper
will warn the first time you open it:

1. Drag `Vuesub.app` from the DMG into your `Applications` folder.
2. Right-click `Vuesub.app` → **Open**.
3. Click **Open** in the confirmation dialog.

After the first launch, you can open it normally.

## License

Vuesub is proprietary software. See `LICENSE.txt` (where applicable) for
end-user terms. Bundled open-source components keep their original licenses;
attribution is included with the application.
