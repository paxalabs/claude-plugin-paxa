# Paxa Labs for Claude Code

Thai, English, and Mandarin Chinese speech AI in Claude Code. Claude can speak through your speakers, read long content aloud, save speech to audio files with matching captions, design new voices from a description, transcribe recordings and videos, translate into Thai, and read PDFs and images with OCR and field extraction.

The plugin runs the official [Paxa Labs MCP server](https://github.com/paxalabs/mcp) on your machine, and adds a skill for writing code against the [Paxa API](https://paxalabs.com/docs). This release runs server version 0.1.0-beta.16. The server is in beta, so tool names and behavior may still change before 1.0.

## Install

You need a Paxa API key. Create one at [paxalabs.com/app/keys](https://paxalabs.com/app/keys); new accounts include free credits, with no card needed.

In Claude Code, install the plugin from this repository:

```
/plugin install paxa --marketplace paxalabs/claude-plugin-paxa
```

On Claude Code versions before 2.1.275, add the marketplace first:

```
/plugin marketplace add paxalabs/claude-plugin-paxa
/plugin install paxa@paxalabs
```

Claude Code asks for your API key during the install. It masks the key as you type and keeps it in your system's secure credential store, not in a settings file.

Installing from a shell with `claude plugin install` doesn't ask for the key. Set it afterwards in a Claude Code session with `/plugin configure paxa@paxalabs`, then restart Claude Code. Until the key is set, Claude Code doesn't start the plugin's server, so the Paxa tools don't appear.

The server needs Node.js 20 or newer, with `npx` on your `PATH`. On first start, npx downloads the server from npm, which takes a few seconds.

## What Claude can do with it

| Tool | What it does |
| --- | --- |
| `speak` | Say a short line out loud through your speakers |
| `queue_speech` | Read long content aloud while Claude keeps working |
| `control_playback` | Check, pause, resume, skip, or clear what's playing |
| `play_audio` | Play a local audio file |
| `text_to_speech` | Save speech as an mp3, opus, or wav file, with matching srt or vtt captions; long scripts become one file |
| `design_voice`, `refine_voice` | Make a new voice from a written description, then adjust it in words |
| `save_voice`, `rename_voice`, `delete_voice` | Keep, rename, or delete your designed voices |
| `transcribe_audio` | Transcribe a recording or video, with speaker labels, word timings, subtitles, and a list of pauses to cut |
| `listen` | Record one spoken reply from your microphone and return what was said |
| `translate_to_thai` | Translate any language into Thai |
| `ocr_document` | Read a PDF or image into Markdown |
| `extract_fields` | Fill a schema of typed fields, such as dates, amounts, and Thai ID numbers, from a PDF or image |
| `list_voices`, `list_models` | Show the voices, and the models with their limits and prices |
| `get_account` | Show your credit balance and plan |
| `send_feedback` | Report a wrong output or a problem to the Paxa team |

Speech, voice design, transcription, listening, translation, OCR, and extraction spend credits from your Paxa account. Each tool's description states its price, and the [pricing page](https://paxalabs.com/pricing) has the full list. The other tools are free.

Speech uses Paxa TTS Flash v2 by default: 50 voices that each speak Thai, English, and Mandarin, including mixed sentences. Every voice follows direction tags written into the text, such as `[whispering]` or `[speaking slowly]`; the [directions guide](https://paxalabs.com/docs/directions) lists them all. Ask Claude to list the voices and play a few, then tell it which one you like.

A few tools use programs on your machine:

- Playback uses a local audio player: `afplay`, built into macOS, or `ffplay`, `mpv`, `mpg123`, `paplay`, or `aplay` on Linux. With `mpg123` or `ffmpeg` installed, speech starts playing while it's still downloading.
- Transcribing video, and joining long narrations into one file, need `ffmpeg`.
- `listen` needs Node.js 22 or newer and a recorder such as `ffmpeg` or `sox`, and the app that runs Claude Code needs microphone permission.

The [server's README](https://github.com/paxalabs/mcp#readme) covers each platform in detail.

## Examples

Ask Claude in plain words. For example:

- "When you finish this task, tell me out loud what changed."
- "Read this article aloud in a calm voice."
- "Turn script.md into a Thai voiceover called narration.mp3, with matching SRT captions."
- "Transcribe meeting.m4a with speaker labels, and save SRT subtitles."
- "Pull the invoice number, date, seller, and total out of invoice.pdf."
- "Translate this paragraph into formal Thai."
- "Add Paxa text to speech to this app, reading the key from PAXA_API_KEY."

## Build with the Paxa API

The plugin also adds the `paxa-api` skill. When you ask Claude to write code that uses Paxa, such as Thai speech in an app or transcription in a voice agent, Claude loads the skill. It points Claude at the current API reference on paxalabs.com and the rules every integration follows, such as reading the key from the environment and taking model and voice ids from the catalog. You can also run it directly with `/paxa:paxa-api`.

## What runs, and what is sent where

- Claude Code starts the server on your machine with `npx -y @paxalabs/mcp@0.1.0-beta.16`. On first start, npm downloads exactly that version from the npm registry. Its source is at [github.com/paxalabs/mcp](https://github.com/paxalabs/mcp).
- The server sends `api.paxalabs.com` only what Claude passes to a tool, over HTTPS: the text to speak or translate, the recording to transcribe, or the document to read. Nothing else from your conversation is sent.
- Claude Code passes your API key to the server as the `PAXA_API_KEY` environment variable, and the server sends it to the API with each request.
- Speech plays through a local audio player, from a temporary folder that the server removes when it exits. `listen` records from your microphone only while it runs, and streams the audio to the API without saving it.
- `text_to_speech` writes audio and caption files where Claude asks it to, by default in the folder you started Claude Code in.
- The `paxa-api` skill reads documentation pages from paxalabs.com.

How Paxa Labs handles the data it receives is described in the [privacy policy](https://paxalabs.com/privacy). Use of the API is covered by the [terms of service](https://paxalabs.com/terms).

## Updates

Each release of this plugin pins one exact server version, so new server releases reach you only through a plugin update. To update, run `claude plugin update paxa@paxalabs` in a shell, or turn on auto-update for the `paxalabs` marketplace in `/plugin`, under **Marketplaces**.

## Troubleshooting

- **The Paxa tools are missing, or a tool says `PAXA_API_KEY is not set`:** run `/plugin configure paxa@paxalabs`, enter your key, and restart Claude Code.
- **`/mcp` shows `plugin:paxa:paxa` as failed:** check that `npx --version` works in the shell you start Claude Code from. If the first download is slow, start Claude Code with a longer startup timeout, such as `MCP_TIMEOUT=60000 claude`.
- **Every Paxa tool appears twice:** you also added the server with `claude mcp add`. Remove that entry with `claude mcp remove paxa`.
- **claude.ai and Cowork:** the server runs in Claude Code, including the IDE extensions and the desktop app's Code tab. In claude.ai chat and Cowork, the plugin adds only the `paxa-api` skill. For Claude Desktop chats, install the `.mcpb` bundle from the server's [releases page](https://github.com/paxalabs/mcp/releases).

## License

MIT. Report security issues privately to hello@paxalabs.com.
