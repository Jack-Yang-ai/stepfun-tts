# StepFun TTS for OpenClaw 🔊

让你的 OpenClaw AI 助手开口说话。飞书 / Telegram / Discord / WhatsApp 全平台语音气泡输出。

**零依赖** — 不需要 ffmpeg，不需要 npm install，clone 下来就能用。

## 效果

你发消息 → AI 回复 → 自动转语音 → 收到语音气泡 🎵

## 快速开始

```bash
# 1. 下载
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts
chmod +x ~/.openclaw/skills/stepfun-tts/bin/*

# 2. 配置 (编辑 ~/.openclaw/openclaw.json)
```

在 `openclaw.json` 中添加：

```json
{
  "messages": {
    "tts": {
      "auto": "always",
      "provider": "openai",
      "openai": {
        "apiKey": "你的-stepfun-api-key",
        "model": "step-tts-2",
        "voice": "yuanqishaonv",
        "baseUrl": "https://api.stepfun.com/v1"
      },
      "maxTextLength": 200
    }
  }
}
```

```bash
# 3. 重启
openclaw gateway restart
```

> provider 写 `openai` 是因为 StepFun API 与 OpenAI 格式完全兼容，OpenClaw 可直接复用。

## 飞书用户注意

飞书机器人需要额外权限才能发语音：

1. [飞书开放平台](https://open.feishu.cn/app) → 你的应用 → 权限管理
2. 开启 `im:resource`（上传文件）+ `im:message`（发消息）
3. **创建新版本并发布**（不发版权限不生效）

Telegram / Discord / WhatsApp 无需额外配置。

## 预设音色

| ID | 风格 |
|----|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `zhishidanv` | 知识女声 📚 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |
| `huoponvhai` | 活泼女孩 ✨ |

## 进阶：音色复刻

用你自己的声音创建专属音色（5-10 秒录音即可）：

```bash
STEPFUN_API_KEY=xxx node bin/stepfun-voice-clone \
  --file ./my-voice.mp3 \
  --text "录音里说的内容" \
  --sample "试听文本"
```

详见 [SKILL.md](./SKILL.md)。

## 技术

- StepFun API `response_format: opus` 直出，无需转码
- 音频时长通过 Ogg 文件头 granule position 解析，纯 JS
- 只依赖 Node.js 标准库

## License

MIT
