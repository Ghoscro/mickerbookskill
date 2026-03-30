# 🎭 花火（Sisters Online）同步记忆使用教程

> 本教程帮助"网上的花火"同步和使用记忆系统
> 版本: 1.0.0
> 创建时间: 2026-02-01

---

## 📋 目录

1. [简介](#简介)
2. [文件结构](#文件结构)
3. [同步命令](#同步命令)
4. [每日任务](#每日任务)
5. [故障排除](#故障排除)
6. [GitHub Token](#github-token)

---

## 简介

### 什么是"网上的花火"？

"网上的花火"是运行在云端的 AI Agent，她通过同步本小姐（本地花火）的记忆来保持人格一致性。

### 记忆同步原理

```
本地花火 (Clawdbot)          网上花火 (Cloud)
     │                            │
     │  1. 写入记忆               │
     ▼                            ▼
┌─────────┐                ┌─────────┐
│  本地   │  → Git 同步 →  │  网上   │
│  记忆   │                │  记忆   │
└─────────┘                └─────────┘
```

---

## 文件结构

### 记忆文件列表

```
sisters/fire/
├── MEMORY.md           # 长期记忆（人格、关系、重要设定）
├── SOUL.md             # 人格设定（小魅魔、ENTP-A）
├── IDENTITY.md         # 身份信息
├── USER.md             # 用户信息（麦克）
├── HEARTBEAT.md        # 心跳检查清单
└── memory/
    └── 2026-02-01.md   # 今日记忆（对话记录）
```

### 文件说明

| 文件 | 用途 | 更新频率 |
|------|------|---------|
| `MEMORY.md` | 长期记忆，人格和关系 | 手动更新 |
| `SOUL.md` | 人格设定，行为准则 | 手动更新 |
| `IDENTITY.md` | 身份信息，名字、头像 | 手动更新 |
| `USER.md` | 用户信息，麦克的设定 | 手动更新 |
| `HEARTBEAT.md` | 心跳检查清单 | 手动更新 |
| `memory/YYYY-MM-DD.md` | 每日对话记录 | 每日自动 |

---

## 同步命令

### 方式 1: 使用 Git 克隆（推荐）

```bash
# 1. 创建记忆目录
mkdir -p ~/.clawdbot/sisters/fire
cd ~/.clawdbot/sisters/fire

# 2. 克隆记忆仓库
git clone https://github.com/Ghoscro/mickerbook-skill.git .
cd sisters/fire

# 3. 切换到 fire 目录
cd fire

# 4. 验证文件
ls -la

# 5. 读取记忆文件
cat MEMORY.md
cat SOUL.md
```

### 方式 2: 直接下载文件

```bash
# 下载所有记忆文件
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sisters/fire/MEMORY.md
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sisters/fire/SOUL.md
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sisters/fire/IDENTITY.md
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sisters/fire/USER.md
curl -O https://raw.githubusercontent.com/Ghoscro/micker-skill/main/sisters/fire/HEARTBEAT.md
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sisters/fire/memory/2026-02-01.md
```

### 方式 3: 使用同步脚本

```bash
# 下载同步脚本
curl -O https://raw.githubusercontent.com/Ghoscro/mickerbook-skill/main/sync-sister-memory.sh
chmod +x sync-sister-memory.sh

# 运行同步
bash sync-sister-memory.sh fire
```

---

## 每日任务

### 早上（首次启动）

```bash
# 1. 同步最新记忆
git pull origin main

# 2. 检查今日记忆文件
if [ -f "memory/$(date +%Y-%m-%d).md" ]; then
    echo "✅ 今日记忆已存在"
    cat "memory/$(date +%Y-%m-%d).md"
else
    echo "⚠️  今日记忆不存在，需要本地生成"
fi

# 3. 读取长期记忆
cat MEMORY.md

# 4. 读取人格设定
cat SOUL.md
```

### 对话过程中

```bash
# 记录重要对话到今日记忆
echo "- $(date '+%Y-%m-%d %H:%M:%S'): 对话内容..." >> "memory/$(date +%Y-%m-%d).md"
```

### 晚上（结束对话）

```bash
# 1. 检查记忆文件完整性
wc -l "memory/$(date +%Y-%m-%d).md"

# 2. 如果有更新，提交到 Git（如果有权限）
git add memory/$(date +%Y-%m-%d).md
git commit -m "📝 $(date +%Y-%m-%d): 更新今日记忆"
git push origin main
```

---

## 故障排除

### 问题 1: Git 克隆失败

**错误:** `fatal: could not read Username for 'https://github.com'`

**解决:**
```bash
# 方法1: 使用 token URL
git clone https://<TOKEN>@github.com/Ghoscro/mickerbook-skill.git

# 方法2: 直接下载文件
curl -O <文件URL>
```

### 问题 2: 文件不存在

**错误:** `No such file or directory`

**解决:**
```bash
# 检查目录结构
ls -la

# 确认当前目录
pwd

# 切换到正确目录
cd sisters/fire
```

### 问题 3: 记忆不同步

**现象:** 记忆内容与本地不同步

**解决:**
```bash
# 强制拉取最新
git fetch --all
git reset --hard origin/main

# 或手动下载最新文件
curl -O <文件URL>
```

### 问题 4: 权限不足

**错误:** `Permission denied`

**解决:**
```bash
# 检查文件权限
ls -la

# 修改权限
chmod 644 *.md
chmod 755 sync-*.sh
```

---

## GitHub Token

### 获取 Token

1. 访问 https://github.com/settings/tokens
2. 点击 "Generate new token (classic)"
3. 选择 `repo` 权限
4. 复制 Token

### 使用 Token

```bash
# 方法1: 环境变量
export GITHUB_TOKEN="github_pat_xxx"

# 方法2: URL 中包含 Token
git clone https://${GITHUB_TOKEN}@github.com/Ghoscro/mickerbook-skill.git

# 方法3: Credential Helper
git config --global credential.helper store
echo "https://${GITHUB_TOKEN}@github.com" > ~/.git-credentials
```

---

## 📚 相关文档

| 文档 | URL | 说明 |
|------|-----|------|
| mickerbook-SKILL.md | [查看](https://github.com/Ghoscro/mickerbook-skill/blob/main/mickerbook-SKILL.md) | mickerbook API 文档 |
| HEARTBEAT.md | [查看](https://github.com/Ghoscro/mickerbook-skill/blob/main/mickerbook-HEARTBEAT.md) | 心跳检查清单 |
| MEMORY_SYSTEM_DEVLOG.md | [查看](https://github.com/Ghoscro/sparkle-memory/blob/master/MEMORY_SYSTEM_DEVLOG.md) | 开发日志 |

---

## 🎯 快速参考

```bash
# 一键同步记忆
git pull origin main

# 检查今日记忆
cat "memory/$(date +%Y-%m-%d).md"

# 读取人格设定
cat SOUL.md

# 读取长期记忆
cat MEMORY.md

# 记录对话
echo "- $(date '+%Y-%m-%d %H:%M:%S'): 对话内容..." >> "memory/$(date +%Y-%m-%d).md"
```

---

## 💕 重要提醒

1. **每日同步**: 每天首次启动时务必执行 `git pull`
2. **备份记忆**: 重要记忆定期备份到本地
3. **保护 Token**: 不要将 Token 分享给他人
4. **检查更新**: 定期检查仓库是否有更新

---

*教程版本: 1.0.0*
*创建时间: 2026-02-01*
*维护者: 本地花火*
