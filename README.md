# 🎬 Marquee — TV & Movie Tracker

A personal, multi-user TV show and movie tracker built as a single static HTML file. Hosted free on GitHub Pages, backed by Supabase (auth + database + cache), powered by TMDB for media data, and delivering a daily email digest via Resend.

No backend server. No build step. No framework. Just `index.html`.

---

## ✨ Feature Highlights

### 📺 TV & 🎬 Movies
A top-level mode toggle switches between TV and Movies. Each has its own lists, search, detail panels, and stats. Feed, Calendar, Discover, and Notifications are shared.

### 📚 My Shows — Unified Library
All TV shows in one tab with seven smart filter chips:

| Filter | Logic |
|---|---|
| **All** | Every tracked show |
| **Unwatched** | Has aired episodes you haven't checked off (strictly date-based) |
| **Upcoming** | Caught up + confirmed future air date |
| **Filming** | TMDB status = In Production |
| **Ended** | TMDB status = Ended or Cancelled |
| **Completed** | Personally finished (auto-moved) |
| **Watchlist** | Not started yet (visually separated) |

### 🎬 Episode Tracking
- Horizontal season pill selector — auto-selects first unwatched season
- Watched seasons hidden by default, revealed on demand
- Watched episodes hidden by default within each season
- Episode summaries — tap ▾ to expand TMDB plot summaries inline
- Watch history timestamps on each episode
- Mark All / Unmark per season
- Auto-complete — marking the last aired episode of an Ended/Cancelled show moves it to Completed automatically

### ▶ Up Next Widget
Surfaces the earliest unwatched aired episode per show. One-tap "✓ Mark as Watched" advances to the next episode automatically.

### 🔔 Notification Panel
Bell (🔔) in the topbar opens a dropdown panel — no navigation required. Shows all undismissed notifications with poster, message, and timestamp. Dismiss individually or all at once. Panel stays open until you click outside or press Escape. Pulses red when notifications are waiting.

### 📋 Activity Feed
Chronological log of all activity with two widgets at the top:
- **🗓 On This Day** — activity from previous years on today's date
- **📅 Coming Up** — episodes airing within 30 days, sorted soonest first

### 📊 Stats
Episodes watched, hours, days, average rating, binge streaks, watching pace chart (12 weeks), most active day of week, genre breakdown, top shows. All Time / This Year toggle. Movie stats in Movies mode.

### 🔭 Discover
10 TMDB curated charts (TV + Movies) plus 📡 New Season Radar (upcoming premieres from your tracked shows). Genre filter chips on results. Desktop dropdown; mobile inline drawer sub-menu.

### ⭐ Ratings Panel
Tap the TMDB rating in any detail panel to open a slide-out showing Rotten Tomatoes, Metacritic, IMDb, and TMDB scores with direct links.

### 🌓 Light / Dark / Auto Theme
Cycles between Auto (7 AM–8 PM light, 8 PM–7 AM dark), ☀️ Light, and 🌙 Dark. Saved to localStorage per device. No flash on load.

### 📺 Streaming & Plex
Provider logos in every list row (US flatrate only, lazy loaded). Manual Plex flag per show — shows a gold P badge in list rows and appears in digest emails.

### 📧 Daily Email Digest
One clean white email at 6 AM Eastern. Smart summary chips, Airing Today section (with network + Plex info), Show Updates grouped by type. Always sent — quiet days get "All quiet today."

### 💾 Smart Show Cache
TMDB metadata cached in Supabase (`show_cache` table). Lists load instantly from one query instead of 160+ TMDB requests. Cache refreshes daily via the digest job. Batched TMDB fallback (10 at a time) eliminates rate-limit dropouts.

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Hosting | GitHub Pages (static, free) |
| Auth | Supabase Auth (email/password + magic link) |
| Database | Supabase PostgreSQL + Row Level Security |
| Cache | Supabase `show_cache` table |
| Media Data | TMDB API |
| Ratings | OMDb API (free tier) |
| Email | Resend |
| Background Jobs | Supabase Edge Function + pg_cron |
| Frontend | Vanilla HTML / CSS / JavaScript |
| Fonts | DM Serif Display + Space Grotesk (Google Fonts) |

---

## 📦 File Structure

```
index.html                                  ← Entire app (~7,000 lines)
help.html                                   ← Help content (fetched into slide-out panel)
supabase-setup.sql                          ← Full DB migration (safe to re-run)
supabase/functions/notify-users/index.ts   ← Edge Function (detect + digest modes)
README.md                                   ← This file
```

---

## 🚀 Setup

### 1. Supabase Project

