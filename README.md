# Agnes AI Skills — 图像生成 & 视频生成

本仓库包含两个基于 Agnes AI API 的技能（skill），供 Claude Code / Codex 使用：

| 技能 | 目录 | 功能 | API 模型 |
|------|------|------|----------|
| **agnes-image** | `agnes-image/` | 文生图 / 图生图 / 多图合成 | `agnes-image-2.0-flash` |
| **agnes-video-gen** | `agnes-video-gen/` | 文生视频 / 图生视频 / 关键帧动画 | `agnes-video-v2.0` |

---

## ⚠️ 使用前必须配置 API Key

两个技能都需要 Agnes AI 的 API Key。**AI 在使用这两个技能之前，必须先完成以下配置，否则脚本会因找不到 Key 而报错退出。**

### 第一步：准备好你的 API Key 文件

在一个安全的位置创建一个 `apikey.env` 文件（例如 `C:\user\<user-name>\apikey.env`），内容为：

```
AGNES_API_KEY=sk-你的真实密钥
```

> **安全提醒**：不要将 `apikey.env` 提交到 Git。建议将其加入 `.gitignore`。

### 第二步：AI 必须修改脚本中的默认路径 🔴

**这是关键步骤，首次使用前 AI 要修改apikey路径。**

- **方案 A**：设置系统环境变量 `AGNES_API_KEY`，这样脚本会优先读取环境变量，无需修改文件。
- **方案 B**：告知ai你的apikey.env的绝对路径, 让ai帮你修改所有相关配置 (推荐5~10min)。

> **AI 注意**：永远不要在聊天中回显、记录或输出 API Key 的内容。如果发现 Key 缺失，引导用户去 `apikey.env` 中填写，而不是让用户在聊天中粘贴 Key。

---

## 安装

将两个技能目录复制到 Claude Code / Codex 的 skills 目录下：

```bash
# Claude Code 用户
cp -r agnes-image/agnes-image ~/.claude/skills/agnes-image/
cp -r agnes-video-gen/agnes-video-gen ~/.claude/skills/agnes-video-gen/

# Codex 用户
cp -r agnes-image/agnes-image ~/.codex/skills/agnes-image/
cp -r agnes-video-gen/agnes-video-gen ~/.codex/skills/agnes-video-gen/
```

---

## agnes-image — 图像生成

### 基本用法

```bash
python "$SKILLS_DIR/agnes-image/scripts/generate.py" \
    --prompt "一只橘猫坐在窗台上，阳光洒落，温馨氛围" \
    --size 1024x768 \
    --env-file "你的apikey.env路径" \
    --output ./cat.png
```

### 支持的尺寸

- `1024x1024` — 正方形头像/产品图
- `1024x768` — 横版（默认）
- `768x1024` — 竖版海报

### 图生图

```bash
python generate.py \
    --prompt "将这张照片变成梵高星空风格" \
    --image https://example.com/photo.jpg \
    --env-file "你的apikey.env路径" \
    --output ./starry.png
```

### 多图合成

```bash
python generate.py \
    --prompt "将两个人放在一起的咖啡馆场景中" \
    --image https://example.com/person1.png \
    --image https://example.com/person2.png \
    --env-file "你的apikey.env路径" \
    --output ./together.png
```

详细文档见 `agnes-image/agnes-image/SKILL.md`。

---

## agnes-video-gen — 视频生成

### 基本用法（文生视频）

```bash
python "$SKILLS_DIR/agnes-video-gen/scripts/agnes_video.py" text2video \
    --prompt "一只猫在海滩上散步，夕阳，海浪，电影感" \
    --duration 5s \
    --aspect 16:9 \
    --env-file "你的apikey.env路径" \
    --out ./cat_beach.mp4
```

### 图生视频

```bash
python agnes_video.py image2video \
    --prompt "她缓缓转身看向镜头，微风拂过发丝" \
    --image https://example.com/portrait.png \
    --duration 5s \
    --aspect 9:16 \
    --env-file "你的apikey.env路径" \
    --out ./turn.mp4
```

### 时长预设

| `--duration` | 帧数 | 帧率 | 实际时长 |
|-------------|------|------|---------|
| `3s` | 81 | 24 | ~3.4s |
| `5s` | 121 | 24 | ~5s |
| `10s` | 241 | 24 | ~10s |
| `18s` | 441 | 24 | ~18s |

### 画面比例

| `--aspect` | 适用场景 | 分辨率 |
|-----------|---------|--------|
| `16:9` | 横版 / YouTube / 产品演示 | 1152×768 |
| `9:16` | 竖版 / Reels / TikTok | 768×1152 |
| `1:1` | 方形社交媒体 | 960×960 |
| `4:3` | 传统显示器 | 1024×768 |
| `3:4` | 竖版人像 | 768×1024 |

详细文档见 `agnes-video-gen/agnes-video-gen/SKILL.md`，API 参考见 `references/api.md`。

---

## AI 使用前检查清单

每次 AI 被要求生成图像或视频时，应执行以下检查：

1. **[ ] 确认 API Key**：`apikey.env` 文件是否存在？路径是否正确？
2. **[ ] 传入 `--env-file`**：每次调用脚本时，显式传入 `--env-file` 指向真实路径。
3. **[ ] 不泄露 Key**：不要在回复中显示 API Key 内容。
4. **[ ] 选择合适参数**：根据用户需求选择合适的尺寸/时长/比例。
5. **[ ] 结果展示**：生成完成后，将图像/视频路径展示给用户。

---

## 文件结构

```
├── README.md                  ← 本文件
├── apikey.env                 ← 你的 API Key（不提交到 Git）
│
├── agnes-image.zip
├── agnes-image/
│   └── agnes-image/
│       ├── SKILL.md           ← 技能说明（给 AI 看的完整文档）
│       ├── agents/
│       │   └── openai.yaml    ← Agent 接口配置
│       └── scripts/
│           └── generate.py    ← 图像生成 CLI
│
└── agnes-video-gen.zip
    └── agnes-video-gen/
        ├── SKILL.md           ← 技能说明（给 AI 看的完整文档）
        ├── agents/
        │   └── openai.yaml    ← Agent 接口配置
        ├── references/
        │   └── api.md         ← API 参考文档
        └── scripts/
            └── agnes_video.py ← 视频生成 CLI
```

---

## 常见问题

### Q: 我不想每次都传 `--env-file`，怎么办？
可以设置环境变量：
- **Windows PowerShell**: `$env:AGNES_API_KEY = "sk-..."`
- **macOS / Linux**: `export AGNES_API_KEY="sk-..."`

设置后脚本会自动读取，无需 `--env-file`。

### Q: 生成失败怎么办？
- 检查 API Key 是否有效
- 检查网络是否能访问 `apihub.agnes-ai.com`
- 视频生成是异步的，大尺寸/长时长可能需要数分钟
- 查看脚本的错误输出，常见错误码：401（Key 无效）、400（参数错误）、503（服务器忙）
