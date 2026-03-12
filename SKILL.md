---
name: stepfun-tts
description: |
  StepFun TTS for OpenClaw. Zero dependencies — API直出Opus，无需ffmpeg。
  支持语音合成 + 音色复刻。

  **当以下情况时使用此 Skill**：
  (1) 用户要求用 StepFun TTS 合成语音
  (2) 用户发送音频/语音文件并要求"克隆音色"、"复刻声音"、"用我的声音"
  (3) 用户提到"换音色"、"自定义音色"、"voice clone"
  (4) 用户提到预设音色切换（如"换成元气少女"）
version: 1.1.0
author: "marine"
tags:
  - tts
  - stepfun
  - speech
  - chinese
  - voice-clone
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

End-to-end text-to-speech + voice cloning. **Zero dependencies** — no ffmpeg, no npm packages.

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

## 音色复刻 🎙️

### 大白话用法（Agent 自动化）

用户只需要：
1. **发一段录音**（5-10 秒，飞书语音/文件都行）
2. **说一句**："用这个声音"、"克隆这个音色"、"我想用自己的声音"

Agent 自动完成全部流程，无需任何命令行操作。

### Agent 执行流程（当用户触发音色复刻时）

```
步骤 1: 下载用户发送的音频文件
        → 使用 feishu_im_bot_image (type=file) 下载到本地

步骤 2: 询问录音内容（如果用户没说的话）
        → "这段录音里你说了什么？我需要知道文字内容来做匹配"

步骤 3: 执行复刻脚本
        → exec: STEPFUN_API_KEY=$KEY node bin/stepfun-voice-clone \
            --file /tmp/openclaw/downloaded-audio.mp3 \
            --text "用户说的对应文本" \
            --sample "你好，这是我的专属音色"

步骤 4: 解析输出 → 拿到 voiceId

步骤 5: 更新 openclaw.json 的 tts.openai.voice 字段
        → 写入新的 voiceId
        → 重启 gateway

步骤 6: 用新音色做一次 TTS 试听
        → tts("你好，这是你的专属音色，听听效果怎么样？")

步骤 7: 回复用户结果
```

### 触发关键词

以下表述会触发此流程：
- "克隆这个音色" / "复刻声音" / "用我的声音"
- "voice clone" / "clone this voice"
- 发送音频文件 + "用这个当音色"
- "换成我自己的声音"

### CLI 用法（高级）

```bash
# CLI 方式
node bin/stepfun-voice-clone --file ./my-voice.mp3 --text "音频对应的文本" --sample "试听文本"

# stdin JSON 方式
echo '{"file":"./my-voice.mp3","text":"对应文本","sampleText":"你好世界"}' | node bin/stepfun-voice-clone
```

输出：
```json
{"voiceId":"voice-abc123","duplicated":false,"sampleAudioPath":"/tmp/.../voice-sample-xxx.wav","sampleText":"你好世界"}
```

### 复刻要求

| 项目 | 要求 |
|------|------|
| 格式 | mp3 或 wav |
| 时长 | 5-10 秒 |
| 内容 | 清晰人声，少背景噪音 |
| text | 强烈建议传入（不传用 ASR，但可能因 CER 校验失败） |

### 复刻流程

```
音频文件 → 上传到 StepFun (purpose: storage) → file_id
                                                  ↓
file_id + model → POST /audio/voices → voice_id + 试听音频
                                          ↓
voice_id → 写入 openclaw.json → 重启 gateway → 全局生效
```

## 预设音色

| ID | 描述 |
|----------|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `zhishidanv` | 知识女声 📚 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |
| `huoponvhai` | 活泼女孩 ✨ |

## License

MIT