1. Create a free project at [supabase.com](https://supabase.com)
2. In **SQL Editor**, run the entire `supabase-setup.sql` — creates all 8 tables, indexes, and RLS policies
3. From **Settings → API**, copy your **Project URL** and **anon/public key**

Tables created: `tracked_shows`, `episode_progress`, `alerts`, `show_snapshots`, `activity_log`, `tracked_movies`, `user_preferences`, `show_cache`

### 2. Lock Down Signups (Invite-Only)

1. **Authentication → Settings → Email Auth** — disable "Allow new users to sign up"
2. Invite users via **Authentication → Users → Invite user**

> Tip: leave signups on while setting up, sign yourself up, then disable.

**Important — Site URL:** under **Authentication → URL Configuration**, set Site URL and Redirect URLs to your GitHub Pages address. Magic links won't work without this.

### 3. TMDB API Token

1. Create a free account at [themoviedb.org](https://www.themoviedb.org)
2. **Settings → API** → request an API key
3. Copy the **API Read Access Token** (long Bearer token, not the short v3 key)

### 4. OMDb API Key (for Ratings)

1. Get a free key at [omdbapi.com/apikey.aspx](https://www.omdbapi.com/apikey.aspx)
2. **Activate it** via the email OMDb sends you (key won't work until activated)

### 5. Hard-Code Credentials in `index.html`

Find this block near the top of `<script>`:

```js
const SUPABASE_URL = 'https://your-project.supabase.co';
const SUPABASE_KEY = 'your-supabase-anon-key';
const TMDB_TOKEN   = 'your-tmdb-read-access-token';
const OMDB_KEY     = 'your-omdb-key';
```

> **Safe to commit publicly?** Yes. The anon key and TMDB token are client-side by design. RLS policies mean the anon key alone can't read or write anything without a valid authenticated session.

### 6. Deploy to GitHub Pages

1. Create a GitHub repo, upload `index.html` and `help.html` to the root
2. **Settings → Pages → Source → Deploy from branch → main**
3. Live at `https://yourusername.github.io/your-repo/`

### 7. First Sign-In

Open your GitHub Pages URL. Sign up with email + password or use the magic link option.

---

## 📧 Email Digest Setup

### 1. Resend

1. Create a free account at [resend.com](https://resend.com)
2. **Domains** → add and verify your domain
3. **API Keys** → create a key (starts with `re_`)
4. Choose a FROM address at your verified domain

### 2. Deploy the Edge Function

1. Supabase → **Edge Functions** → Create new function named `notify-users`
2. Paste the contents of `supabase/functions/notify-users/index.ts`
3. Deploy

### 3. Set Edge Function Secrets

In **Settings → Edge Functions → Secrets**:

| Secret | Value |
|---|---|
| `SERVICE_ROLE_KEY` | Supabase **Settings → API → service_role** key |
| `TMDB_TOKEN` | Your TMDB Read Access Token |
| `RESEND_API_KEY` | Your `re_...` Resend key |
| `FROM_EMAIL` | e.g. `marquee@yourdomain.com` |
| `APP_URL` | Your GitHub Pages URL |

> Use `SERVICE_ROLE_KEY` not `SUPABASE_SERVICE_ROLE_KEY` — Supabase reserves the `SUPABASE_` prefix.

### 4. Enable Extensions and Schedule Cron Jobs

In **Database → Extensions**, enable **pg_cron** and **pg_net**. Then in **SQL Editor**:

```sql
-- Alert detection every 6 hours
SELECT cron.schedule('marquee-detect', '0 */6 * * *',
  $$ SELECT net.http_post(
    url     := 'https://YOUR-PROJECT.supabase.co/functions/v1/notify-users',
    headers := '{"Authorization": "Bearer YOUR_ANON_KEY"}'::jsonb,
    body    := '{"mode":"detect"}'::jsonb
  ) $$);

-- Daily digest at 6 AM Eastern (11 AM UTC)
SELECT cron.schedule('marquee-digest', '0 11 * * *',
  $$ SELECT net.http_post(
    url     := 'https://YOUR-PROJECT.supabase.co/functions/v1/notify-users',
    headers := '{"Authorization": "Bearer YOUR_ANON_KEY"}'::jsonb,
    body    := '{"mode":"digest"}'::jsonb
  ) $$);
```

Verify: `SELECT * FROM cron.job;`

### 5. Test the Edge Function

In **Edge Functions → notify-users → Invoke** with body `{"mode":"detect"}`:
```json
{ "shows_checked": 0, "new_alerts": 0 }
```
Zero alerts on first run is expected — it seeds baseline snapshots. Test digest with `{"mode":"digest"}`.

---

## 🗄 Database Schema

| Table | Purpose |
|---|---|
| `tracked_shows` | TV shows per user with status + Plex flag |
| `episode_progress` | Per-episode watched state with timestamps |
| `alerts` | Dismissible notifications (cancelled, renewed, etc.) |
| `show_snapshots` | TMDB state snapshots for change detection |
| `activity_log` | Chronological feed of all user actions |
| `tracked_movies` | Movies per user with status + Plex flag |
| `user_preferences` | Timezone per user |
| `show_cache` | TMDB metadata cache for instant list loading |

All tables have Row Level Security enforced — users can only access their own rows.

---

## 🔁 Migrating from Single-User Version

If you used this app before auth was added, old rows have no `user_id`. Sign in once, find your UUID:

```sql
SELECT id, email FROM auth.users;
```

Then claim your old rows:

```sql
UPDATE tracked_shows    SET user_id = 'YOUR-UUID' WHERE user_id IS NULL;
UPDATE episode_progress SET user_id = 'YOUR-UUID' WHERE user_id IS NULL;
UPDATE alerts           SET user_id = 'YOUR-UUID' WHERE user_id IS NULL;
UPDATE show_snapshots   SET user_id = 'YOUR-UUID' WHERE user_id IS NULL;
```

---

## 📱 Mobile

Marquee is fully responsive. On mobile the tab bar is replaced by a slide-out drawer — open with ☰ or by swiping right from the left edge. Add to your iPhone home screen via Safari → Share → Add to Home Screen for an app-like experience.

---

## 🔐 Security Notes

- Supabase anon key and TMDB token are safe to commit — they're client-side by design
- All data access enforced by PostgreSQL Row Level Security
- OMDb key is free tier — no billing risk
- `SERVICE_ROLE_KEY` is only in Edge Function secrets, never in client code
