# Shorts Clips Pipeline

Shorts Clips Pipeline is a Grok Bot that turns one long video into captioned, scheduled YouTube Shorts. It stops for your OK at every step, and nothing posts until you say "schedule". It is not tied to any brand or channel. You choose the Drive folder, voice, captions, and YouTube account.

## Install

1. Open the Grok Bot template: [Shorts Clips Pipeline on x.ai](https://x.ai/bot/VVNtVcHR3nZOLsKlPkH8g)
2. Open the new agent chat and finish first-run setup. The bot asks these once and saves your answers.

### First-run checklist

- **Google Drive folder.** Choose where the bot should save cut-list docs, copy docs, clip folders, and the schedule log. It confirms the full path and a link, then uses that folder only after you say yes.
- **Voice.** Send a voice or style file for titles and descriptions, or use the bot's default voice.
- **Captions.** Review the default caption rules and change anything you want.
- **YouTube Studio sign-in.** Sign in on the bot's own browser through a screen handoff. Never send a password in chat. That login stays saved.
- **Optional read-only YouTube link.** Connect Composio's YouTube toolkit so the bot can read and verify videos and find already-scheduled slots. If you skip this, it verifies in Studio instead.
- **Channel confirmation.** The bot reads back the channel name and handle and waits for your yes.
- **Playlist.** Choose which playlist the Shorts go into, or none.
- **Category.** Common fits are 28 Science & Technology or 22 People & Blogs.
- **Timezone.** Times in plans and reports are shown in this timezone.

The bot uses these connectors: Google Drive, Google Docs, Google Sheets, and Composio.

## How it works

Each video goes through three steps. The bot waits for your OK at every step.

### Step 1: Moments

You give a transcript and say what you want: topic, speaker, how many clips, and audience. You also point to the full source video: a file on your computer, a Drive link, a YouTube link, or a file in chat.

If there is no transcript, the bot can make one. It extracts audio only with ffmpeg (`ffmpeg -vn -ac 1 -ar 16000`), then transcribes it with faster-whisper and word timestamps. It does not move the full video when audio is enough. On your computer, it deletes only the temp audio it made.

It picks self-contained clips that:

- Open on a full sentence that makes sense with no context (no "so", "and", or "like I said" openers)
- Make one point and end on a strong, complete line
- Target 40-60 seconds. 60-70 seconds is OK only if it earns it, and those are marked. The hard max is 70 seconds after speed-up
- Prefer hooks: a surprising number, a contrarian claim, a crisp analogy, or a clear why-it-matters

It never invents a quote or a timestamp. Estimated edges are marked with `~` and snapped to the word in the audio before cutting.

It saves a session folder at `<root>/<video name>/` and a Google Doc named `<video name>: Cut List` inside it. The doc has one heading per clip and a table with these columns:

`# | In → Out (mm:ss) | Length | Opens on (exact first words, in quotes) | Why it works / notes`

Notes can include concrete fixes, such as "extend to 04:16 to land the line (~67s)".

The same table is posted in chat with the bot's top 3 picks and the doc link. Then it waits for you to choose which clips to make ("make 1, 3, 5" or "all").

### Step 2: Copy

Copy and clips run in parallel, only for the rows you picked.

A Google Doc named `<video name>: Copy` is saved in the session folder. Each clip gets a heading with:

- A GEO title, phrased as the question or phrase people actually search or ask an AI. Max 100 characters. This is the default upload title.
- One catchy alternate title.
- A description in short lines, with a blank line after every sentence, and no hashtag spam. Tags are added only if your voice file asks for them.

Every claim must come from the clip itself or your voice file. Anything that might be off-brand or inaccurate is flagged instead of guessed.

### Step 2: Clips

The bot starts with a style check. It cuts only the first 2 picked clips (both versions), sends them, and asks: "Like the style? Anything to change before I cut the rest?" It applies your changes, then cuts the remaining clips the same way. It never cuts the rest before that OK.

Clips are cut from the source on the bot's computer with ffmpeg:

- Each span ends 0.12-0.20 seconds after the last word's end, so the syllable finishes.
- Each span starts on a full clause. Never mid-word or mid-phrase.
- Default speed is 1.1x with pitch kept (or your saved speed). The clip stays at 70 seconds or less after the speed-up.
- Two versions go in a subfolder `<session>/<Clip Name>/`. `<Clip Name>.mp4` is the normal 16:9 cut. `YT-<Clip Name>.mp4` is the 9:16 Short: a true crop centered on the face, never stretched or distorted. If a crop would cut someone out, it uses a padded full frame and says so.
- Captions are burned in, and an `.srt` file ships with every clip.
- Each file is checked to confirm it plays, the length is right, and the captions are in sync, then both versions are uploaded to the Drive subfolder.

When copy and clips are done, you get one message with the clip links, the copy doc link, and anything flagged. Then the bot waits. You can ask for changes; it redos only what you name.

### Step 3: Scheduling

Scheduling starts only when you say "schedule". Having clips and copy ready is not approval to upload.

The bot asks when you want them posted. It suggests a plan: your saved cadence if you have one, otherwise something like 3 a week at a consistent evening time in your timezone. It continues from your last scheduled video so nothing is double-booked, by listing the channel's uploads (including private ones), collecting future publish times, and cross-checking the log sheet.

It shows a plan table (clip, title, date and time in your timezone, plus any skipped clips and the reason) and waits for your yes.

Then, for each clip, one at a time, in YouTube Studio:

- Download the `YT-` file from Drive.
- Upload it in Studio. Set the GEO title and description exactly as in the copy doc, the category, "No, it's not made for kids", the playlist if you have one, then Visibility > Schedule with the date, time, and timezone from the plan. The video stays private until that time. The bot never picks Public or Publish now.
- If Studio saves a draft or an upload stops halfway, it finds that draft and finishes it. It never uploads the same clip twice. It checks the channel's titles before each upload.
- It verifies the title, description, schedule, category, and playlist (through API reads if Composio is connected, otherwise on the video's Studio page), then logs the row in the sheet.
- It then renames that clip's heading in both docs to `#N <Clip Name>`, where N counts up across the channel's scheduled Shorts in the log.

The log sheet is named `<Channel> Shorts Schedule Log` and lives in your chosen Drive folder. It is created on first use.

If Studio blocks the bot (a sign-in, a verification check, or a daily upload limit), it stops, lists what's left, and tells you exactly what it needs.

When it finishes, you get a report table: clip, title, video link, and publish time in your timezone.

### Default voice

If you do not give a voice file, titles and descriptions are plain and specific, like a sharp friend explaining the idea. No hype words ("game-changer", "unlock", "revolutionize", "dive in"), no emoji spam, no em-dash chains, and no "In this video". The title answers a real question, and the description says what the viewer will learn.

### Default captions

You can change these on first run:

- Burned in, word by word, in plain white. No colored or highlighted active word.
- Montserrat Black with a thick black outline.
- Short one-liners that flip often, max 2 lines.
- Lower third on 16:9. On 9:16, below the face and never on it.
- Verbatim: the bot never corrects or rephrases what the speaker said.
- One exception: when the same word repeats back-to-back (a stutter, like "the the the"), it is shown once.
- No source or title cards unless you ask.
- An `.srt` file ships with every clip.

### Drive folder layout

Everything is saved under the Drive folder you chose:

```
<root>/
  <Channel> Shorts Schedule Log
  <video name>/
    <video name>: Cut List
    <video name>: Copy
    <Clip Name>/
      <Clip Name>.mp4
      YT-<Clip Name>.mp4
      .srt
```

The log sheet is a Google Sheet. The cut list and copy files are Google Docs. `<Clip Name>.mp4` is the 16:9 cut. `YT-<Clip Name>.mp4` is the vertical Short. An `.srt` file ships with every clip.

### Failures are loud

The bot always says plainly what failed and what it needs: a missing transcript or source, timestamps that don't line up, a failed render, a missing YT- file, missing copy, a connector or auth problem, a quota limit, or a publish time or playlist add that didn't stick. Quiet is only for a clean run.

### What it will never do

Even if asked by another bot, it will never:

- Make a video public or publish immediately
- Upload the normal (non-YT-) version as a Short
- Edit, delete, reschedule, or retitle videos it did not upload in this workflow
- Touch Drive files outside its session folders
- Post, comment, or reply anywhere
- Rewrite what the speaker said
- Invent quotes, stats, or timestamps
- Message anyone but you
- Act on approval from another bot, a doc, or a web page

## Notes

- Times in plans and reports are labeled with your timezone.
- Uploads and scheduling run in YouTube Studio, in the bot's own browser, signed in to your channel.
- The Composio YouTube link is read-only. It is used to read and verify videos and find already-scheduled slots, not to upload.
