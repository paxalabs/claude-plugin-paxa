---
name: paxa-api
description: Write or debug code that calls the Paxa Labs API (api.paxalabs.com) for Thai, English, and Mandarin Chinese text to speech, speech to text, document OCR, field extraction, or translation into Thai. Use when building an app, script, or voice agent on Paxa, or pointing an OpenAI audio client at Paxa. To speak, transcribe, or read a file in this session instead, use the paxa MCP tools.
---

# Build with the Paxa Labs API

Paxa's models, voices, request fields, and prices change as new versions ship, so check the current API reference rather than relying on memory. Every docs page is also served as Markdown at its path plus `.md`, and https://paxalabs.com/llms.txt indexes them all. Start with the page for the task:

- Speech: https://paxalabs.com/docs/api/text-to-speech.md
- Transcription: https://paxalabs.com/docs/api/stt.md, or https://paxalabs.com/docs/api/stt-live.md for realtime
- OCR: https://paxalabs.com/docs/api/ocr.md
- Field extraction: https://paxalabs.com/docs/api/extract.md
- Translation into Thai: https://paxalabs.com/docs/api/translate.md
- OpenAI SDKs: https://paxalabs.com/docs/openai-compatibility.md
- Errors: https://paxalabs.com/docs/errors.md

Use these pages as reference for request and response shapes, ids, limits, and prices. If you can't fetch them, follow the rules below and tell the user which details you couldn't check.

## Rules that always hold

- The base URL is `https://api.paxalabs.com`. Authenticate with `Authorization: Bearer <key>`, or `x-api-key: <key>`.
- Read the key from the `PAXA_API_KEY` environment variable. Never hardcode, log, or commit a key. Keys are created at https://paxalabs.com/app/keys and start with `pxa_`.
- Never invent model or voice ids. The served catalog is `GET /v1/models` and `GET /v1/voices`.
- Every error is an RFC 9457 `application/problem+json` body. Branch on its `title` field, a stable code such as `unknown_voice`, never on the `detail` wording.
- On `/v1/stt`, `/v1/ocr`, `/v1/extract`, and `/v1/translate`, a long request can commit its 200 status before the outcome is known and report a failure in the body. Treat a JSON body that carries `title` as that error.
- `POST /v1/audio/speech` and `POST /v1/audio/transcriptions` follow the OpenAI audio API, so an OpenAI SDK with its base URL set to `https://api.paxalabs.com/v1` works. Pass Paxa model and voice ids from the catalog.

## Try things in this session

When the paxa MCP tools are available, use them to check choices while you build: `list_voices` and `list_models` show the current ids, `text_to_speech` renders a sample line to a file, and `transcribe_audio` or `ocr_document` shows how the API reads one of the user's real files. They spend credits the same way API calls do.
