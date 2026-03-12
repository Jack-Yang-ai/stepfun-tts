---
name: stepfun-tts
description: StepFun Step-TTS-2 TTS for OpenClaw + Feishu voice messages. One-stop solution.
version: 0.3.0
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
      bin: ["ffmpeg"]
---

# StepFun TTS for OpenClaw

End-to-end text-to-speech for OpenClaw + 飞书语音气泡。

```
Text → StepFun API → MP3 → ffmpeg → Opus + Duration
                                       ↓
                            OpenClaw gateway 自动发送飞书语音
                                       or
                            独立模式: 直接上传飞书 + 发语音消息
```

## 安装 3 步走

### Step 1: 安装 skill

```bash
# 方式 A: clawhub
clawhub install stepfun-tts

# 方式 B: 手动
git clone https://github.com/Jack-Yang-ai/stepfun-tts.git ~/.openclaw/skills/stepfun-tts
chmod +x ~/.openclaw/skills/stepfun-tts/bin/stepfun-tts
```

### Step 2: 配环境变量

```bash
export STEPFUN_API_KEY="your-stepfun-api-key"     # 必填 - StepFun 平台获取
export FFMPEG_PATH="/path/to/ffmpeg"               # 可选 - 如果 ffmpeg 不在 PATH 中
```

### Step 3: 配 OpenClaw TTS

编辑 `~/.openclaw/openclaw.json`:

```json5
{
  "messages": {
    "tts": {
      "auto": "always",          // "always" | "off" | "short"
      "provider": "openai",      // StepFun API 兼容 OpenAI 格式
      "openai": {
        "apiKey": "your-stepfun-api-key",
        "model": "step-tts-2",
        "voice": "yuanqishaonv",
        "baseUrl": "https://api.stepfun.com/v1"
      },
      "maxTextLength": 100       // ≤100字自动转语音
    }
  }
}
```

```bash
openclaw gateway restart
```

Done! 🎉

## 飞书应用权限要求

飞书开放平台 → 你的应用 → 权限管理，确保开启：

| 权限 | 说明 | 用途 |
|------|------|------|
| `im:resource` | 读取 IM 资源 | 基础权限 |
| **`im:resource:upload`** | 上传 IM 资源 | ⚠️ **上传语音文件必需** |
| `im:message:send_as_bot` | 以机器人身份发消息 | 发送语音消息 |

> ⚠️ 没有 `im:resource:upload` 权限会导致语音上传失败！

## 工作模式

### 模式 A: OpenClaw 集成（推荐）

配好 `openclaw.json` 后，OpenClaw 网关自动处理：
1. AI 回复 ≤ maxTextLength 字 → 自动调用 TTS
2. 生成 opus 文件 + 获取时长
3. 网关上传飞书 + 发送语音气泡

你什么都不用管。

### 模式 B: 独立调用

不依赖 OpenClaw，直接通过 stdin 调用：

```bash
# 仅生成音频
echo '{"text":"你好世界"}' | STEPFUN_API_KEY=xxx node bin/stepfun-tts
# → {"audioPath":"/tmp/.../stepfun-xxx.opus","format":"opus","duration":2}

# 生成 + 直接发飞书
echo '{
  "text": "你好世界",
  "feishu": {
    "chatId": "oc_xxx",
    "appId": "cli_xxx",
    "appSecret": "xxx"
  }
}' | STEPFUN_API_KEY=xxx node bin/stepfun-tts
# → {"audioPath":"...","format":"opus","duration":2,"feishu":{"file_key":"file_xxx","message_id":"om_xxx"}}
```

## 配置参考

### 环境变量

| 变量 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `STEPFUN_API_KEY` | ✅ | — | StepFun API 密钥 |
| `STEPFUN_BASE_URL` | ❌ | `https://api.stepfun.com/v1` | API 地址 |
| `STEPFUN_TTS_MODEL` | ❌ | `step-tts-2` | TTS 模型 |
| `STEPFUN_TTS_VOICE` | ❌ | `yuanqishaonv` | 默认音色 |
| `STEPFUN_TTS_FORMAT` | ❌ | `opus` | 输出格式 opus/mp3 |
| `STEPFUN_TIMEOUT_MS` | ❌ | `30000` | 超时毫秒 |
| `FFMPEG_PATH` | ❌ | 自动查找 | ffmpeg 二进制路径 |
| `FEISHU_APP_ID` | ❌ | — | 独立模式用 |
| `FEISHU_APP_SECRET` | ❌ | — | 独立模式用 |

### 可用音色

| Voice ID | 描述 |
|----------|------|
| `yuanqishaonv` | 元气少女 🎀 |
| `zhishidanv` | 知识女声 📚 |
| `wenrounvsheng` | 温柔女声 🌸 |
| `chengshunansheng` | 成熟男声 🎩 |
| `huoponvhai` | 活泼女孩 ✨ |

> 完整列表见 [StepFun 文档](https://platform.stepfun.com/)

## 依赖

- **Node.js** ≥ 16
- **ffmpeg**（Opus 转换 + 时长检测）
  ```bash
  # macOS
  brew install ffmpeg
  # Ubuntu
  sudo apt install ffmpeg
  # 或 npm
  npm install -g ffmpeg-static
  ```

## 常见问题

**Q: 语音和文字同时回复了？**
A: 这是 AI 行为问题，不是 skill 问题。AI 调用 `tts()` 后应回复 `NO_REPLY` 避免重复。

**Q: 上传飞书报权限错误？**
A: 确保飞书应用开启了 `im:resource:upload` 权限，并重新发布版本。

**Q: ffmpeg 找不到？**
A: 设置 `FFMPEG_PATH` 环境变量指向 ffmpeg 二进制文件完整路径。

**Q: 没有 ffmpeg 能用吗？**
A: 可以，会降级为 MP3 输出，但飞书语音气泡要求 Opus 格式，所以不装 ffmpeg 的话语音消息无法正常显示。

## License

MIT
