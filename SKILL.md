---
name: stepfun-tts
description: StepFun Step-TTS-2 text-to-speech for OpenClaw. MP3 → Opus conversion + duration detection for Feishu voice bubbles.
version: 0.2.0
author: "marine"
tags:
  - tts
  - stepfun
  - speech
  - chinese
  - feishu
  - opus
metadata:
  openclaw:
    emoji: "🔊"
    os: ["darwin", "linux", "win32"]
    requires:
      env: ["STEPFUN_API_KEY"]
      bin: ["ffmpeg", "ffprobe"]
---

# StepFun TTS for OpenClaw

Integrates [StepFun's Step-TTS-2](https://platform.stepfun.com/) text-to-speech API as an OpenClaw TTS provider.

## Pipeline

```
Text → StepFun API → MP3 → ffmpeg → Opus → ffprobe → duration
                                      ↓
                              { audioPath, format: "opus", duration: 3 }
```

**Why Opus?** 飞书语音消息要求 Opus 格式 + 时长（秒）。本 skill 自动完成转换。

## Features

- 🇨🇳 High-quality Chinese TTS (multiple voices)
- 🔄 Auto MP3→Opus conversion via ffmpeg
- ⏱ Duration detection via ffprobe (integer seconds)
- 📱 飞书语音气泡完整支持 (file_type=opus + duration)
- 🔒 API key via environment variable
- 📦 Zero npm dependencies (Node.js built-ins + system ffmpeg)

## Quick Start

### Prerequisites

```bash
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt install ffmpeg
```

### Install

```bash
clawhub install stepfun-tts
# or copy to ~/.openclaw/skills/stepfun-tts/
```

### Configure

```bash
export STEPFUN_API_KEY="your-api-key"
```

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

```bash
openclaw gateway restart
```

## Standalone Usage

```bash
echo '{"text":"你好","config":{"apiKey":"xxx"}}' | node bin/stepfun-tts
# {"audioPath":"/tmp/openclaw/tts/stepfun-xxx.opus","format":"opus","duration":2}
```

**Force MP3 output** (skip Opus conversion):
```bash
echo '{"text":"hello","config":{"apiKey":"xxx","format":"mp3"}}' | node bin/stepfun-tts
```

## Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `STEPFUN_API_KEY` | ✅ | — | StepFun API key |
| `STEPFUN_BASE_URL` | ❌ | `https://api.stepfun.com/v1` | API base URL |
| `STEPFUN_TTS_MODEL` | ❌ | `step-tts-2` | TTS model |
| `STEPFUN_TTS_VOICE` | ❌ | `yuanqishaonv` | Default voice |
| `STEPFUN_TTS_FORMAT` | ❌ | `opus` | Output format: `opus` or `mp3` |
| `STEPFUN_TIMEOUT_MS` | ❌ | `30000` | Request timeout (ms) |
| `OPENCLAW_TTS_OUT_DIR` | ❌ | `/tmp/openclaw/tts` | Output directory |

## Available Voices

| Voice ID | Description |
|----------|-------------|
| `yuanqishaonv` | 元气少女 |
| `zhishidanv` | 知识大女 |
| `wenrounvsheng` | 温柔女声 |
| `chengshunansheng` | 成熟男声 |
| `huoponvhai` | 活泼女孩 |

## Handler Protocol

**Input** (stdin JSON):
```json
{ "text": "你好", "config": { "format": "opus" } }
```

**Output** (stdout JSON):
```json
{ "audioPath": "/tmp/.../stepfun-xxx.opus", "format": "opus", "duration": 2 }
```

**Error**: exit(1), message on stderr.

## License

MIT
