# Supported Frameworks and Configurations

This document lists all frameworks supported by Vercel and provides specific configuration guidance for each.

## JavaScript/TypeScript Frameworks

### Next.js (Recommended for React)
- **Build Command:** `next build`
- **Dev Command:** `next dev`
- **Output Directory:** `.next` (default)
- **Environment Variables:** `NEXT_PUBLIC_*` for client-side
- **Best For:** Full-stack React applications, SSR, API routes
- **Vercel Integration:** First-class support, automatic detection

### Vite (Vue.js, React, Svelte)
- **Build Command:** `npm run build` or `vite build`
- **Dev Command:** `npm run dev` or `vite`
- **Output Directory:** `dist`
- **Best For:** Fast development, modern bundling
- **Vercel Detection:** Automatic via package.json

### Vue.js (Vue CLI or Nuxt)
- **Build Command:** `npm run build`
- **Dev Command:** `npm run serve` (Vue CLI) or `npm run dev` (Nuxt)
- **Output Directory:** `dist` (Vue CLI) or `.output` (Nuxt)
- **Environment Variables:** `VUE_APP_*` for client-side
- **Best For:** Progressive web apps, interactive UIs

### SvelteKit
- **Build Command:** `npm run build`
- **Dev Command:** `npm run dev`
- **Output Directory:** `.svelte-kit` or `build`
- **Best For:** Lightweight, reactive applications

### Astro
- **Build Command:** `astro build`
- **Dev Command:** `astro dev`
- **Output Directory:** `dist`
- **Best For:** Content-focused sites, static generation

## Python Frameworks

### Flask
- **Entry Point:** `app.py` or `main.py`
- **Requirements:** `requirements.txt` or `Pipfile`
- **WSGI Server:** Gunicorn (recommended for production)
- **Vercel Configuration:**
  ```json
  {
    "builds": [{ "src": "app.py", "use": "@vercel/python" }],
    "routes": [{ "src": "/(.*)", "dest": "app.py" }]
  }
  ```
- **Best For:** Simple APIs, microservices

### FastAPI
- **Entry Point:** `main.py`
- **Requirements:** `requirements.txt` with `fastapi` and `uvicorn`
- **ASGI Server:** Uvicorn
- **Vercel Configuration:**
  ```json
  {
    "builds": [{ "src": "main.py", "use": "@vercel/python" }],
    "routes": [{ "src": "/(.*)", "dest": "main.py" }]
  }
  ```
- **Best For:** High-performance APIs, async operations
- **Note:** Automatic API documentation at `/docs`

### Django
- **Entry Point:** `wsgi.py`
- **Requirements:** `requirements.txt` with `django`
- **Static Files:** Must configure `STATIC_ROOT` and `collectstatic`
- **Vercel Configuration:** Requires custom `vercel.json`
- **Best For:** Complex web applications with admin interface

## Static Site Generators

### Hugo
- **Build Command:** `hugo`
- **Output Directory:** `public`

### Jekyll
- **Build Command:** `jekyll build`
- **Output Directory:** `_site`

### Gatsby
- **Build Command:** `gatsby build`
- **Output Directory:** `public`

## Framework-Specific Environment Variables

### Next.js
```env
NEXT_PUBLIC_SUPABASE_URL=...        # Client-side accessible
NEXT_PUBLIC_SUPABASE_ANON_KEY=...   # Client-side accessible
DATABASE_URL=...                     # Server-side only
```

### Vue.js (Vue CLI)
```env
VUE_APP_SUPABASE_URL=...
VUE_APP_SUPABASE_ANON_KEY=...
```

### Flask/FastAPI
```env
SUPABASE_URL=...
SUPABASE_KEY=...
DATABASE_URL=...
```

### Vite (all frameworks)
```env
VITE_SUPABASE_URL=...
VITE_SUPABASE_ANON_KEY=...
```

## Choosing the Right Framework

**For the user's needs:**
- **Full-stack app with database:** Next.js + Supabase
- **Fast API backend:** FastAPI + Supabase
- **Simple API:** Flask + Supabase
- **Interactive frontend:** Vue.js/Svelte + Supabase
- **Content-heavy site:** Astro/Hugo + Supabase (for CMS)

## Vercel Build Configuration

All frameworks should include a `.gitignore` file that excludes:
```
node_modules/
.next/
dist/
.env.local
.vercel
__pycache__/
*.pyc
```

For Python projects, create `vercel.json`:
```json
{
  "builds": [
    {
      "src": "main.py",
      "use": "@vercel/python"
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "main.py"
    }
  ]
}
```

For static exports (Next.js):
```json
{
  "buildCommand": "next build && next export",
  "outputDirectory": "out"
}
```
