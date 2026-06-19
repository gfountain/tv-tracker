# 🎬 Marquee — TV & Movie Tracker

A personal, multi-user TV show and movie tracker built as a single static HTML file. Hosted for free on GitHub Pages, backed by Supabase (authentication + database), and powered by the TMDB API for all media data.

No backend server. No build step. No framework. Just one `index.html` file.

---

## ✨ Features

### 📺 TV Mode & 🎬 Movie Mode

A **top-level toggle** in the topbar switches the entire app between TV Shows and Movies. Each mode has its own tab bar, search context, and detail view — with shared infrastructure like the Calendar, Stats, and Activity Feed.

---

### 🔐 Accounts & Auth

- Sign in with **email + password** or a **passwordless magic link** — both options on the same login screen
- **Multi-user** — each person only ever sees their own data, enforced at the database level via Supabase Row Level Security (not just in the UI)
- **Invite-only friendly** — built for a small group; open signups can be disabled in the Supabase dashboard
- **Set or change your password from inside the app** — useful if you signed in via magic link and want a password for next time, or if email delivery isn't working. Go to ⚙️ Settings → Update Password
- **Session persistence** — stays signed in across browser restarts until you explicitly sign out (🚪 button in the topbar)
- Your email address is displayed in the topbar so you always know which account you're using

---

### 📺 TV Shows

#### Finding & Adding Shows
- **Live search** as you type — powered by TMDB's full database of 100k+ TV shows, with poster thumbnails and ratings in the dropdown
- Add a show to any list directly from search results or from its detail panel
- The search bar automatically switches to TV show search when you're in TV mode

#### Organizing Your Shows
- **Three status lists**: Watching, Watchlist, and Completed
- **Grid view** (poster cards) or **List view** (compact rows) — toggle between them per tab
- **Watching tab sub-views**:
  - **Unwatched Episodes** (default) — only shows that have aired episodes you haven't checked off yet, with a badge showing how many are unwatched
  - **All Shows** — your full Watching list
- **Sort** your list by: Recently Added, Name (A–Z), Next Air Date, or TMDB Rating
- **Filter by genre** — genre chips appear dynamically based on what genres are actually in your list
- Move a show between lists or remove it entirely from its detail panel

#### Episode Tracking
- **Collapsible season accordions** — each season expands to show every episode with an aired/upcoming indicator
- **Per-episode watched checkboxes** — click any episode to toggle it watched/unwatched; the season stays expanded while you check things off
- **Season progress bars and checkmarks** — at a glance see how far into each season you are; a green ✓ when it's fully done
- **"Mark All / Unmark All"** per season — check off or reset an entire season in one click
- **Hide Watched Seasons** (on by default) — fully-watched seasons are hidden automatically so you can focus on what's left; click "👁 Show Watched Seasons" to reveal them
- Future/unaired episodes are visually dimmed and not clickable
- Progress counts on show cards use real aired-episode data, not just total episode counts

