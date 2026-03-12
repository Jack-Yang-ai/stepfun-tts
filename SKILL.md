---
name: stepfun-tts
description: StepFun Step-TTS-2 text-to-speech integration for OpenClaw. Converts text to natural Chinese/English speech via StepFun's TTS API.
version: 0.1.0
author: "marine"
tags:
  - tts
  - stepfun
  - speech
  - chinese
  - audio
metadata:
  openclaw:
    emoji: "🔊"
    os: ["darwin", "linux", "win32"]
    requires:
      env: ["STEPFUN_API_KEY"]
---

# StepFun TTS for OpenClaw

Integrates [StepFun's Step-TTS-2](https://platform.stepfun.com/) text-to-speech API as an OpenClaw TTS provider. Produces natural-sounding Chinese and English speech.

## Features

- 🇨🇳 High-quality Chinese TTS (multiple voices)
- 🔄 OpenAI-compatible API format
- ⚡ Fast generation (typically < 2s for short text)
- 🔒 API key via environment variable (no hardcoding)
- 📦 Zero external dependencies (Node.js built-ins only)

## Quick Start

### 1. Get your API key

Sign up at [StepFun Platform](https://platform.stepfun.com/) and create an API key.

### 2. Set environment variable

```bash
export STEPFUN_API_KEY="your-api-key-here"
```

### 3. Configure OpenClaw

Add to `~/.openclaw/openclaw.json`:

```json5
{
  "messages": {
    "tts": {
      "auto": "always",
      "provider": "openai",
      "openai": {
        "apiKey": "${STEPFUN_API_KEY}",
        "model": "step-tts-2",
        "voice": "yuanqishaonv"
      },
      "maxTextLength": 100
    }
  }
}
```

> **Note:** StepFun's TTS API is OpenAI-compatible, so we use the `openai` provider with StepFun's base URL configured via the handler.

### 4. Restart gateway

```bash
openclaw gateway restart
```

## Standalone Usage

The handler can also be used standalone:

```bash
echo '{"text": "你好世界", "config": {"apiKey": "your-key"}}' | node bin/stepfun-tts
# Output: {"audioPath": "/tmp/openclaw/tts/stepfun-xxx.mp3", "format": "mp3"}
```

Or via curl directly:

```bash
curl -sS 'https://api.stepfun.com/v1/audio/speech' \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer $STEPFUN_API_KEY" \
  -d '{"model":"step-tts-2","voice":"yuanqishaonv","input":"你好世界"}' \
  --output output.mp3
```

## Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `STEPFUN_API_KEY` | ✅ | — | StepFun API key |
| `STEPFUN_BASE_URL` | ❌ | `https://api.stepfun.com/v1` | API base URL |
| `STEPFUN_TTS_MODEL` | ❌ | `step-tts-2` | TTS model name |
| `STEPFUN_TTS_VOICE` | ❌ | `yuanqishaonv` | Default voice ID |
| `STEPFUN_TIMEOUT_MS` | ❌ | `30000` | Request timeout (ms) |
| `OPENCLAW_TTS_OUT_DIR` | ❌ | `/tmp/openclaw/tts` | Audio output directory |

### Available Voices

| Voice ID | Description |
|----------|-------------|
| `yuanqishaonv` | 元气少女 (Energetic girl) |
| `zhishidanv` | 知识大女 (Knowledgeable woman) |
| `wenrounvsheng` | 温柔女声 (Gentle female) |
| `chengshunansheng` | 成熟男声 (Mature male) |
| `huoponvhai` | 活泼女孩 (Lively girl) |

> Check StepFun's documentation for the latest voice list.

## Handler Protocol

The handler follows the OpenClaw TTS provider contract:

**Input** (stdin JSON):
```json
{
  "text": "Hello world",
  "config": {
    "apiKey": "optional-override",
    "model": "step-tts-2",
    "voice": "yuanqishaonv"
  }
}
```

**Output** (stdout JSON):
```json
{
  "audioPath": "/tmp/openclaw/tts/stepfun-1234567890-abc123.mp3",
  "format": "mp3"
}
```

**Error**: Exit code 1, error message on stderr.

## Security

- API key read from `STEPFUN_API_KEY` env var or stdin config — never hardcoded
- No API key logging
- Temp files in `/tmp/openclaw/tts/` — cleaned by OS
- HTTPS only with Node.js default certificate validation

## License

MIT
