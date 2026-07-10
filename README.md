# Sainirnay Sanjee — Portfolio

A single-file, self-editable personal portfolio website. No build tools, no framework, no backend server required — just one HTML file that anyone can host anywhere.

**Live demo:** _[add your Vercel/Netlify URL here once deployed](https://sainirnay-sanjee.vercel.app/)_

---

## What this is

A personal portfolio site for an Electronics & Computer Science student, built with a navy-and-gold color scheme, smooth scroll animations, and a built-in **admin edit mode** — meaning the site owner can log in directly on the live page and edit almost everything (text, skills, projects, work experience, recommendations, and even the profile photo) without touching any code.

## Features

- Hero section with typed intro, photo upload, and quick stats
- Skills carousel with drag reorder, add/delete, and proficiency sliders
- Education timeline
- "Why Work With Me" bio panel
- Projects carousel with hidden links
- Work Experience carousel
- Recommendations carousel with star ratings
- Contact form (opens visitor's email client)
- GitHub & LinkedIn footer links
- Admin edit mode with password-protected login
- Responsive nav bar with smooth scroll

## Tech stack

- **HTML, CSS, JavaScript** — no framework, no build step, no npm install
- **Google Fonts** (Plus Jakarta Sans + Inter) — loaded via CDN link
- **Supabase** — free hosted Postgres database, used only to persist edits (so Save actually sticks around after a page refresh, for any visitor, not just the browser that made the edit)
- **Supabase JS client** — loaded via CDN script tag, no npm/bundler needed

Everything lives in **one file** (`index.html`): all CSS is in a single `<style>` block, all JavaScript in a single `<script>` block at the bottom. This makes the whole project trivially portable — copy the file anywhere and it works.

## How the admin edit mode works

1. Click **Login** in the nav bar
2. Enter the password
3. All editable fields (text, skill/project/work/recommendation cards, the profile photo) become directly clickable/editable on the page itself
4. Click **Save** — this bundles every editable piece of content into one JSON object and writes it to a Supabase table
5. Anyone who visits the page after that (including the owner, next time they open it) will see the saved version, since it's loaded from Supabase on page load

## Setting up persistence (Supabase)

The site will run and look correct without any setup — but the Save button won't actually persist anything until you connect a database. Steps:

1. Create a free project at [supabase.com](https://supabase.com)
2. In the **SQL Editor**, run:
   ```sql
   create table portfolio_content (
     key text primary key,
     value jsonb,
     updated_at timestamptz
   );

   alter table portfolio_content enable row level security;

   create policy "Public read" on portfolio_content
     for select using (true);

   create policy "Public write" on portfolio_content
     for all using (true) with check (true);
   ```
3. Go to **Settings → API** and copy your **Project URL** and **anon public key** (not the `service_role` key — that one must never be exposed in client-side code)
4. Paste both into the top of the `<script>` section in `index.html`:
   ```js
   const SUPABASE_URL = "https://your-project-ref.supabase.co";
   const SUPABASE_ANON_KEY = "your-anon-public-key";
   ```

Once these are filled in, Save/Load automatically switch from a temporary in-preview mode to writing/reading real, persistent data.

### About the profile photo

Uploaded photos are resized client-side (max 480px) and stored as a base64-encoded string inside the same saved JSON object — no separate file storage needed. This is simple and works well for one profile photo. If you later want to store multiple images (e.g. real project screenshots), consider switching to **Supabase Storage** instead, which stores actual files and keeps the database lighter.

## Hosting

Since it's a single static HTML file, it can be hosted almost anywhere for free:

1. Push `index.html` to a GitHub repository
2. Connect that repo to [Vercel](https://vercel.com) or [Netlify](https://netlify.com)
3. Deploy — no build settings needed, since there's no build step
4. You'll get a live URL immediately, and it redeploys automatically whenever you push changes to the repo

## Project structure

```
index.html   ← everything: markup, styles, and behavior in one file
README.md    ← this file
```

## Customization

- Colors are defined as CSS variables at the top of the `<style>` block (`--navy-900`, `--gold`, `--paper`, etc.) — change these to re-theme the whole site
- Fonts are set via `--font-display` and `--font-body`
- The admin password is the `ADMIN_PASSWORD` constant near the top of the `<script>` block
