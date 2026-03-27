---
name: git-commit-message
description: 当用户需要你提交git记录的时候，生成符合 Conventional Commits 的中文 commit 信息
---


## 概述
此技能用于生成 git 的 commit 记录，严格按照以下流程进行

## 流程

### 步骤 1：获取变更概览
```bash
git diff --staged --stat
git diff --stat
```

### 步骤 2：智能选择性读取
根据 --stat 结果判断：

  - 核心代码文件（.py/.ts/.js/.go 等）：读取完整 diff，使用 -U2 减少上下文
  - 配置文件（.json/.yaml/.toml）：读取完整 diff
  - 锁文件（*.lock, package-lock.json）：跳过，仅记录"依赖更新"
  - 生成文件/缓存（dist/, .cache/, *.min.js）：跳过
  - 大文件（单文件 >200 行变更）：只读 --stat，根据文件名推断

  示例：只读核心文件的 diff
  ```bash
  git diff --staged -U2 -- "*.py" "*.ts" ":!*.lock"
  ```

### 步骤 3：生成 commit 信息

格式如下：

```md
type(scope): subject

  body

type（英文）：feat/fix/docs/style/refactor/perf/test/chore
scope（可选，英文/拼音）：如 api, ui, auth
subject（中文，≤20字）：简述主要变更
body（中文）：详细说明变更内容和原因，多个要点用列表
```

示例：

```md
feat(auth): 添加用户登录功能

  - 实现基于 JWT 的认证系统
  - 添加登录接口和令牌验证中间件
  - 更新依赖
```
注意事项
  - 优先分析 staged 变更（已 git add 的）
  - 如果没有 staged，再看 unstaged
  - 直接输出 commit 信息，无需额外说明