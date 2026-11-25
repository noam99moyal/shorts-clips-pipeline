# Shorts Clips Pipeline

Shorts Clips Pipeline is a Grok Bot that turns one long video into captioned, scheduled YouTube Shorts. It stops for your OK at every step, and nothing posts until you say "schedule". It is not tied to any brand or channel; you choose the Drive folder, voice, captions, and YouTube account.

## Install

1. Open the Grok Bot template: [Shorts Clips Pipeline on x.ai](https://x.ai/bot/VVNtVcHR3nZOLsKlPkH8g)
2. Open the new agent chat and finish first-run setup. The bot asks these once and saves your answers.

First-run checklist:

- A Google Drive folder for cut lists, copy docs, clips, and the schedule log (confirmed with a path and link, used only after your yes)
- A voice or style file for titles and descriptions, or the default voice
- Caption rules (defaults shown; change anything you want)
- YouTube Studio sign-in in the bot's own browser, through a screen handoff. Never send a password in chat.

Connectors: Google Drive, Google Docs, Google Sheets, and Composio.

## How it works

Each video goes through three steps. The bot waits for your OK at every step.

### Step 1: Moments

- You share a transcript (or the bot makes one with ffmpeg audio extract and faster-whisper) and the source video, plus what you want: topic, speaker, how many clips, audience.
- It picks self-contained 40-60s clips (70s hard max) that open on a full, no-context sentence, make one point, and end on a strong line. No invented quotes or timestamps.
- You get a Google Doc cut list (in/out times, length, opening words, notes) plus the top 3 picks in chat, then you choose which to make.

### Step 2: Copy

- For the clips you picked, it writes a Google Doc with a GEO title (max 100 characters; the default upload title), one alternate title, and a short description.
- Every claim must come from the clip or your voice file. Anything unsure is flagged, not guessed.

### Step 2: Clips

- It cuts the first 2 clips for a style check, then the rest only after your OK.
- Cuts start on a full clause, end 0.12-0.20s after the last word, and run at 1.1x with pitch kept (or your saved speed).
- Each clip gets a 16:9 version and a 9:16 `YT-` Short (face-centered crop, or a padded frame if a crop would cut someone out), plus burned-in captions and an `.srt` file, saved to Drive.

### Step 3: Scheduling

- Starts only when you say "schedule". It suggests a cadence, avoids double-booking by checking existing scheduled uploads and the `<Channel> Shorts Schedule Log` sheet, and shows a plan for your yes.
- In YouTube Studio, each `YT-` file is uploaded with the copy-doc title and description, category, "not made for kids", playlist, and Visibility > Schedule. Never Public or Publish now.
- After verify and log, headings are renamed `#N`, and you get a report: clip, title, link, and publish time in your timezone.

### Defaults and safety

Default voice is plain and specific: no hype, no emoji spam, no "In this video". Default captions are burned in word by word in plain white Montserrat Black with a thick black outline, max 2 short lines, verbatim (stutters shown once), lower third on 16:9 and below the face on 9:16.

The bot never publishes a video as public, and nothing is uploaded until you say "schedule". Failures are reported plainly, such as a missing source, a bad render, an auth or quota problem, or a schedule that didn't stick. Quiet is only for a clean run.
