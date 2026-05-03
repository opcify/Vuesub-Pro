# Vuesub

**Audio &amp; video to subtitles, plus voice cloning. Offline by default, cloud-optional.**

Vuesub turns audio and video files into accurate, timestamped subtitles using
[OpenAI Whisper](https://github.com/openai/whisper) running entirely on your
machine — or, optionally, via the OpenAI cloud API for the convenience of
not downloading the model. New in 0.2.0: a **Voice Clone** tab that runs
zero-shot voice synthesis with [CosyVoice 3](https://github.com/FunAudioLLM/CosyVoice)
locally — upload a clean clip, type any text, hear it in the same voice.

> **Downloads**
> - macOS · Apple Silicon: [Vuesub-mac-aarch64.dmg](https://github.com/opcify/Vuesub-Pro/releases/latest/download/Vuesub-mac-aarch64.dmg)
> - Windows · x64: [Vuesub-windows-x64-setup.exe](https://github.com/opcify/Vuesub-Pro/releases/latest/download/Vuesub-windows-x64-setup.exe)
> - Linux · x64: [Vuesub-linux-x64.deb](https://github.com/opcify/Vuesub-Pro/releases/latest/download/Vuesub-linux-x64.deb)
>
> **Landing page:** https://opcify.github.io/Vuesub-Pro/
> **All releases:** https://github.com/opcify/Vuesub-Pro/releases

This repo hosts the GitHub Pages landing site and the cross-platform release
binaries. The application source lives in a separate private repository.

---

## What it does

Drop an audio or video file in, pick a Whisper model, click **Transcribe**,
and Vuesub produces a fully-edited subtitle track. From there:

- **Edit each line in place** — click any segment in the transcript and start
  typing. Auto-saves as you go. The textarea grows with the text via a pure
  CSS auto-grow, so editing long transcripts stays smooth.
- **Drag the timing** — every caption clip on the waveform is draggable; drag
  the body to move the whole clip, drag either edge to resize. Adjacent clips
  can't overlap.
- **Insert / delete / merge** — empty gaps over 100 ms get a `+` button to
  drop a new caption in. Hover any row to reveal a `×` delete. Shift-click
  adjacent rows then `⌘J` (`Ctrl+J` on Win/Linux) to merge into one.
- **Follow the audio** — the playhead pins the currently-playing segment to
  the third visible row of the transcript so you always have context above
  and lookahead below.
- **Export to your tool of choice** — SRT, VTT, plain text, or **FCPXML** for
  Final Cut Pro at any resolution / frame rate. (See [Export formats](#export-formats)
  below — the short version: pick **SRT** for almost anything, **FCPXML** for
  Final Cut Pro.)

The interface borrows the design language of pro-app editors (Final Cut Pro,
Logic Pro): dark chrome, dense panels, monospaced timecodes, a waveform
timeline with zoom (`⌘+` / `⌘−` / `⌘0`), horizontal scroll for long files.
`Space` plays / pauses, `⌘Z` undoes the last destructive edit.

## Voice Clone (new)

The **Voice Clone** tab on the left rail runs zero-shot voice synthesis using
[CosyVoice 3 0.5B](https://github.com/FunAudioLLM/CosyVoice). Upload a 5–30 s
single-speaker WAV/MP3, type any text in the supported languages (Chinese,
English, Japanese, Korean, German, Spanish, French, Italian, Russian, +
18 Chinese dialects), get back the same voice saying it. Cold start on Mac
CPU is ~60 s; warm subsequent generates are ~3.5× realtime. Output WAVs play
in-app and can be saved anywhere.

Vuesub auto-transcribes the reference clip with Whisper at registration time
(picks the largest installed model automatically) so you don't type out what
your reference says — but the transcript is editable inline because accuracy
matters: CosyVoice's zero-shot output is sensitive to misalignment between
the reference text and reference audio.

The CosyVoice runtime is too heavy to bundle (~6.5 GB), so it's installed on
demand the first time you hit Download. **You'll need three tools on PATH** —
the page detects what's missing and shows install snippets:

- **Python 3.10–3.12** — `brew install python@3.12` (mac) · [python.org](https://www.python.org/downloads/) (Win) · `apt install python3.12 python3.12-venv` (Linux)
- **uv** — `curl -LsSf https://astral.sh/uv/install.sh | sh` (mac/Linux) · `irm https://astral.sh/uv/install.ps1 | iex` (Win)
- **git** — `xcode-select --install` (mac) · [git-scm.com](https://git-scm.com/download/win) (Win) · `apt install git` (Linux)

Storage layout under `~/.vuesub/`: speakers (reference clips + auto
transcripts) live in `speakers/`, generated clips in `clones/`, the runtime +
model in `runtimes/cosyvoice/`. The history list on the Voice Clone page
shows generated clips, capped at 5 visible rows with scroll for the rest.

> **Heads up — Voice Clone is fully tested only on Apple Silicon Mac.** Windows
> and Linux binaries are produced from CI but the runtime path hasn't been
> live-tested on those platforms. Please [open an issue](https://github.com/opcify/Vuesub-Pro/issues)
> if anything breaks.

## Local first, cloud optional

**Local (default).** Whisper runs on your machine via
[`faster-whisper`](https://github.com/SYSTRAN/faster-whisper) (about 4× faster
than the original Whisper at the same accuracy). Audio never leaves the disk.
Useful for confidential interviews, unreleased music, NDA'd content — or
anything you'd rather not hand to a third-party server.

**Cloud (opt-in).** Open Settings → OpenAI API tab, paste your API key, then
pick **OpenAI whisper-1 (cloud)** as the model. Vuesub auto-transcodes your
file to 16 kHz mono Opus at 16 kbps before upload, so even a multi-hour
recording fits comfortably under OpenAI's 25 MB limit. The API key is stored
locally in your app data, never sent anywhere except to OpenAI.

## Plays nearly anything

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
| `.webm`   | WebM (incl. AV1 + Opus)   | Browser-recorded video, OBS                |

For video files, Vuesub extracts the audio track and ignores the video. Some
containers (notably WebM with Opus on macOS) aren't accepted by the system's
native audio player; Vuesub transparently falls back to a Web Audio playback
path so the file plays anyway.

## Export formats

When you click **Export**, Vuesub asks where to save and writes the transcript
in one of four formats. The two that cover almost every real workflow are
**SRT** and **FCPXML** — the others are situational.

### SRT — the universal subtitle file *(pick this if you're not sure)*

SubRip (`.srt`) is the lowest common denominator for timed subtitles. It's
plain text: a number, a `HH:MM:SS,mmm --> HH:MM:SS,mmm` range, and the line.
Almost everything that plays or edits video accepts it:

- **Video players** — VLC, IINA, mpv, QuickTime (with a plugin), every smart
  TV / set-top box that supports external subs. Drop `MyVideo.srt` next to
  `MyVideo.mp4` with the same base name and most players pick it up
  automatically.
- **YouTube / Vimeo** — upload as a caption track on a video; both platforms
  ingest SRT directly and run their auto-translation on top.
- **Editors** — Premiere Pro, DaVinci Resolve, CapCut, Final Cut Pro,
  Camtasia, ScreenFlow: import as a caption / subtitle track and it lands
  with timing intact. (For FCP specifically, **FCPXML** below preserves
  more — see when to prefer it.)
- **Generated content** — feed the SRT into burn-in tools (`ffmpeg
  -vf subtitles=…`), translation tools, or a script that turns it into a
  blog post.

If you don't know what your downstream tool expects, export SRT first.

### FCPXML — native captions for Final Cut Pro

FCPXML is Apple's project interchange format. Vuesub writes a tiny FCPXML 1.11
project where every segment is a real, native FCP `<caption>` clip on lane 1
of an empty timeline. That means after you import it into FCP:

- Each segment is a separate caption you can restyle, retime, or
  re-translate using the built-in caption editor — not baked-in graphics
  text.
- Captions are **frame-aligned to the rate you pick**, so FCP doesn't pop
  up *"caption is not on an edit frame boundary"* warnings or silently
  nudge them.
- The timeline arrives at the resolution and frame rate you chose, which
  also sets the timecode format (NDF vs. drop-frame for 29.97 / 59.94).

When the dialog appears, **match your video project's resolution and frame
rate exactly.** Common picks:

| Project                                             | Choose                |
| --------------------------------------------------- | --------------------- |
| iPhone / most modern cameras                        | 1080p · 30 (or 60)    |
| Cinema / film look (24p deliverables)               | 1080p · 23.976        |
| Broadcast US (NTSC)                                 | 1080p · 29.97 or 59.94 |
| Broadcast Europe (PAL)                              | 1080p · 25 or 50      |
| 4K phone footage / pro cameras                      | 2160p · 30 or 24      |

Workflow: in FCP, open your project → **File → Import → XML…** → pick
Vuesub's `.fcpxml`. The captions appear as their own caption clip on the
timeline; copy them onto your edit and they snap to the same timeline.
Vuesub remembers your last resolution / FPS choice across launches.

### VTT — for HTML5 `<video>` and the web

WebVTT (`.vtt`) is the format browsers expect when you attach captions
to an HTML5 `<video>` with a `<track>` element. Same idea as SRT, slightly
different syntax (uses `.` instead of `,` in timestamps and starts with
a `WEBVTT` header). Pick this if you're publishing the video to a website
you control and want captions to show in the browser without a third-party
player.

### TXT — plain transcript, no timing

`.txt` is just the lines, one per segment, with all timing stripped. Use it
when you need the *content*, not the timing — turning a podcast into a
blog post, dropping a transcript into Notion / Google Docs, feeding text
to a search index or an LLM, sending notes to someone who doesn't need
the video.

---

## Languages

Auto-detect by default, or pick a fixed language for higher accuracy:

> English · 中文 Mandarin · 中文 Cantonese · 日本語 · Español · Italiano · Deutsch · ไทย · Русский · 한국어 · Nederlands · Français · हिन्दी · العربية · עברית

Your last-used language is remembered across launches. For Chinese
transcripts, a **简/繁** button in the toolbar converts the entire transcript
between Simplified and Traditional Chinese in place, via OpenCC.

## Models

Whisper models download on demand from the Settings → Models panel — you get
byte-level progress and an ETA while it runs, can pause at any time, and can
delete cached models from the same place. You can also import a custom
faster-whisper / CT2 model directory packaged as a `.zip`.

| Model       | Disk     | RAM    | Notes                                    |
| ----------- | -------- | ------ | ---------------------------------------- |
| `tiny`      |  ~75 MB  | ~1 GB  | Very fast, lower accuracy                |
| `base`      | ~145 MB  | ~1 GB  | Sensible default                         |
| `small`     | ~485 MB  | ~2 GB  | Better accuracy                          |
| `medium`    | ~1.5 GB  | ~5 GB  | Strong accuracy on most audio            |
| `large-v3`  | ~3.1 GB  | ~10 GB | Best quality; needs a beefy machine      |
| `whisper-1` | —        | —      | OpenAI cloud (requires API key)          |

> **Model download failing?** Only if the in-app downloader can't reach
> Hugging Face (network, firewall, region block), grab a pre-packaged model
> from our mirror and import it via *Settings → Models → Import*:
> [Whisper models · Google Drive mirror](https://drive.google.com/drive/folders/1fBrrqx_LGI4zGyShDLUyFfIsozjT8esc?usp=sharing).

## System requirements

- **macOS 11 Big Sur** or later · Apple Silicon (Intel via Rosetta works,
  just slower at transcription).
- **Windows 10 / 11** · x64. Includes WebView2 (preinstalled on Win 11).
- **Linux** · x64. Debian / Ubuntu via the `.deb`. Needs `webkit2gtk-4.1`
  at runtime (Ubuntu 22.04+: `apt install libwebkit2gtk-4.1-0`).
- Roughly **1 GB free disk** for the app, plus the size of whichever Whisper
  model you download.

## First launch

### macOS

The app isn't yet signed with an Apple Developer ID. Drag `Vuesub.app`
from the DMG into your `Applications` folder, then **strip the
quarantine attribute** that macOS adds to downloaded apps:

```bash
sudo xattr -rd com.apple.quarantine /Applications/Vuesub.app
```

After that, double-click to launch it normally.

> **Why is this necessary?** On macOS 14+ Gatekeeper attaches a
> `com.apple.quarantine` attribute to anything you download from the
> web, and for unsigned apps it shows *"Vuesub is damaged and can't be
> opened"* with no override. The right-click → Open trick no longer
> works for these. The `xattr -rd` command removes the attribute and
> tells Gatekeeper to stop second-guessing — same effect that signing
> with an Apple Developer ID would have, just done by hand.

### Windows

Run the NSIS installer (`Vuesub-windows-x64-setup.exe`). SmartScreen may show
a "publisher unknown" warning the first time — click **More info** →
**Run anyway**. Subsequent launches are silent.

### Linux

```bash
sudo apt install ./Vuesub-linux-x64.deb
```

Then launch *Vuesub* from your application menu, or run `vuesub` from the
terminal.

## License

Vuesub is proprietary software. See `LICENSE.txt` (where applicable) for
end-user terms. Bundled open-source components keep their original licenses;
attribution is included with the application.

---

> ⚠️ **Heads up — Windows & Linux builds are untested.** We ship Windows x64
> and Linux x64 binaries from CI, but they haven't gone through real-world
> testing yet. macOS (Apple Silicon) is the only daily-driven build right
> now. If you hit anything broken on Windows or Linux, please
> [open an issue](https://github.com/opcify/Vuesub-Pro/issues) so we can
> fix it.
