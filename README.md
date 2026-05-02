# Vuesub

**Audio &amp; video to subtitles. Offline by default, cloud-optional.**

Vuesub turns audio and video files into accurate, timestamped subtitles using
[OpenAI Whisper](https://github.com/openai/whisper) running entirely on your
machine — or, optionally, via the OpenAI cloud API for the convenience of
not downloading the model.

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
  Final Cut Pro at any resolution / frame rate.

The interface borrows the design language of pro-app editors (Final Cut Pro,
Logic Pro): dark chrome, dense panels, monospaced timecodes, a waveform
timeline with zoom (`⌘+` / `⌘−` / `⌘0`), horizontal scroll for long files.
`Space` plays / pauses, `⌘Z` undoes the last destructive edit.

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

The app isn't yet signed with an Apple Developer ID, so Gatekeeper will warn
the first time you open it:

1. Drag `Vuesub.app` from the DMG into your `Applications` folder.
2. Right-click `Vuesub.app` → **Open**.
3. Click **Open** in the confirmation dialog.

After the first launch you can open it normally.

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
