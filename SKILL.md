---
name: stepfun-tts
description: |
  StepFun 语音合成 for OpenClaw。零依赖，API 直出 Opus，无需 ffmpeg。
  让你的 AI 助手说话——飞书/Telegram/Discord/WhatsApp 全平台语音气泡输出。

  **触发场景**：
  (1) 用户要求 AI 用语音回复
  (2) 配置 TTS 让机器人自动语音输出
  (3) 切换预设音色（如"换成成熟男声"）
  (4) 音色复刻——用自己的声音（进阶功能）
version: 1.2.0
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

# StepFun TTS for OpenClaw 🔊

让你的 OpenClaw 机器人开口说话。支持飞书、Telegram、Discord、WhatsApp 语音气泡。

**零依赖** — 不需要 ffmpeg，不需要 npm install，开箱即用。

```
你发消息 → AI 生成回复 → StepFun API 合成语音 → 语音气泡发到你的聊天
```

---

## 快速开始（3 分钟搞定）

### 1. 下载 skill

```bash
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts
chmod +x ~/.openclaw/skills/stepfun-tts/bin/*
```

### 2. 获取 StepFun API Key

去 [StepFun 开放平台](https://platform.stepfun.com/) 注册，创建一个 API Key。

### 3. 配置 OpenClaw

编辑 `~/.openclaw/openclaw.json`，添加以下配置（和现有配置同级）：

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

> **Q: 为什么 provider 写 `openai` 而不是 `stepfun`？**
> A: StepFun 的 API 接口格式和 OpenAI 完全一样（都是 `POST /audio/speech`），OpenClaw 可以直接复用 `openai` provider，只需把 `baseUrl` 指向 StepFun 即可。

### 4. 重启

```bash
openclaw gateway restart
```

### 5. 测试

给你的机器人发条消息。如果收到**语音气泡**而不是纯文字 → 成功 ✅

---

## 飞书用户额外步骤（必做）

飞书机器人发语音需要额外权限。不做这一步，你只会收到文字。

1. 登录 [飞书开放平台](https://open.feishu.cn/app)
2. 进入你的机器人应用 → **权限管理**
3. 搜索并开启：
   - `im:resource` — 上传文件（语音文件要先上传才能发）
   - `im:message` — 发送消息
4. **发布新版本**（权限改了不发版不生效！）
   - 版本管理与发布 → 创建版本 → 申请发布

> Telegram / Discord / WhatsApp 不需要额外配置，开箱即用。

---

## 配置说明

### openclaw.json 字段解释

| 字段 | 值 | 说明 |
|------|-----|------|
| `auto` | `"always"` | 每条回复都转语音。改成 `"short"` 则只有短回复转语音 |
| `provider` | `"openai"` | 固定写 openai（因为 API 兼容） |
| `apiKey` | 你的 key | StepFun 平台获取 |
| `model` | `"step-tts-2"` | 推荐。也可用 `step-tts-mini`（更快）或 `step-tts-vivid`（更有表现力） |
| `voice` | `"yuanqishaonv"` | 预设音色，见下方音色列表 |
| `baseUrl` | `"https://api.stepfun.com/v1"` | StepFun API 地址，不要改 |
| `maxTextLength` | `200` | 超过这个字数的回复不转语音（避免长文浪费额度） |

### 环境变量（独立脚本使用时需要）

如果你要在命令行单独使用 TTS 脚本或音色复刻功能，还需要设环境变量：

```bash
# 加到 ~/.zshrc 或 ~/.bashrc
export STEPFUN_API_KEY="你的-stepfun-api-key"
```

> **注意两处 key 的关系**：`openclaw.json` 里的 apiKey 给网关用（自动 TTS），环境变量给脚本用（手动调用 / 音色复刻）。内容一样，但两处都要配。

---

## 预设音色

| 音色 ID | 风格 |
|---------|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `zhishidanv` | 知识女声 📚 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |
| `huoponvhai` | 活泼女孩 ✨ |

切换音色：修改 `openclaw.json` 里的 `voice` 字段，然后 `openclaw gateway restart`。

---

## 跨平台支持

| 平台 | 语音气泡 | 额外配置 |
|------|---------|---------|
| 飞书 | ✅ | 需要加权限 + 发版（见上方） |
| Telegram | ✅ | 无需额外配置 |
| Discord | ✅ | 无需额外配置 |
| WhatsApp | ✅ | 无需额外配置 |

---

## 独立使用（不通过 OpenClaw）

如果你只想在命令行用，不接 OpenClaw：

```bash
echo '{"text":"你好世界"}' | STEPFUN_API_KEY=xxx node bin/stepfun-tts
# 输出: {"audioPath":"/tmp/.../stepfun-xxx.opus","format":"opus","duration":2}
```

支持的输出格式：`opus`（默认）、`mp3`、`wav`、`flac`

---

## Troubleshooting

| 症状 | 原因 | 解决 |
|------|------|------|
| 飞书只收到文字，没语音 | 飞书应用缺权限 | 开放平台加 `im:resource` + `im:message` 权限，**然后发版** |
| 语音文件生成了但发不出 | Gateway 没读到 TTS 配置 | 检查 `openclaw.json` 格式是否正确，`openclaw gateway restart` |
| `STEPFUN_API_KEY not set` | 环境变量没配 | `export STEPFUN_API_KEY=xxx` 加到 shell 配置 |
| HTTP 401 | API Key 无效或过期 | 去 StepFun 平台重新生成 |
| 语音生成但声音卡顿 | 网络问题或文本过长 | 减小 `maxTextLength`，检查网络 |

---

## 进阶：音色复刻 🎙️

> 这是可选的高级功能。如果你只想用预设音色，跳过这部分。

用你自己的声音（或任何人的声音）创建专属音色。

### 怎么用

**最简单的方式**（通过 AI 助手）：
1. 录一段 5-10 秒的语音（手机录音就行，安静环境）
2. 发给你的 OpenClaw 机器人
3. 说"用这个声音"或"克隆这个音色"
4. AI 自动完成全部流程

**命令行方式**：
```bash
STEPFUN_API_KEY=xxx node bin/stepfun-voice-clone \
  --file ./my-voice.mp3 \
  --text "录音里说的内容" \
  --sample "试听用的文本"
```

输出：
```json
{"voiceId":"voice-tone-xxx","sampleAudioPath":"/tmp/.../voice-sample-xxx.wav"}
```

拿到 `voiceId` 后，改 `openclaw.json` 里的 `voice` 字段，重启 gateway 即可。

### 录音建议

| 项目 | 要求 |
|------|------|
| 时长 | 5-10 秒 |
| 格式 | mp3 或 wav |
| 环境 | 安静，无背景噪音 |
| 采样率 | ≥ 16kHz（手机默认录音一般够） |
| 内容 | 自然说话，别刻意播音腔 |
| text 参数 | 强烈建议传入录音对应文字（提高成功率） |

### 复刻失败？

| 错误 | 原因 | 解决 |
|------|------|------|
| `CER_NOT_PASS` | 传入的文字和录音内容不匹配 | 确保 `--text` 和录音说的一字不差，或不传让 ASR 识别 |
| `INVALID_AUDIO_FILE` | 格式不对或文件损坏 | 用 mp3/wav，5-10 秒，采样率 ≥16kHz |

---

## 技术细节

### 为什么零依赖？

StepFun API 的 `response_format` 参数直接支持 `opus` 输出：
- 不需要 ffmpeg 做格式转换
- 音频时长通过解析 Ogg 文件头的 granule position 计算，纯 Node.js 实现
- 整个 skill 只依赖 Node.js 标准库（fs, https, path）

### 架构

```
bin/stepfun-tts          → TTS 合成（text → opus/mp3/wav）
bin/stepfun-voice-clone  → 音色复刻（audio → voiceId）

OpenClaw Gateway 自动调用 stepfun-tts，按平台发送语音气泡：
├── 飞书: 上传 opus → sendVoiceMessage
├── Telegram: sendVoice API
├── Discord: sendVoiceMessage
└── WhatsApp: sendAudio
```

## License

MIT
