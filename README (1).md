# Rate Your Albums

A personal album rating web app — search, rate, and track every album you've ever heard.

Live at: [wsgun.github.io/rateyouralbums](https://wsgun.github.io/rateyouralbums)

---

## Features

- **Album search** powered by MusicBrainz with Cover Art Archive
- **0–100 rating system** with score grades (S / A / B / C / D / F / Perfect)
- **Comments** on each album
- **Home feed** with genre-based curation — Today's Picks, Pop, Hip-Hop, R&B, Electronic, Jazz, Classical, Rock, K-Pop, Latin, Folk
- **My Ratings** tab with stats overview, score distribution chart, and sortable grid
- **User accounts** — username + password signup, no email required
- **Cross-device sync** via Supabase backend
- **Social features** — follow users, view their ratings and comments, see who else rated the same album
- **Multilingual** — English, Korean (한국어), Portuguese (Português)
- **PWA** — installable on mobile as a home screen app

## Stack

| Layer | Tech |
|---|---|
| Frontend | Vanilla HTML/CSS/JS |
| Hosting | GitHub Pages |
| Database | Supabase (PostgreSQL) |
| Music data | MusicBrainz API |
| Cover art | Cover Art Archive |

## Database Schema

```sql
-- Users
create table profiles (
  id uuid primary key default gen_random_uuid(),
  username text unique not null,
  password_hash text not null,
  password_hint text default '',
  created_at timestamp default now()
);

-- Ratings
create table ratings (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references profiles(id) on delete cascade,
  album_key text not null,       -- MusicBrainz release-group ID
  album_name text not null,
  album_artist text not null,
  album_cover text,
  score integer check (score >= 0 and score <= 100),
  comment text default '',
  saved_at bigint not null,
  unique(user_id, album_key)
);

-- Follows
create table follows (
  follower_id uuid references profiles(id) on delete cascade,
  following_id uuid references profiles(id) on delete cascade,
  created_at timestamp default now(),
  primary key (follower_id, following_id)
);
```

## Local Development

No build step needed. Just open `index.html` in a browser.

For Supabase to work locally, the project URL and API key are already embedded in the source. Note that the free Supabase plan pauses after 2 weeks of inactivity — restore it from the Supabase dashboard if the app stops loading data.

## Notes

- Passwords are hashed client-side with SHA-256 before being stored
- No email required for signup — password recovery relies on an optional hint set at registration
- MusicBrainz has a rate limit of 1 request/second; cover art loads progressively to stay within limits
