# stepfun-tts

🔊 StepFun Step-TTS-2 text-to-speech skill for [OpenClaw](https://github.com/openclaw/openclaw).

One-stop solution: **text → speech → 飞书语音气泡**

## Features

- 🇨🇳 High-quality Chinese TTS (Step-TTS-2)
- 🔄 Auto MP3 → Opus conversion (ffmpeg)
- ⏱ Duration detection for Feishu voice messages
- 📱 Feishu voice bubble support (`im:resource:upload`)
- 🔌 OpenClaw integration OR standalone mode
- 📦 Zero npm dependencies

## Quick Start

```bash
# 1. Install
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts

# 2. Set API key
export STEPFUN_API_KEY="your-key"

# 3. Configure OpenClaw (see SKILL.md)
# 4. openclaw gateway restart
```

## Feishu Permissions Required

| Permission | Description |
|------------|-------------|
| `im:resource` | Read IM resources |
| **`im:resource:upload`** | **Upload IM resources (voice files)** |
| `im:message:send_as_bot` | Send messages as bot |

## Voices

| ID | Name |
|----|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |

## Pipeline

```
Text → StepFun API → MP3 → ffmpeg → Opus → Feishu Upload → Voice Bubble
```

See [SKILL.md](SKILL.md) for full documentation.

## License

MIT