#### Show Details
- Full-screen slide-out panel with backdrop art, poster, title, overview, genres, TMDB rating, air status, season/episode counts
- **Next episode air date** highlighted when something is coming up soon
- **Cancelled/ended notices** shown prominently inline
- **▶ Trailer embed** — official YouTube trailer auto-selected (falls back to any available trailer)
- **📺 Where to Watch** — US streaming provider logos pulled live from TMDB (Netflix, Hulu, HBO, etc.)
- **🎭 Cast** — horizontally scrolling row with circular headshots, actor names, and character names (uses TMDB's aggregate credits for accurate recurring-cast data)
- **🎬 Crew** — creators, executive producers, and showrunners as chips
- **🎬 You Might Also Like** — a row of similar shows, each clickable to open their detail panel
- **Status action buttons** — move between Watching/Watchlist/Completed or remove, all from the detail panel

---

### 🎬 Movies

#### Finding & Adding Movies
- **Live movie search** — powered by TMDB, with poster, year, and runtime in results
- Add directly from search results to either To Watch or Watched
- The search bar automatically switches to movie search when you're in Movies mode

#### Organizing Your Movies
- **Two status lists**: To Watch and Watched
- Same **grid/list view toggle** and **sort options** as TV (Recently Added, Name, Rating, Release Date)
- **Filter by genre** — same dynamic chip system as TV
- Watched movies show a green **"✓ Watched"** stamp on their card, plus the date you marked them watched

#### Movie Details
- Same slide-out panel as TV shows: backdrop, poster, overview, genres, rating
- **Runtime** displayed prominently (e.g. "2h 28m")
- **"Mark as Watched"** / **"↩ Unmark"** toggle button — prominently placed at the top of the info section
- Date you watched it, shown below the toggle
- Trailer embed, streaming providers, cast, crew chips (Director, Screenplay, etc.), and similar movies
- Add to To Watch or directly to Watched from any untracked movie's panel

---

### 📆 Episode & Release Calendar

- **Full month grid** showing every upcoming and past episode across **all** your tracked TV shows (Watching, Watchlist, and Completed)
- **Movie release dates** from your To Watch list also appear on the calendar, prefixed with 🎬 so you can tell them apart from TV episodes
- Up to 3 items per day cell, with a **"+N more"** indicator when there are more
- Click any chip to jump straight to that show or movie's detail panel
- **‹ Previous / Next ›** month navigation and a **Today** button
- Past items are visually muted; upcoming ones are highlighted in amber
- The calendar caches fetched data per month so switching months is fast after the first load

---

### 🔔 Alerts & Notifications

#### In-App Dismissible Notifications
A "🔔 Notifications" section at the top of the Alerts tab — persisted events that fire once when something changes:

| Icon | Type | What triggers it |
|------|------|-----------------|
| 📅 | Season date announced | A show had no confirmed air date, now it does |
| 🆕 | New season added | TMDB's season count for a show increased |
| 🔄 | Status changed | Show flipped from In Production → Returning Series, or vice versa |
| 🚫 | Cancelled | Show status changed to Canceled |
| 🏁 | Ended | Show status changed to Ended |
| 🎬 | Premieres today | The next episode is S_x_E1 and airs today |

- **Dismiss individual alerts** with the ✕ button on each row
- **"Dismiss all"** link to clear an entire section at once
- **Flashing red dot** on the Alerts tab button when there are undismissed notifications
- Dismissals are stored in Supabase so they stay dismissed across devices and reloads

#### Live Status Sections
Always-current sections that reflect real-time TMDB data (not dismissible — they disappear when no longer true):
- **📅 Upcoming Episodes/Seasons** — shows with a confirmed next-episode date in the next 30 days, with countdowns ("3d", "2w", "Today!")
- **🚫 Cancelled or Ended** — any tracked show currently in Canceled or Ended status
- **✅ Currently Returning** — any tracked show in Returning Series status

#### Background Email Notifications (via Supabase Edge Function + Resend)
A daily automated job that runs independently — even when no one has the app open:
- Checks TMDB for every tracked show across all users
- Detects the same changes as the in-app alert system
- Sends a beautifully formatted dark-mode email digest to each affected user
- Emails are sent via [Resend](https://resend.com) from your own verified domain
- Runs on a cron schedule set in Supabase (default: daily at 9am UTC)

---

### ▶ Up Next Widget

- Appears at the top of the **Watching** tab
- Shows the **next unwatched aired episode** for each show you're actively watching — so you always know exactly where to pick up
- Displays the show's backdrop image, show name, season/episode number, and episode title
- Updates automatically when you check off an episode, mark a season, add a show, or move something into Watching
- Clicking any card opens that show's detail panel directly

---

### 📋 Activity Feed

A dedicated **Feed tab** showing a chronological log of everything you and your invited users do in the app:

**TV activity:**
- ➕ Added a show to your list
- ✓ Watched an episode (S_x_E_y_)
- 🏆 Finished an entire season
- 🔄 Moved a show between lists
- 🎉 Completed a series
- 🗑️ Removed a show

**Movie activity:**
- 🎬 Added a movie to To Watch or Watched
- 🍿 Marked a movie as watched
- 🗑️ Removed a movie

Each feed item shows the show/movie poster, a readable description, and a relative timestamp ("2h ago", "3d ago"). Clicking any item opens its detail panel. The feed supports **"Load more"** pagination for long histories.

---

### 📊 Stats

#### TV Stats
- Total shows tracked
- Total episodes watched
- Total hours watched
- Total days watched (24-hour equivalent)
- Average TMDB rating across your tracked shows
- Shows completed
- **Genre breakdown** — horizontal bar chart showing which genres you've watched the most episodes of, built from your actual watch history
- **Most-Watched Shows** — top 5 ranked by episodes watched, each clickable to their detail panel
- **Biggest Commitment** — the show with the most total episodes in your library

#### Movie Stats (in Movies mode)
- Total movies tracked
- Movies watched
- Movies on your To Watch list
- Total hours of movies watched
- Average TMDB rating
- **Favorite genres** — bar chart built from your watched movies

---

### ⚙️ Settings

- View your signed-in email address
- **Set or change your password** without needing an email link — enter a new password directly while logged in (minimum 6 characters, with confirmation field)
- Enter key submits the form for quick keyboard use

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| Hosting | GitHub Pages (static) |
| Auth | Supabase Auth (email/password + magic link) |
| Database | Supabase (PostgreSQL + Row Level Security) |
| Media Data | TMDB API (shows, movies, episodes, trailers, cast, streaming) |
| Email | Resend (background notification emails) |
| Notifications | Supabase Edge Function + pg_cron (daily cron job) |
| Frontend | Vanilla HTML / CSS / JavaScript — no frameworks, no build step |
| Fonts | Google Fonts — DM Serif Display + Space Grotesk |

---

## 📦 File Structure

```
index.html                                    ← The entire app — credentials near the top of <script>
supabase-setup.sql                            ← Run once in Supabase SQL Editor (safe to re-run)
supabase/functions/notify-users/index.ts      ← Daily email notification Edge Function
README.md                                     ← This file
```

---

## 🚀 Setup

### 1. Supabase project

1. Create a free project at [supabase.com](https://supabase.com)
2. In the **SQL Editor**, run the entire contents of `supabase-setup.sql` — this creates all five tables (`tracked_shows`, `episode_progress`, `alerts`, `show_snapshots`, `activity_log`, `tracked_movies`), per-user unique constraints, indexes, and RLS policies
3. From **Settings → API**, copy your **Project URL** (bare URL, no `/rest/v1/` suffix) and **anon/public key**

### 2. Lock down signups (invite-only)

1. **Authentication → Settings → Email Auth** — turn off "Allow new users to sign up"
2. To invite someone: **Authentication → Users → Invite user**

> Tip: leave signups on while you're first setting up, sign yourself up, then turn it off.

**Important — Site URL:** under **Authentication → URL Configuration**, set the **Site URL** and **Redirect URLs** to your GitHub Pages address (e.g. `https://yourusername.github.io/your-repo/`). If left as the default `localhost:3000`, magic links will redirect to a dead address.

### 3. TMDB API token

1. Create a free account at [themoviedb.org](https://www.themoviedb.org)
2. Go to **Settings → API** and request an API key
3. Copy the **API Read Access Token** (the long Bearer-style token, not the short v3 key)

### 4. Hard-code credentials in `index.html`

Find this block near the top of the `<script>` section and replace the placeholders:

```js
const SUPABASE_URL = 'https://your-project.supabase.co';
const SUPABASE_KEY = 'your-supabase-anon-key';
const TMDB_TOKEN  = 'your-tmdb-read-access-token';
```

> **Safe to commit publicly?** Yes. The anon key and TMDB token are both designed for client-side use. Your data is protected by Supabase RLS policies — the anon key alone can't read or write anything without a valid authenticated session.

### 5. Deploy to GitHub Pages

1. Create a GitHub repo and upload `index.html` (and optionally the other files) to the root
2. **Settings → Pages → Source → Deploy from branch → main**
3. Your app is live at `https://yourusername.github.io/your-repo/`

### 6. First sign-in

Open your GitHub Pages URL and sign up with email + password, or use **Send Magic Link Instead** to sign in passwordlessly.

---

## 📧 Background Email Notifications Setup

### 1. Set up Resend

1. Create a free account at [resend.com](https://resend.com)
2. Go to **Domains** and add your custom domain (e.g. `tvtracker.yourdomain.com`)
3. Add the DNS records Resend provides to your domain registrar (Namecheap, etc.)
4. Once verified, go to **API Keys** and create a key — it starts with `re_`
5. Choose a FROM address at your verified domain (e.g. `marquee@tvtracker.yourdomain.com`)

### 2. Deploy the Edge Function

1. In Supabase, go to **Edge Functions** and click **Create a new function**
2. Name it exactly: `notify-users`
3. Delete the default code and paste the entire contents of `supabase/functions/notify-users/index.ts`
4. Deploy it

### 3. Set Edge Function secrets

In **Settings → Edge Functions** (or Secrets), add:

| Secret | Value |
|---|---|
| `RESEND_API_KEY` | Your `re_...` Resend API key |
| `SERVICE_ROLE_KEY` | Supabase **Settings → API → service_role** key (the secret one) |
| `TMDB_TOKEN` | Your TMDB Read Access Token |
| `FROM_EMAIL` | e.g. `marquee@tvtracker.yourdomain.com` |
| `APP_URL` | Your GitHub Pages URL |

> Note: use `SERVICE_ROLE_KEY` (not `SUPABASE_SERVICE_ROLE_KEY`) — Supabase reserves the `SUPABASE_` prefix for its own built-in variables.

### 4. Enable extensions and schedule the cron

In **Database → Extensions**, enable **pg_cron** and **pg_net**. Then in the **SQL Editor**:

```sql
select cron.schedule(
  'daily-marquee-notifications',
  '0 14 * * *',   -- 9am Eastern (14:00 UTC). Adjust to your preference.
  $$
  select net.http_post(
    url := 'https://your-project.supabase.co/functions/v1/notify-users',
    headers := '{"Authorization": "Bearer YOUR_ANON_KEY"}'::jsonb
  )
  $$
);
```

Verify it's scheduled: `select * from cron.job;`

### 5. Test it

In **Edge Functions → notify-users**, use the Invoke/Test tool (POST, empty body). You should see:
```json
{ "shows_checked": 5, "new_alerts": 0, "emails_attempted": 0, "results": [] }
```
Zero alerts on first run is expected — it seeds the baseline snapshots. Future runs only email when something actually changes.

---

## 🔁 Migrating from an older single-user version

If you used an earlier version of this app before accounts were added, your old rows have no `user_id`. The `supabase-setup.sql` file includes a commented-out section at the bottom with `UPDATE ... SET user_id = '...'` statements. Sign in once first, find your UUID with `SELECT id, email FROM auth.users;`, then run those updates.

---

## 💡 Possible Future Features

- **Personal ratings & notes** per show or movie
- **"Rewatch" mode** — track a second viewing without losing original history
- **SMS notifications** via Twilio (in addition to email)
- **Shared/household watchlists** — let invited users co-edit a list
- **Import from Trakt or IMDb** — bring your existing history over
- **Binge streak & watching-pace stats** — episodes per week chart, longest streak
- **Background movie alerts** — "releasing today," "now streaming" notifications for your To Watch movies
