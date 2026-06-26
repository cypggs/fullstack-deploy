# User Credentials and API Keys

This document explains the credentials required by the `fullstack-deploy` skill.

## Setup

1. Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```
2. Fill in the real values for each service.
3. The skill will automatically load them via `source .env` before running commands.

> **Never commit `.env` or `.claude/settings.json` to Git.** They are already listed in `.gitignore`.

## GitHub

**Environment variable:** `GITHUB_TOKEN`

**Permissions required:** `repo` (full repository access), `workflow`

**How to get it:**
- Create a token at: https://github.com/settings/tokens/new
- Select scopes: `repo`, `workflow`.
- Copy the token into `.env` as `GITHUB_TOKEN`.

**Username:** The skill retrieves the username automatically from the token:
```bash
set -a && source .env && set +a
curl -s -H "Authorization: token $GITHUB_TOKEN" https://api.github.com/user | grep '"login"'
```

## Vercel

**Environment variables:** `VERCEL_TOKEN`, `VERCEL_TEAM_ID`

**How to get them:**
- Create a token at: https://vercel.com/account/tokens
- Copy the token into `.env` as `VERCEL_TOKEN`.
- Your Team ID is shown in the Vercel dashboard URL (`https://vercel.com/[team]/...`) or under team settings.
- If you are using a personal account (not a team), you can leave `VERCEL_TEAM_ID` empty.

## Supabase

This skill uses three different Supabase tokens. Each has a specific purpose and must be obtained from a different place in the Supabase dashboard.

### 1. `SUPABASE_URL` (Project URL)

**What it is:** The public URL of your Supabase project.

**How to get it:**
1. Open your project dashboard: https://supabase.com/dashboard/projects
2. Go to **Project Settings → API**.
3. Copy **Project URL** into `.env` as `SUPABASE_URL`.

### 2. `SUPABASE_ANON_KEY` (anon public)

**What it is:** The public key used by the frontend/browser Supabase client.

**Why it is still required:**
- The browser/client uses this key to read and write data through Row Level Security (RLS).
- It is safe to expose to the browser **only when RLS policies are properly configured**.
- Do **not** replace this with the `service_role` key in client-side code.

**How to get it:**
1. Open your project dashboard: https://supabase.com/dashboard/projects
2. Go to **Project Settings → API**.
3. Copy **anon public** key into `.env` as `SUPABASE_ANON_KEY`.

### 3. `SUPABASE_SERVICE_ROLE_KEY` (service_role)

**What it is:** A secret key that bypasses RLS for server-side/admin operations.

**Why it is required:**
- Used for server-side API routes, admin scripts, or any operation that needs to bypass RLS.
- Required when the skill needs to perform operations that the anon key cannot do.
- **Must never be exposed to the browser or frontend code.**

**How to get it:**
1. Open your project dashboard: https://supabase.com/dashboard/projects
2. Go to **Project Settings → API**.
3. Reveal and copy **service_role key** into `.env` as `SUPABASE_SERVICE_ROLE_KEY`.

### 4. `SUPABASE_MANAGEMENT_TOKEN` (Management API)

**What it is:** A token for the Supabase Management API, used to manage projects and execute SQL programmatically.

**Why it is required:**
- This skill uses it to automatically execute `database.sql` against your Supabase project.
- Without it, you would have to manually open the Supabase SQL Editor and paste the schema.
- It enables fully automated database setup and migrations.

**How to get it:**
1. Go to **Supabase Account Tokens**: https://supabase.com/dashboard/account/tokens
2. Click **Generate new token**.
3. Give it a name (e.g., `claude-fullstack-deploy`) and copy the token.
4. Paste it into `.env` as `SUPABASE_MANAGEMENT_TOKEN`.

**API endpoint used by this skill:**
```bash
POST https://api.supabase.com/v1/projects/{project-ref}/database/query
```
The skill sends the contents of `database.sql` to this endpoint to create tables, indexes, triggers, and RLS policies automatically.

## Security Notes

- Rotate tokens periodically.
- Do not share `.env` files or screenshots containing tokens.
- Use Row Level Security (RLS) policies in Supabase to protect data.
- The Supabase anon key is safe to use in client-side code when RLS is properly configured.
- The `service_role` key and `SUPABASE_MANAGEMENT_TOKEN` must stay server-side only.
