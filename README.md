# 🎬 Marquee — TV Show Tracker

A personal, multi-user TV show tracker that runs entirely as a single static HTML file. Hosted for free on GitHub Pages, backed by Supabase (database + authentication), and powered by the TMDB API for all show data.

No backend server, no build step, no framework — just one `index.html` file.

---

## ✨ Features

### Accounts & Access
- **Multi-user accounts** via Supabase Auth — sign up with email + password, or sign in passwordlessly with a magic link
- **Invite-only friendly** — designed for you + a handful of people you invite, not open public signup (see [Setup](#-setup) for how to lock this down)
- **Row Level Security** — every user can only ever see and modify their own shows, progress, and alerts; this is enforced at the database level, not just in the app
- **Set/change password from inside the app** — no email link required once you're logged in (Settings → Update Password); useful if you signed in via magic link and want a password for next time, or if reset emails aren't arriving
- **Session persistence** — stays signed in across browser restarts until you explicitly sign out

### Finding & Adding Shows
- **Live search** powered by TMDB's full show database (100k+ titles), with debounced search-as-you-type and poster thumbnails in the results dropdown
- **Add to any list** directly from search results or from a show's detail panel
- **Show suggestions** — every show's detail panel includes a "You Might Also Like" row of similar titles, one click away from adding

### Organizing Your Shows
- **Three status lists**: Watching, Watchlist, and Completed
- **Grid or List view** — toggle between a poster-forward grid and a denser list view, per tab
- **Watching tab sub-views**:
  - **All Shows** — everything you're currently watching
  - **Unwatched Episodes** — filtered down to only shows that have aired episodes you haven't checked off yet, with an "N new" badge
- Move a show between lists, or remove it entirely, from its detail panel

### Episode Tracking
- **Per-episode watched/unwatched checkboxes**, organized into collapsible season accordions
- **Season progress bars and checkmarks** — see at a glance how far into a season you are, with a green checkmark once it's fully watched
- **"Mark All" / "Unmark All"** button per season — check off (or revert) every aired episode in one click
- **Hide/Unhide Watched Seasons** toggle — collapse fully-completed seasons out of view so you can focus on what's left, with a count of how many are hidden
- **Seasons stay expanded** while you check off episodes — toggling an episode no longer collapses the season you're working in
- Future/unaired episodes are visually dimmed and not clickable until they actually air
- Progress badges on show cards reflect real watched/total counts (using TMDB's aired-episode data), not just static metadata

### Episode Calendar
- **Full month-grid calendar** showing every episode airing across *all* your tracked shows (Watching, Watchlist, and Completed)
- Up to 3 episode chips per day, with a "+N more" indicator on busy days
- Click any episode chip to jump straight to that show's detail panel
- **Previous/Next month navigation**, plus a "Today" button to snap back to the current month
- Past episodes are styled differently from upcoming ones at a glance

### Alerts & Notifications
- **Dismissible notification feed** for one-time events, including:
  - 📅 A show you're tracking just got a confirmed air date for its next episode (when it previously didn't have one)
  - 🆕 A new season has been added to a show
  - 🔄 A show's production status changed (e.g. confirmed renewed, or back into production)
  - 🚫 A show has been cancelled
  - 🏁 A show has ended
  - 🎬 A new season premieres today
- **Dismiss individual alerts** or **dismiss an entire section at once**
- **Flashing notification dot** on the Alerts tab in the top bar whenever there's something new and undismissed
- **Live status sections** (always current, not dismissible) showing:
  - Upcoming episodes/seasons across your watching list, with a countdown ("3d", "2w", "Today!")
  - Currently cancelled or ended shows
  - Currently returning shows

### Show Details
- Full detail panel for every show: backdrop art, poster, overview, genres, rating, season/episode counts, and air status
- **Where to Watch** — streaming provider logos (Netflix, Hulu, etc.) pulled live from TMDB for US availability
- **Cancellation/ending notices** shown prominently when relevant
- **Next episode air date** highlighted when a new episode is coming up

### Design
- Dark, cinema-inspired interface with warm amber accents, custom typography (DM Serif Display + Space Grotesk), and poster-forward card layouts
- Fully responsive — usable on both desktop and mobile browsers

---

## 🛠 Tech Stack

| Layer      | Technology                   |
|------------|-------------------------------|
| Hosting    | GitHub Pages (static)         |
| Auth       | Supabase Auth                 |
| Database   | Supabase (PostgreSQL + RLS)   |
| Show Data  | TMDB API                      |
| Frontend   | Vanilla HTML / CSS / JavaScript — no frameworks, no build step |
| Fonts      | Google Fonts (DM Serif Display, Space Grotesk) |

---

## 📦 File Structure

```
index.html          ← The entire app (HTML + CSS + JS) — credentials hard-coded near the top
supabase-setup.sql  ← Run once in the Supabase SQL Editor (safe to re-run any time)
README.md           ← This file
```

---

## 🚀 Setup

### 1. Create your Supabase project

1. Create a free project at [supabase.com](https://supabase.com)
2. Open the **SQL Editor** and run the entire contents of `supabase-setup.sql`. This creates all four tables (`tracked_shows`, `episode_progress`, `alerts`, `show_snapshots`), sets up per-user unique constraints, and enables Row Level Security so each signed-in user only ever sees their own rows.
3. From **Settings → API**, copy your **Project URL** and **anon/public key**.

### 2. Lock down auth (invite-only)

This app is meant for you and a small number of people you invite — not the general public:

1. Go to **Authentication → Settings** (sometimes under **Authentication → Providers → Email**)
2. Turn **off** "Allow new users to sign up"
3. To invite someone later, go to **Authentication → Users → Invite user** and enter their email

> Tip: while you're first setting things up, it's easiest to leave signups **on**, sign yourself up once through the app's "Sign Up" tab, then turn signups off afterward.

You can also control whether email confirmation is required before a new account can log in, under **Authentication → Settings → Email Auth → Confirm email**.

**Important — Site URL:** under **Authentication → URL Configuration**, set the **Site URL** (and add to **Redirect URLs**) to your actual GitHub Pages address, e.g. `https://yourusername.github.io/your-repo/`. If this is left as the default `localhost:3000`, magic links and confirmation emails will redirect to a dead address and show an "expired or invalid" error.

### 3. Get a TMDB API token

1. Create a free account at [themoviedb.org](https://www.themoviedb.org)
2. Go to **Settings → API** and request an API key
3. Copy the **API Read Access Token** (the long Bearer-style token, not the short v3 API key)

### 4. Hard-code your credentials

Open `index.html` and find this block near the top of the `<script>` section:

```js
const SUPABASE_URL = 'https://YOUR-PROJECT.supabase.co';
const SUPABASE_KEY = 'YOUR-SUPABASE-ANON-KEY';
const TMDB_TOKEN = 'YOUR-TMDB-READ-ACCESS-TOKEN';
```

Replace the three placeholders with your real values and save.

> **Is it safe to commit these to a public repo?** Yes. The Supabase anon key and TMDB token are both designed to be used in client-side code and carry no special privileges by themselves. Your data is protected by the Row Level Security policies in `supabase-setup.sql`, not by keeping these values secret — the anon key only lets someone talk to your project as an *unauthenticated* visitor; they still can't read or write anything without signing in as a real user, and even then, only their own rows.

### 5. Deploy to GitHub Pages

1. Create a new GitHub repository (public or private both work)
2. Upload `index.html` (and `supabase-setup.sql` / this `README.md`, if you'd like) to the root
3. Go to **Settings → Pages → Source → Deploy from branch → main**
4. Your app will be live at `https://yourusername.github.io/your-repo/`

### 6. First sign-in

Open your GitHub Pages URL and either:
- **Sign Up** with an email + password (if signups are still open), or
- Accept a Supabase invite sent to your email, or
- Use **Send Magic Link Instead** to sign in passwordlessly

---

## 🔁 Migrating from an older single-user version

If you used an earlier version of this app before accounts existed, your old `tracked_shows` / `episode_progress` rows have no owner and will be invisible once RLS is on. `supabase-setup.sql` includes a commented-out section at the bottom with `UPDATE ... SET user_id = '...'` statements — sign in once first so your account exists, find your user ID with `SELECT id, email FROM auth.users;`, then run those updates to claim your old data.

---

## 💡 Ideas for Future Customization

- **Background alerts while the app is closed** — would require a scheduled Supabase Edge Function to poll TMDB independently of anyone having the page open
- **Shared/family watchlists** — a new table and adjusted RLS policies to let specific other users view (not just you)
- **CSV import** — for migrating watch history from another tracker
