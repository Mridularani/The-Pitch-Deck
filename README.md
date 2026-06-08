# The Story Bank

A shared research tool for your show team — track news items, links, and videos worth discussing, with priority scores, flags, and category filters.

---

## Setup (one-time, ~10 minutes)

### 1. Create your Supabase project

1. Go to [supabase.com](https://supabase.com) and create a new project
2. Once it's ready, go to **SQL Editor** and run this:

```sql
create table story_items (
  id           bigserial primary key,
  title        text not null,
  url          text,
  type         text,
  category     text,
  priority     int default 3,
  podcast_worthy boolean default false,
  hot          boolean default false,
  needs_research boolean default false,
  covered      boolean default false,
  assigned_to  text,
  notes        text,
  created_at   timestamptz default now()
);

-- Allow anonymous read/write (safe for a team with a shared password)
alter table story_items enable row level security;

create policy "Allow all for anon" on story_items
  for all using (true) with check (true);
```

3. Go to **Project Settings → API** and copy:
   - **Project URL** (looks like `https://xxxx.supabase.co`)
   - **anon public** key (long string under "Project API keys")

---

### 2. Configure the app

Open `index.html` and find the CONFIG block near the top:

```js
const CONFIG = {
  supabaseUrl:  'YOUR_SUPABASE_URL',   // ← paste Project URL here
  supabaseKey:  'YOUR_SUPABASE_ANON_KEY', // ← paste anon key here
  appPassword:  'YOUR_TEAM_PASSWORD',  // ← choose a shared password
  sessionHours: 8,                     // ← how long login lasts
};
```

Replace the three `YOUR_...` values. That's all the code you need to touch.

---

### 3. Deploy to GitHub Pages

```bash
# Create a new repo on github.com, then:
git init
git add index.html README.md
git commit -m "initial"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

Then in your GitHub repo:
- Go to **Settings → Pages**
- Source: **Deploy from branch**
- Branch: `main` / `/ (root)`
- Click **Save**

Your app will be live at `https://YOUR_USERNAME.github.io/YOUR_REPO/` within a minute.

Share that link with your team along with the password.

---

## How to use

| Action | How |
|---|---|
| Add item | Click **+ Add item** or press `Cmd/Ctrl+K` |
| Fetch title | Paste a URL → click **↻ Fetch title** |
| Edit item | Click the ✎ pencil icon |
| Delete item | Click the ✕ icon |
| Filter | Use the pill buttons (category / type / flag) |
| Search | Type in the search box (searches title, category, notes) |
| Toggle view | List ↔ Cards button top right |
| Export | Click **↓ Export** to download a JSON backup |

---

## Data model

Each item stores:

| Field | Description |
|---|---|
| `title` | Story headline |
| `url` | Source link |
| `type` | Article / Video / Tweet / Other |
| `category` | One of 7 presets or custom |
| `priority` | 1–5 stars |
| `podcast_worthy` | Flag |
| `hot` | Flag — time-sensitive |
| `needs_research` | Flag |
| `covered` | Flag — already done |
| `assigned_to` | Free-text name |
| `notes` | Story angle / research notes |

---

## Updating the app

Just edit `index.html` locally and push:

```bash
git add index.html
git commit -m "update"
git push
```

GitHub Pages redeploys automatically.

---

## Next steps (optional upgrades)

- **Per-user login** — swap the password check for Supabase Auth (email magic link)
- **Real-time cursors** — see who's viewing what using Supabase Realtime presence
- **Episode tagging** — add an `episode` column to group items by segment
- **Slack notifications** — use Supabase webhooks to post new items to your team Slack
