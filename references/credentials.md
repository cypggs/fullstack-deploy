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

**Environment variables:** `SUPABASE_URL`, `SUPABASE_ANON_KEY`

**How to get them:**
- Open your project dashboard: https://supabase.com/dashboard/projects
- Go to **Project Settings → API**.
- Copy **Project URL** into `.env` as `SUPABASE_URL`.
- Copy **anon public** key into `.env` as `SUPABASE_ANON_KEY`.

## Security Notes

- Rotate tokens periodically.
- Do not share `.env` files or screenshots containing tokens.
- Use Row Level Security (RLS) policies in Supabase to protect data.
- The Supabase anon key is safe to use in client-side code when RLS is properly configured.
