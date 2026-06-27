# fullstack-deploy

**一句话：想做一个网站？说出来，剩下的事情交给 Claude。**

> **English summary:** `fullstack-deploy` is a Claude Code skill that turns a single sentence into a live full-stack website. It handles requirements, Supabase database design, app development, GitHub repo creation, Vercel deployment, and hands you a public URL — all in one conversation. Zero cost to start.

https://fullstack-deploy.openclihub.com/
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
6. 🌐 给你一个可直接访问的 URL (Vercel国内可能无法直接访问，不过域名解析到自己的就可以了)

**零成本起步，一句话生成线上网站。**

更关键的是，这背后用的全是**赛博菩萨级**服务——免费额度对独立开发者极其友好：

| 服务 | 免费额度亮点 | 适合场景 |
|------|-------------|----------|
| **Vercel** | Hobby 版免费部署、自动 HTTPS、全球 CDN | 前端托管、Serverless Functions |
| **Supabase** | Free 版 500MB 数据库、无限 API 请求、内置 Auth & Storage | 数据库、认证、文件存储 |
| **GitHub** | 免费私有/公开仓库、Actions CI/CD | 代码托管、自动构建 |

对个人项目、MVP、独立开发产品来说，这个组合**完全免费**就能跑很久。只有流量或数据量真正做大后才需要付费升级。

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
| **Supabase 项目** | 数据库 URL、anon key、service_role key | [→ 进入 Dashboard 创建项目](https://supabase.com/dashboard/projects) |
| **Supabase 账号** | Management API Token（自动执行 SQL 必备） | [→ 创建 Account Token](https://supabase.com/dashboard/account/tokens) |

**权限说明：**
- GitHub Token 需要勾选 `repo` 和 `workflow`
- Vercel Token 复制后保存好，Team ID 可在 Vercel 控制台 URL 或 Team Settings 查看
- Supabase 的项目 URL、`anon public` key、`service_role` key 都在 **Project Settings → API** 页面
- Supabase Management API Token 在 **Account → Access Tokens** 页面创建，用于自动执行 `database.sql`

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
# Project URL / anon key / service_role key: https://supabase.com/dashboard/project/_/settings/api
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_ANON_KEY=eyJ...               # 前端/浏览器客户端使用
SUPABASE_SERVICE_ROLE_KEY=eyJ...       # 服务端使用，切勿暴露到浏览器

# Supabase Management API Token（自动执行 SQL 必备）
# Create at: https://supabase.com/dashboard/account/tokens
SUPABASE_MANAGEMENT_TOKEN=sbp_...
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
    "SUPABASE_ANON_KEY": "eyJ...",
    "SUPABASE_SERVICE_ROLE_KEY": "eyJ...",
    "SUPABASE_MANAGEMENT_TOKEN": "sbp_..."
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

## 给其他 AI Agent 使用

`fullstack-deploy` 不是 npm 包，所以**不支持 `npx skills add`**。它的本质是一份结构化指令（`SKILL.md`）+ 自动化脚本，任何支持 system prompt / skills / rules 的 AI Agent 都可以接入。

### 通用 Agent Prompt

把下面这段 prompt 复制到你的 Agent 的 system prompt、rules 或 skills 描述里，Agent 就会按 `fullstack-deploy` 的流程工作：

```text
You are running the fullstack-deploy skill. Your job is to turn a user request into a live full-stack website in one conversation.

When the user says anything like "build an app", "create a website", "deploy my project", "full-stack application", or uses "/fullstack-deploy", follow this pipeline:

1. Requirements & Planning
   - Ask clarifying questions if the idea is vague.
   - Recommend a framework (Next.js for full-stack React, Flask/FastAPI for Python, Astro for content sites, etc.).
   - Design the Supabase schema: tables, relationships, RLS policies, storage buckets.

2. Database Setup
   - Write a complete database.sql file.
   - Use SUPABASE_MANAGEMENT_TOKEN to execute it automatically via the Supabase Management API when available.
   - If no management token, provide the SQL and ask the user to run it in Supabase SQL Editor.

3. Application Development
   - Initialize the project in a subdirectory (e.g., site/) when the current directory already contains skill files.
   - Install dependencies and implement features: database client, API routes, UI components, auth, uploads if needed.
   - Test locally with npm run dev or equivalent.

4. Git & GitHub
   - git init, create .gitignore, commit, create a GitHub repo with GITHUB_TOKEN, push to main.

5. Vercel Deployment
   - Deploy with vercel --token $VERCEL_TOKEN --yes --prod.
   - Add environment variables via the Vercel API.
   - Disable ssoProtection and passwordProtection so the site is public.
   - Redeploy with --force if needed.

6. Completion
   - Verify the production URL returns 200.
   - Provide the production URL, GitHub repo URL, and Vercel dashboard URL.
   - Create README / CLAUDE.md documentation.

Rules:
- Always load credentials from .env first with: set -a && source .env && set +a
- Never commit .env or .claude/settings.json.
- Use TodoWrite or equivalent to track progress across the 6 phases.
- Prefer automated API calls; fall back to manual steps only when tokens are missing.
```

### 各主流 Agent 接入方式

| Agent | 接入方式 |
|-------|----------|
| **Claude Code** | 克隆到 `~/.claude/skills/fullstack-deploy`，对话中输入 `/fullstack-deploy 我要做...` |
| **Cursor** | 把上面「通用 Agent Prompt」贴到项目的 `.cursorrules` 文件，或在 Cursor Settings → Rules 里粘贴 |
| **OpenAI Codex / CLI** | 把 prompt 作为 `--system-prompt` 或写入 `codex.md`（如果支持），运行前确保 `.env` 已加载 |
| **OpenClaw** | 把 `SKILL.md` 放到 agent 的 skills/rules 目录，或在 system prompt 中引用本仓库 |
| **Hermes Agent** | 在配置文件的 `system_prompt` 或 `skills` 字段粘贴「通用 Agent Prompt」 |
| **Pi Agent** | 把 prompt 加到 Pi 的 agent rules / memory 中，或每次对话开头粘贴 |

> 提示：如果你的 Agent 支持读取本地文件，可以直接让它先读取 `SKILL.md` 和 `references/*.md`，这样比纯 prompt 更完整。

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

## 进阶玩法

### 🌐 绑定自己的域名 + Cloudflare 自动解析

Vercel 默认会分配一个 `xxx.vercel.app` 的二级域名，但如果你有自己的域名，强烈建议搭配 **Cloudflare** 使用：

- **免费 DNS 解析**：Cloudflare 免费版就支持自动/批量 DNS 管理
- **自动 HTTPS**：Vercel 会自动为自定义域名申请并续期 SSL 证书
- **全球 CDN 加速**：Cloudflare 的 CDN + Vercel Edge Network 双重加速
- **域名安全保护**：隐藏源站、DDoS 防护、Page Rules 重定向

**绑定步骤：**
1. 在 Vercel 项目控制台进入 **Settings → Domains**，添加你的域名（如 `app.yourdomain.com`）。
2. Vercel 会给出需要添加的 DNS 记录（CNAME 或 A 记录）。
3. 去 Cloudflare 的 DNS 面板添加对应记录，**关闭代理**（灰色云朵）让 Vercel 验证通过。
4. 验证成功后，可以重新开启 Cloudflare 代理（橙色云朵）享受 CDN 和安全功能。

### 🔔 增加 Webhook 通知

想让部署成功/失败后自动收到通知？可以在以下环节接入 Webhook：

- **Vercel Deploy Hooks**：在 Vercel 项目设置中配置 Deploy Hook URL，外部触发重新部署
- **GitHub Actions Webhooks**：监听 push、pull_request 事件，推送到 Slack/钉钉/飞书
- **Supabase Database Webhooks**：数据库表发生变更时触发 HTTP 请求，实现实时通知或联动其他服务
- **Cloudflare Workers**：在自定义域名层面做访问统计、A/B 测试、边缘逻辑

推荐组合：
```
GitHub push → Vercel 自动构建 → 部署成功 → Webhook → Slack/飞书群通知
```

### 🛠️ 持续定制你的 Skill

这个 Skill 只是起点。建议你把它当成自己的**部署脚手架**，不断按自己的需求魔改：

- **加用户系统**：Supabase Auth + 登录/注册页面
- **加后台管理**：基于 RLS 的角色权限、管理员面板
- **加文件上传**：Supabase Storage + 图片/文档管理
- **加支付**：Stripe / Lemon Squeezy 集成
- **加国际化**：多语言支持
- **加 AI 能力**：接入 Claude / OpenAI API
- **换技术栈**：Vue、Svelte、Flask、FastAPI、Astro 都可以

它的本质是一组自动化脚本 + 最佳实践。你越用它，越应该把它改成**完全符合自己习惯的工作流**。

---

## 安全须知

- `.env` 和 `.claude/settings.json` 已加入 `.gitignore`，**永远不要提交到 GitHub**。
- GitHub Token 拥有仓库写入权限，请妥善保管，不要截图或外传。
- Supabase 的 `anon key` 是客户端可用的，**必须配合 RLS 策略**保护数据。
- Supabase 的 `service_role key` 和 `SUPABASE_MANAGEMENT_TOKEN` 权限很高，**只能留在服务端 `.env` 中**，绝不能写入前端代码或暴露到浏览器。
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
A: 可以。部署成功后，在 Vercel 控制台添加 Custom Domain 即可。更推荐使用 **Cloudflare** 做 DNS 解析和 CDN 加速，具体步骤见上方的「绑定自己的域名 + Cloudflare 自动解析」。

---

## 作者的话

这个 Skill 是我一年前刚学会写 Claude Code Skill 时创作的第一个作品。

那时候我只是想**省掉每次重复的配置工作**：建库、写代码、开仓库、配环境、部署……一套下来至少半天。有了它之后，很多项目从想法到上线只需要十几分钟。

现在我已经用它部署了几十个网站，从个人工具到完整产品。把它开源出来，希望更多人可以**零成本拥有自己的全栈网站**。

**这个 Skill 的最佳用法是：先用它跑通第一个项目，然后 fork 一份改成你自己的专属部署工作流。** 每个人的技术栈和习惯不同，把它调制成最适合自己的形状，才是赛博菩萨们的真正用法。

如果你用它做出了有趣的东西，或者改出了更酷的版本，欢迎来 [GitHub Issues](https://github.com/cypggs/fullstack-deploy/issues) 分享，也欢迎 PR。

---
<img width="1206" height="2622" alt="be8c26910340c516ba7299c0402d53c1" src="https://github.com/user-attachments/assets/1c1d9afc-f49c-4c90-b337-3ebab15fcd46" />


## 许可证

MIT — 自由使用、修改、分发。
