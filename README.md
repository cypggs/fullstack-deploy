# fullstack-deploy

**一句话：想做一个网站？说出来，剩下的事情交给 Claude。**

> **English summary:** `fullstack-deploy` is a Claude Code skill that turns a single sentence into a live full-stack website. It handles requirements, Supabase database design, app development, GitHub repo creation, Vercel deployment, and hands you a public URL — all in one conversation. Zero cost to start.

---

## 这是什么？

这是一个**赛博活佛级别**的 Claude Code Skill。

一年前我刚学会写 Skill 的时候，创造了它。从那以后，我用它从零到一部署了**几十个网站**——从个人博客到带数据库、登录、后台管理的完整应用。现在我把它开源出来，任何人都可以免费使用。

它的核心能力很简单：

**你说一句话，它给你一个带域名的全栈网站。**

不需要买服务器，不需要配域名，不需要写 Docker，不需要熬夜看报错。Claude 会帮你完成：

1. 📋 需求分析 & 技术选型
2. 🗄️ Supabase 数据库设计（表、关系、RLS 权限）
3. 💻 应用开发（Next.js / Vue / Flask / FastAPI / Astro 等任意 Vercel 支持框架）
4. 🐙 GitHub 仓库创建 & 代码推送
5. 🚀 Vercel 生产环境部署
6. 🌐 给你一个可直接访问的 URL

**零成本起步，一句话生成线上网站。**

---

## 效果演示

你粘贴这样一句 prompt：

```text
/fullstack-deploy 我要做一个支持用户注册登录的待办事项应用，前端用 Next.js，数据库用 Supabase，部署到 Vercel。
```

然后 Claude 会：

- 创建一个 todo list 让你看到进度
- 自动设计 `users` 和 `todos` 表
- 生成 Next.js 项目代码
- 创建 GitHub 仓库并推送
- 部署到 Vercel 生产环境
- 最后回复你：

```text
🎉 部署成功！

🌐 生产环境：https://todo-app-xxxxx.vercel.app
🐙 GitHub 仓库：https://github.com/yourname/todo-app
⚙️ Vercel 控制台：https://vercel.com/yourname/todo-app
```

---

## 快速开始

### 1. 安装 Skill

Claude Code 的 Skill 通常放在 skills 目录。你可以直接克隆：

```bash
# macOS / Linux
mkdir -p ~/.claude/skills
cd ~/.claude/skills
git clone https://github.com/cypggs/fullstack-deploy.git

# 或者把目录软链过去
cd ~/.claude/skills
ln -s /path/to/fullstack-deploy fullstack-deploy
```

安装后重启 Claude Code，或在对话里使用：

```text
/fullstack-deploy 我要做一个 ...
```

### 2. 申请 API Key（点击直达）

