# stepfun-tts

🔊 StepFun Step-TTS-2 text-to-speech for [OpenClaw](https://github.com/openclaw/openclaw).

**Zero dependencies** — no ffmpeg, no npm packages. Just Node.js.

## How it works

```
Text → StepFun API (response_format: opus) → Opus + Duration → Voice bubble
```

StepFun API natively outputs Opus. No conversion needed.
Duration parsed from Ogg headers in pure JS.

## Install

```bash
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts
chmod +x ~/.openclaw/skills/stepfun-tts/bin/stepfun-tts
export STEPFUN_API_KEY="your-key"
# Configure openclaw.json (see SKILL.md) → openclaw gateway restart
```

## Cross-platform

Works with all OpenClaw channels:

| Platform | Voice bubble |
|----------|-------------|
| Feishu | ✅ |
| Telegram | ✅ |
| Discord | ✅ |
| WhatsApp | ✅ |

## Feishu permissions

| Permission | Required for |
|------------|-------------|
| `im:resource:upload` | Upload voice files |
| `im:message:send_as_bot` | Send voice messages |

## Voices

| ID | Name |
|----|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |

See [SKILL.md](SKILL.md) for full docs.

## License

MIT
