# AI Video Narrator

A React app that adds AI-generated narration to your videos. Upload a video, describe how you want it narrated, and the app uses OpenAI to write and speak the script, then mixes it with your video—all in the browser with no backend.

## Features

- **Upload & analyze** – Upload a video; the app samples 20 frames evenly over the duration for context.
- **Narration length** – Target length is computed from video duration (100 words per minute) and passed into the prompt.
- **AI narration text** – Instructions + frame images go to OpenAI’s vision model; you get editable narration text.
- **AI narration audio** – Text is turned into speech with OpenAI TTS (voice: **nova**).
- **Audio mixing** – Video-editor style UI with separate volume sliders for video audio and AI narration.
- **Timeline** – Single timeline; click to seek. Preview plays video + narration in sync (with your volume mix) before export.
- **Export** – Merge is done with **ffmpeg.wasm** (fast, no real-time playback). Output is MP4. Download uses the name: `videofilename_ai_narration_YYYY-MM-DD_HH-MM-SS.mp4`.

## Setup

1. **Install dependencies**

   ```bash
   npm install
   ```

2. **Environment variables**

   Create a `.env` in the project root (you can copy from `.env.example`):

   ```env
   REACT_APP_OPENAI_API_KEY=your_openai_api_key
   REACT_APP_MODEL=gpt-5-nano
   ```

3. **Run the app**

   ```bash
   npm start
   ```

   Open [http://localhost:3000](http://localhost:3000).

## Usage

### Narration tab

1. **Upload video** – Choose a video file. The app extracts 20 frames and shows progress.
2. **Instructions** – Type how you want the narration (tone, focus, style). This is injected into the prompt template at `public/prompt_narration.txt` as `{instructions}`. The template also receives `{num_words}` (from video duration at 100 wpm).
3. **Create Narration Text** – Sends frames + prompt to OpenAI; the result appears in an editable text box.
4. **Create Narration Audio** – Converts that text to speech with TTS (nova). Use the audio player to listen.
5. Optionally edit the text and regenerate audio.

### Audio Mixing tab

1. **Preview** – Shows the source video or, after merge, the final video.
2. **Time** – Displays current time / total duration.
3. **Timeline** – Click to seek; video and both audio tracks move together. You hear the mix at the current volume levels (no need to export to preview).
4. **Tracks** – Two rows:
   - **Video audio** – Volume slider (0–100%).
   - **AI Narration** – Volume slider (0–100%).
5. **Create Video** – Runs ffmpeg.wasm to mix video + both audio tracks and produce an MP4. First run loads the FFmpeg core (~31 MB) once.
6. **Export** – Downloads the merged file as `videofilename_ai_narration_YYYY-MM-DD_HH-MM-SS.mp4`.

## Project structure

- `public/prompt_narration.txt` – Prompt template. Placeholders: `{num_words}`, `{instructions}`.
- `src/App.js` – Main UI, OpenAI calls, ffmpeg merge, timeline seek/preview.
- `.env` – `REACT_APP_OPENAI_API_KEY`, `REACT_APP_MODEL` (not committed; use `.env.example` as a template).

## Tech stack

- **React** (Create React App)
- **OpenAI** – Responses API (vision + text) for narration script; Audio API (TTS) for speech.
- **ffmpeg.wasm** – In-browser merge: video + video audio + narration, output MP4. No server required.

## Notes

- Processing is **client-side only** (no backend).
- **Large videos** – ffmpeg.wasm keeps files in memory. Very large inputs (e.g. >100 MB) can hit limits; shorter or smaller files are more reliable.
- **Narration length** – Computed as `(duration_seconds / 60) * 100` words and passed as `{num_words}` into the prompt.

## Scripts

| Command        | Description                |
|----------------|----------------------------|
| `npm start`    | Development server         |
| `npm run build`| Production build           |
| `npm test`     | Run tests                  |
