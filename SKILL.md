---
name: stepfun-tts
description: StepFun Step-TTS-2 TTS for OpenClaw. Zero dependencies — API直出Opus，无需ffmpeg。
version: 1.0.0
author: "marine"
tags:
  - tts
  - stepfun
  - speech
  - chinese
  - feishu
  - telegram
  - discord
  - opus
metadata:
  openclaw:
    emoji: "🔊"
    os: ["darwin", "linux", "win32"]
    requires:
      env: ["STEPFUN_API_KEY"]
---

# StepFun TTS for OpenClaw

End-to-end text-to-speech. **Zero dependencies** — no ffmpeg, no npm packages.

```
Text → StepFun API (response_format: opus) → Opus file + duration
                                                ↓
                                    OpenClaw gateway auto-sends:
                                    飞书 / Telegram / Discord / WhatsApp
```

## 为什么零依赖？

StepFun API 的 `response_format` 参数直接支持 `opus` 输出，不需要 ffmpeg 转码。
Duration 通过解析 Ogg 文件头的 granule position 计算，纯 JS 实现。

## 安装 3 步

### 1. 装 skill

```bash
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts
chmod +x ~/.openclaw/skills/stepfun-tts/bin/stepfun-tts
```

### 2. 设 API key

```bash
export STEPFUN_API_KEY="your-stepfun-api-key"
```

### 3. 配 OpenClaw

编辑 `~/.openclaw/openclaw.json`：

```json5
{
  "messages": {
    "tts": {
      "auto": "always",
      "provider": "openai",
      "openai": {
        "apiKey": "your-stepfun-api-key",
        "model": "step-tts-2",
        "voice": "yuanqishaonv",
        "baseUrl": "https://api.stepfun.com/v1"
      },
      "maxTextLength": 100
    }
  }
}
```

```bash
openclaw gateway restart
```

搞定！所有 channel（飞书/Telegram/Discord/WhatsApp）自动语音输出。

## 跨平台支持

OpenClaw 网关内建了语音气泡支持，skill 只管生成 opus 文件：

| 平台 | 语音气泡 | 需要的权限 |
|------|---------|-----------|
| 飞书 | ✅ | `im:resource:upload` + `im:message:send_as_bot` |
| Telegram | ✅ | Bot API sendVoice（自动） |
| Discord | ✅ | 发消息权限（自动） |
| WhatsApp | ✅ | 自动 |

## 独立使用

```bash
echo '{"text":"你好"}' | STEPFUN_API_KEY=xxx node bin/stepfun-tts
# {"audioPath":"/tmp/.../stepfun-xxx.opus","format":"opus","duration":2}
```

支持的 `response_format`：`opus`（默认）、`mp3`、`wav`、`flac`

## 环境变量

| 变量 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `STEPFUN_API_KEY` | ✅ | — | StepFun API key |
| `STEPFUN_TTS_MODEL` | ❌ | `step-tts-2` | TTS 模型 |
| `STEPFUN_TTS_VOICE` | ❌ | `yuanqishaonv` | 默认音色 |
| `STEPFUN_TTS_FORMAT` | ❌ | `opus` | 输出格式 |
| `STEPFUN_BASE_URL` | ❌ | `https://api.stepfun.com/v1` | API 地址 |

## 音色

| ID | 描述 |
|----------|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `zhishidanv` | 知识女声 📚 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |
| `huoponvhai` | 活泼女孩 ✨ |

## License

MIT