| 服务 | 用途 | 直达链接 |
|------|------|----------|
| **GitHub** | 自动创建仓库、推送代码 | [→ 创建 Personal Access Token](https://github.com/settings/tokens/new) |
| **Vercel** | 部署网站、管理环境变量 | [→ 创建 Access Token](https://vercel.com/account/tokens) |
| **Supabase** | 数据库、用户认证、文件存储 | [→ 进入 Dashboard 创建项目](https://supabase.com/dashboard/projects) |

**权限说明：**
- GitHub Token 需要勾选 `repo` 和 `workflow`
- Vercel Token 复制后保存好，Team ID 可在 Vercel 控制台 URL 或 Team Settings 查看
- Supabase 的项目 URL 和 `anon public` key 在项目设置的 API 页面

### 3. 配置环境变量

```bash
cd ~/.claude/skills/fullstack-deploy
cp .env.example .env
```

编辑 `.env`，填入你刚申请的 key：

```env
# GitHub Personal Access Token (repo + workflow permissions)
# Create at: https://github.com/settings/tokens/new
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxx

# Vercel credentials
# Token: https://vercel.com/account/tokens
VERCEL_TOKEN=xxxxxxxxxx
VERCEL_TEAM_ID=team_xxxxxxxxxx        # 个人账号可留空

# Supabase project credentials
# Find these in your project dashboard: https://supabase.com/dashboard/projects
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_ANON_KEY=eyJ...
```

> **安全提示：** `.env` 已被 `.gitignore` 排除，永远不会被提交。

#### 高级用法：使用 `.claude/settings.json`

如果你不想每次手动 `source .env`，可以把变量写进 `.claude/settings.json`：

```json
{
  "env": {
    "GITHUB_TOKEN": "ghp_xxxxxxxxxxxxxxxxxxxx",
    "VERCEL_TOKEN": "xxxxxxxxxx",
    "VERCEL_TEAM_ID": "team_xxxxxxxxxx",
    "SUPABASE_URL": "https://xxxx.supabase.co",
    "SUPABASE_ANON_KEY": "eyJ..."
  }
}
```

这样 Claude 每次执行 Bash 时都会自动加载。注意 `.claude/settings.json` 也已在 `.gitignore` 中。

### 4. 运行

```text
/fullstack-deploy 我要做一个 [你的网站描述]，前端用 [框架]，数据库用 Supabase，部署到 Vercel。
```

English version:

```text
/fullstack-deploy Build me a [description] using [framework], Supabase for the database, and deploy it to Vercel.
```

然后就坐着看 Claude 表演。

---

## 支持的技术栈

| 类型 | 框架 |
|------|------|
| React 全栈 | **Next.js**（推荐） |
| 现代前端 | Vite, Vue, Svelte, SvelteKit |
| 静态站点 | Astro, Hugo, Jekyll, Gatsby |
| Python 后端 | Flask, FastAPI, Django |

详细配置参考 `references/frameworks.md`。

---

## 这个 Skill 的工作流程

```
用户一句话
    ↓
需求分析 + 技术选型
    ↓
Supabase 数据库设计（SQL schema）
    ↓
应用代码开发
    ↓
Git 初始化 → GitHub 仓库创建 → 推送
    ↓
Vercel 部署 + 环境变量配置
    ↓
🎉 返回生产环境 URL
```

整个流程 Claude 会用 TodoWrite 工具展示进度，你可以随时看到做到哪一步。

---

## 安全须知

- `.env` 和 `.claude/settings.json` 已加入 `.gitignore`，**永远不要提交到 GitHub**。
- GitHub Token 拥有仓库写入权限，请妥善保管，不要截图或外传。
- Supabase 的 `anon key` 是客户端可用的，**必须配合 RLS 策略**保护数据。
- 建议定期轮换 token。

---

## 常见问题

**Q: 真的完全免费吗？**
A: 是的。Vercel Hobby、Supabase Free、GitHub 免费账号就足够个人项目和小型应用使用。

**Q: 不会写代码能用吗？**
A: 能。你只需要描述清楚想要什么，Claude 会帮你写代码、部署、调试。

**Q: 部署失败怎么办？**
A: 常见原因：
1. 环境变量缺失 → Skill 会自动通过 Vercel API 添加
2. Vercel 访问保护 → Skill 会自动关闭 ssoProtection/passwordProtection
3. 构建报错 → 查看 Vercel 控制台日志，修复后重新 push

**Q: 可以绑定自己的域名吗？**
A: 可以。部署成功后，在 Vercel 控制台添加 Custom Domain 即可。

---

## 作者的话

这个 Skill 是我一年前刚学会写 Claude Code Skill 时创作的第一个作品。

那时候我只是想**省掉每次重复的配置工作**：建库、写代码、开仓库、配环境、部署……一套下来至少半天。有了它之后，很多项目从想法到上线只需要十几分钟。

现在我已经用它部署了几十个网站，从个人工具到完整产品。把它开源出来，希望更多人可以**零成本拥有自己的全栈网站**。

如果你用它做出了有趣的东西，欢迎来 [GitHub Issues](https://github.com/cypggs/fullstack-deploy/issues) 分享。

---

## 许可证

MIT — 自由使用、修改、分发。
