# YouSustain in Mombasa: countdown site

A one-page site for participants: a countdown to **3 October 2026, 00:00 East Africa Time**, each visitor's local time for that moment, the logistical note as a PDF download, the cultural night brief, and a shared wall where people post how they're feeling.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole site (styles and script included) |
| `logo.png` | YouSustain logo |
| `logistics.pdf` | Participant & Coordinator Logistical Note (download button) |

## Put it online with GitHub Pages

1. Upload the three files to the root of the repository.
2. Go to **Settings → Pages**, set **Source** to *Deploy from a branch*, choose `main` and `/ (root)`, then save.
3. After a minute the site is live at `https://<your-username>.github.io/<repo-name>/`.

## Make the wall shared (Supabase, free)

GitHub Pages only serves files, so the wall needs a small database to let everyone see each other's posts. Until you connect one, each visitor only sees their own posts.

1. Create a free project at [supabase.com](https://supabase.com).
2. Open **SQL Editor** and run:

```sql
create table public.wall_posts (
  id uuid primary key default gen_random_uuid(),
  name text not null check (char_length(name) between 1 and 40),
  country text check (char_length(country) <= 40),
  mood text check (char_length(mood) <= 20),
  message text not null check (char_length(message) between 1 and 280),
  volunteer boolean not null default false,
  created_at timestamptz not null default now()
);

alter table public.wall_posts enable row level security;

create policy "Anyone can read the wall"
  on public.wall_posts for select to anon using (true);

create policy "Anyone can post to the wall"
  on public.wall_posts for insert to anon with check (true);
```

3. In **Project Settings → API**, copy the **Project URL** and the **anon public** key.
4. In `index.html`, near the top of the `<script>`, fill them in:

```js
const SUPABASE_URL = "https://xxxx.supabase.co";
const SUPABASE_ANON_KEY = "eyJhbGciOi...";
```

5. Commit. The wall now refreshes every 15 seconds for everyone.

To remove an unwanted post, delete its row in Supabase's **Table Editor**.
