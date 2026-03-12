# stepfun-tts

StepFun Step-TTS-2 text-to-speech skill for [OpenClaw](https://github.com/openclaw/openclaw).

High-quality Chinese & English speech synthesis via StepFun's TTS API.

## Install

```bash
clawhub install stepfun-tts
# or manually copy to ~/.openclaw/skills/stepfun-tts/
```

## Setup

1. Get API key from [StepFun Platform](https://platform.stepfun.com/)
2. `export STEPFUN_API_KEY="your-key"`
3. Add TTS config to `~/.openclaw/openclaw.json` (see SKILL.md)
4. `openclaw gateway restart`

## Voices

| ID | Name |
|----|------|
| `yuanqishaonv` | 元气少女 |
| `wenrounvsheng` | 温柔女声 |
| `chengshunansheng` | 成熟男声 |

## License

MIT
