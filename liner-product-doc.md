# Liner — Product Document
*A personal canvas for music memories*

> **Convention going forward:** Every major decision, design, research finding, and build artifact from our sessions goes into this document. Update it as we go.

---

## Table of Contents

1. [The Idea](#1-the-idea)
2. [Why Music](#2-why-music)
3. [Competitive Landscape](#3-competitive-landscape)
4. [What Makes Liner Different](#4-what-makes-liner-different)
5. [Product Architecture](#5-product-architecture)
6. [User Types](#6-user-types)
7. [Core Primitives](#7-core-primitives)
8. [Privacy Model](#8-privacy-model)
9. [Features Designed So Far](#9-features-designed-so-far)
10. [Lyrics API Strategy](#10-lyrics-api-strategy)
11. [Spotify API Capabilities](#11-spotify-api-capabilities)
12. [Tech Stack — MVP](#12-tech-stack--mvp)
13. [MVP Scope](#13-mvp-scope)
14. [Open Questions](#14-open-questions)
15. [Name & Brand](#15-name--brand)
16. [Session Log](#16-session-log)

---

## 1. The Idea

Liner is a personal music canvas — a place where anyone (artist, DJ, or listener) can show the world what music means to them through **moments and memories**, not ratings and reviews.

It is not:
- A streaming platform
- A review or rating app
- A social network with an algorithmic feed
- An artist marketing tool

It is:
- A canvas. Your page, your cards, your story.
- Private by default. Public or anonymous when you choose.
- Organized around **memory cards** — song + photo/video + your words.
- Built with Instagram's privacy model (per-card visibility, friends require approval).

**One-line pitch:** *"The thing that comes inside an album — the liner notes — but for everyone, in both directions."*

---

## 2. Why Music

Research-backed reasons music is uniquely powerful and why no platform serves this yet:

| Mechanism | What it means for the product |
|---|---|
| **Dopamine + identity** — music activates the medial prefrontal cortex more than food or drugs. "Music you love isn't just rewarding — it feels like *ours*." | People will care deeply about their page. Music taste is identity, not preference. |
| **Memory encoding** — the hippocampus and amygdala attach music to place, time, emotion simultaneously. One song = a time machine. | The memory card (song + moment + your words) is the right atomic unit. Ratings are wrong. |
| **Prediction + surprise** — the brain anticipates musical patterns and releases dopamine on surprise. The drop that hits different. | Explains why music is so hard to replicate with AI. The "realness" argument holds scientifically. |
| **Sad music = comfort** — slow/sad music doesn't cause sadness; it produces nostalgia, emotional release, and autobiographical reflection. | Anonymous heartbreak playlists aren't masochistic. They're processing. The product is justified. |
| **Teenage music is permanent** — music from ages 14–24 is encoded differently. It's the soundtrack of becoming a person. | Nostalgia is the most powerful emotion in the product. Lean into it. |
| **Social glue** — from tribal drums to national anthems, music builds collective identity. | The public/anonymous sharing layer creates belonging for strangers. |

**The four things music actually means to a person:**
1. **Identity** — who you are, who your people are
2. **Memory** — a time machine, involuntary and instant
3. **Emotion** — a safe container for feelings too big to say out loud
4. **Belonging** — proof that someone else felt what you felt

None of the existing platforms give you a canvas for any of these four things. That's the gap.

---

## 3. Competitive Landscape

### Bucket 1 — Artist tools (crowded, skip as primary wedge)
Linkfire, Linktree, Sonikit, Beacons, Supertape, Official Artists. All do the same thing: aggregate streaming links, analytics, pre-saves, merch. None lean into story or meaning. Competing here = competitor #47.

### Bucket 2 — Listener memory apps (our territory)
| App | What it does | What it's missing |
|---|---|---|
| **Echo (songmemories.app)** | Song + photo + words, no social feed, privacy-first | Strictly private. No public sharing, no anonymous layer, no lyric clip. |
| **Musiary** | Spotify-connected daily diary, one song/day | Shallow, no story, no social |
| **Melody Lane** | Capture music-linked memories | Minimal traction, no sharing |
| **Songfaces** | 2018 attempt at same concept | Never shipped meaningfully |

### Bucket 3 — Rating/review apps (wrong format)
| App | Users | Problem |
|---|---|---|
| **Musicboard** | 400K+, growing 16K/month | Review format doesn't work for music. "Music is more subjective — reviews feel one-note, taste becomes personality." Cold and empty. |
| **RateYourMusic** | Large, active | Old, ugly, web-only |
| **Kora, Sleevd, Musotic** | Small, early | All still built on rating paradigm |

**Key insight from reviews:** *"The Letterboxd format doesn't map to music."* Film has craft to evaluate. Music has feeling. Star ratings on songs are boring. Your annotation of what a lyric meant to you at 2am is not.

### What nobody is building
The exact combination of:
- Per-card privacy toggle (not per-account)
- Memory/annotation as the atomic unit (not rating)
- Anonymous public sharing
- Instagram story card export as the growth engine
- Lyric highlight + personal annotation layer

**This combination does not exist.**

---

## 4. What Makes Liner Different

Three structural advantages that are hard to copy:

### 4.1 Per-card privacy (not per-account)
Every app has one privacy setting. Liner has Instagram Stories-level granularity per card:
- This heartbreak playlist → **private**
- This Avicii lyric annotation → **public, anonymous**
- This pre-match playlist → **friends only**

Requires real product architecture. Can't be bolted on later.

### 4.2 Annotation as the atomic unit
The entire competitor field (Musicboard, RYM, AOTY, Kora, Sleevd) is built on the rating paradigm. Liner's atom is a written annotation tied to a personal moment. These two paradigms are philosophically incompatible — competitors can't copy this without rebuilding from scratch.

### 4.3 Anonymous public sharing + IG export = self-funding growth loop
- Echo explicitly rejects public
- Musicboard requires identity
- Nobody has built: "post your memory anonymously → export as beautiful card → stranger sees it on IG at 2am → taps 'find yours →' → lands on liner.fm"

That's the entire acquisition funnel with no ad spend.

### 4.4 Genius annotations as context layer (future)
Pull Genius crowd-sourced annotations alongside lyrics. "Here's what 4,200 people said this line means" + below it, your personal annotation. No other product has this layering.

---

## 5. Product Architecture

```
liner.fm/[username]          ← public profile page (shows only public cards)
liner.fm/[username]/canvas   ← private canvas (all cards, all states)
liner.fm/card/[id]           ← individual card permalink
liner.fm/explore             ← public feed (public + anonymous cards from all users)
liner.fm/friends             ← friends feed (cards shared with you)
```

### Privacy states per card
| State | Who sees it |
|---|---|
| Private | Only you |
| Friends | Approved followers only |
| Public (named) | Everyone, attributed to your handle |
| Public (anonymous) | Everyone, no name attached |

### Friends model
Exactly Instagram:
- You follow people → request sent
- They approve → you see their friends-only cards
- Anonymous users can request to follow; you approve/ignore
- Anonymity is about the card's public face, not the whole account

---

## 6. User Types

Three users, one underlying primitive (the memory card):

| User | Their page looks like | Primary use case |
|---|---|---|
| **Listener** | Heartbreak playlists, song memories, lyric annotations | Journaling, sharing, finding belonging |
| **Artist** | Story behind each track, lyrics + meaning, demos, photos, tour memories | Soul-bared EPK, direct fan connection |
| **DJ** | Set archives, "what I was feeling that night," tracklists with narrative | Story layer nobody builds for DJs |

**Build order:** Listeners first. Artists follow audiences, not the reverse. Letterboxd built for movie fans; filmmakers showed up later.

---

## 7. Core Primitives

### The Memory Card
The atomic unit. One card = one song/playlist + optional media + your words.

**Card types:**
| Type | Contains |
|---|---|
| **Memory card** | Song embed + photo + your story |
| **Lyric card** | Highlighted lyric + your annotation |
| **Playlist card** | Collection of songs + a title + context |
| **Story card** | Longer written piece, like a liner note to yourself |
| **Video card** | Video clip from a moment + song playing |

**Card fields:**
- Song (Spotify/Apple Music link, album art auto-pulled)
- Title (the headline of your memory)
- Date + optional place tag
- Media (photo or video upload)
- Journal text (open, no length limit, writing toolbar)
- Lyric quote (highlighted excerpt with source attribution)
- Mood chips (bittersweet / nostalgic / heavy / hopeful / raw / grateful / joyful / at peace)
- Privacy state (private / friends / public / anonymous)

### The Canvas
Your personal page. Grid of cards, filterable by privacy state. Counter shows: "14 cards · 3 public · 5 friends · 6 private."

### The Explore Feed
Reverse-chronological or resonated-sorted feed of all public cards across the platform. Anonymous ones sit alongside named ones. Filter by card type. Key metric: **resonated** (not liked).

### The IG Share Card
Auto-generated 9:16 export per card. Format:
- Dark background (#1a1730)
- Lyric or memory text, large
- Album art + song name + artist
- "liner.fm" watermark bottom left
- "find yours →" bottom right (the pull)

Square (1:1) variant for Twitter/X.

### Resonated (not Liked)
The social action. "847 people resonated with this." Not liked. Not hearted. Resonated = *I felt this too.* Different word, different psychological weight.

---

## 8. Privacy Model

```
Page level:     Public profile URL exists (shows only public cards)
Card level:     Each card independently: private / friends / public named / public anonymous
Anonymous mode: Toggle in sidebar. When on, new public cards strip your name.
Friends:        Instagram-style approvals. Requested → pending → approved.
```

**UI treatment:**
- Privacy badge on every card (visible at a glance)
- Tap badge to change privacy inline
- Anonymous mode toggle shows "anonymous mode: on/off" in sidebar under your name
- Counter in canvas header: "14 cards · 3 public · 5 friends · 6 private"

---

## 9. Features Designed So Far

### 9.1 Canvas (My Page)
- Card grid, filterable by privacy state
- Stats bar: cards / tracks imported / public / resonated
- Import banner (dismissible after import)
- Add card button → modal

### 9.2 Card Detail View
**Split layout:**
- Left: dark side — album art / photo / video (tabbed), song identity, waveform, streaming platform chips
- Right: white side — title input, metadata row (type + date + place), writing toolbar, journal textarea, lyric pull-quote block, attachment row, mood chips, resonated counter

**Writing toolbar:** B / I / U / " lyric / — break / H1 / list

### 9.3 Explore Feed
- Card stream, swipeable
- Filter chips: All / Songs / Playlists / Lyrics
- Each card: user chip (anon or named), card content, resonated count, share button
- Selected card → right panel shows IG share card preview + share options

### 9.4 Lyric Highlighter (v2, post-Musixmatch deal)
- Full lyrics loaded line by line, dimmed
- Tap line → highlights purple with left border
- Multi-line selection supported
- Right panel: clip preview (dark card), "add to" options (memory card / standalone / IG only), annotation field
- "2 lines selected" counter in footer
- "via Musixmatch" attribution

### 9.5 Import Flow
Options: Playlists / Top Tracks / Recently Played / Liked Songs
Progress bar during import. Songs populate sidebar and become searchable in card creation.

---

## 10. Lyrics API Strategy

### The landscape
| Provider | Status | Notes |
|---|---|---|
| **Musixmatch** | Best option — 14M+ songs, licensed | Free tier: 30% lyrics, non-commercial only. Commercial = email sales@musixmatch.com for custom deal. |
| **Genius API** | Good for annotations, permissive | Full lyrics display still hits copyright at scale. Better fit philosophically (annotation-native). |
| **Reverse-engineered wrappers** | Hard no | "Strictly educational," legal risk, don't build on this. |

### Three-tier strategy

**v1 (now, zero cost):**
User types or pastes the lyric they want to quote. Spotify API provides all song metadata (title, artist, album art, year) — free and permissive. User enters the lyric manually. Legally clean — user-generated quotation, not programmatic display.

**v2 (post-traction, post-revenue):**
Negotiate Musixmatch commercial deal. Build the in-app line-by-line highlighter. Full experience.

**v3 (Genius layer):**
Pull Genius crowd-sourced annotations alongside lyrics. "4,200 people said this means X" + your annotation below. Unique layering no other product has.

### Copyright principle
Display only what users explicitly choose to quote. Short quotations for personal expression = fair use territory. Never programmatically display full lyrics without a commercial license.

---

## 11. Spotify API Capabilities

### What we can pull (with user OAuth)

| Scope | Endpoint | What we get |
|---|---|---|
| `user-library-read` | `GET /me/tracks` | Liked/saved songs |
| `playlist-read-private` | `GET /me/playlists` | All playlists (public + private) |
| `user-top-read` | `GET /me/top/tracks` | Top tracks (short/medium/long term) |
| `user-read-recently-played` | `GET /me/player/recently-played` | Last 50 played tracks |
| `user-read-private` | `GET /me` | Profile, display name, avatar |
| `user-read-email` | `GET /me` | Email (for account creation) |

### What we get per track
- Track name, artist(s), album name
- Album art (multiple sizes)
- Spotify URL + URI
- Duration, explicit flag, popularity score
- Preview URL (30-second MP3, can be null)
- `added_at` timestamp (when they saved/added it)

### February 2026 API changes (critical)
Spotify made breaking changes in Feb 2026:
- **Dev Mode apps**: Max 25 users, owner must have active Spotify Premium
- **Extended Quota Mode apps**: No changes, existing integrations unaffected
- **Migration**: Several endpoints removed/renamed (see February 2026 migration guide)

**For MVP:** Dev Mode is fine. 25 users = plenty for testing. Apply for Extended Quota when ready to go public.

### Key removed endpoints (avoid these)
- `GET /tracks` (batch) → use individual fetch
- `GET /browse/categories/{id}`
- `GET /users/{id}/playlists` → use `GET /me/playlists`
- Content-specific save/remove endpoints → use generic `PUT/DELETE /me/library`

### Auth flow
PKCE (Authorization Code with PKCE) — correct for web apps without a backend secret. No server needed for MVP.

---

## 12. Tech Stack — MVP

### Frontend
- **Framework:** React (single-page app, no Next.js needed for MVP)
- **Styling:** CSS custom properties, no framework — keeps it lean and fast
- **Fonts:** Instrument Serif (display/headings) + DM Sans (body) — warm, editorial, not generic
- **Routing:** Simple hash-based (`#canvas`, `#explore`, `#import`) for MVP

### Auth
- Spotify OAuth 2.0 with PKCE (client-side, no server needed)
- Store access token in `sessionStorage`, refresh token in `localStorage`

### Data
- **MVP:** `localStorage` for user cards (no backend)
- **v2:** Supabase (Postgres + Auth + Storage) — fast to set up, generous free tier
- **Later:** migrate to proper backend when needed

### APIs
| API | Purpose | Cost |
|---|---|---|
| Spotify Web API | Library import, song search, metadata | Free |
| Musixmatch (v1) | User pastes lyrics manually | Free (user-generated) |
| Musixmatch (v2) | In-app lyric display | Commercial deal |
| html2canvas or puppeteer | IG card export | Free/open source |

### Hosting
- **MVP:** Vercel (free tier, zero config, instant deploy)
- Custom domain: `liner.fm` (available, register it)

### Future additions
- **Backend:** Node.js + Express or Supabase Edge Functions
- **DB:** Supabase Postgres
- **Storage:** Supabase Storage (photos/videos)
- **Email:** Resend (transactional, magic links)

---

## 13. MVP Scope

### What ships in MVP (8-week target)

**Week 1–2: Auth + Import**
- [ ] Spotify OAuth PKCE flow
- [ ] Pull user profile (name, avatar)
- [ ] Import: playlists, top tracks, recently played, liked songs
- [ ] Store in localStorage
- [ ] Populate sidebar with playlists

**Week 3–4: Canvas + Card Creation**
- [ ] Canvas grid view with filter chips
- [ ] Create card modal: song search (from imported library), title, journal, mood chips, privacy selector
- [ ] Lyric card type: manual lyric paste field
- [ ] Card renders in grid with privacy badge
- [ ] Stats bar updates

**Week 5: Card Detail View**
- [ ] Click card → full detail view
- [ ] Left: album art / song identity / streaming chips
- [ ] Right: journal text (editable), mood chips, lyric block
- [ ] Edit mode toggle

**Week 6: Explore + Share**
- [ ] Explore feed (seeded with demo cards)
- [ ] Resonated button
- [ ] IG share card generator (html2canvas)
- [ ] Copy link

**Week 7: Polish + Friends**
- [ ] Anonymous mode toggle
- [ ] Follow/unfollow (local state for MVP)
- [ ] Mobile responsive layout
- [ ] Empty states, loading states, error states

**Week 8: Launch prep**
- [ ] Deploy to Vercel
- [ ] `liner.fm` domain
- [ ] Spotify app in Extended Quota (apply)
- [ ] Seed 10–20 real memory cards yourself
- [ ] Send to 10 friends

### What does NOT ship in MVP
- Backend / database (localStorage only)
- Real-time Explore feed (seeded/demo data)
- Musixmatch in-app highlighter (v2)
- Video uploads (photo only for MVP)
- Push notifications
- Artist-specific features
- DJ set archive

---

## 14. Open Questions

| Question | Status | Notes |
|---|---|---|
| Domain: `liner.fm` available? | ❓ Check | `.fm` domains fit the brand perfectly |
| Spotify Extended Quota — how long to approve? | ❓ Check | Historically 1–4 weeks, submit early |
| IG card export: html2canvas vs server-side render? | ❓ Decide | html2canvas = client-side, no server. Server-side = better quality but needs backend. Start client-side. |
| Backend timing: localStorage → Supabase? | ❓ When | Probably after 10–20 real users. LocalStorage breaks on multi-device. |
| Musixmatch commercial deal — what's the price? | ❓ Research | Email sales@musixmatch.com when ready |
| Artist onboarding — do we build it in v1? | ❌ No | Listeners first. Artists follow. |
| Genius annotation layer — how to surface it? | ❓ Design | Needs its own design pass |
| How does the Explore feed get seeded at launch? | ❓ Plan | Self-seed. 20 real cards from you + friends before anyone else uses it. |

---

## 15. Name & Brand

### Name: **Liner**
Short for liner notes — the thing that came folded inside an album, written by the artist or a fan, that told you what the music *meant*. Nobody uses this word in tech yet. Warm, analog, exactly the vibe.

**Domain:** `liner.fm` (`.fm` = radio, music, perfect TLD)

**Tagline options:**
- *"your music, your story"*
- *"the story behind the song"*
- *"save how it felt"*
- *"music is more than a playlist"*

### Visual identity (from mockups)
- **Primary font:** Instrument Serif — warm, editorial, feels like a book
- **Body font:** DM Sans — clean, readable, modern without being cold
- **Primary color:** Deep purple (`#534AB7`) — creative, emotional, not corporate
- **Background:** Warm off-white (`#f5f2ec`) — paper, analog, not sterile white
- **Dark surface:** `#1a1730` — night listening, intimate
- **Accent colors:** Teal (positive), Coral (energy), Amber (mood/warmth), Pink (resonated)

### Vocabulary
| Avoid | Use instead |
|---|---|
| Like | Resonate |
| Post | Memory / Card |
| Feed | Canvas (yours) / Explore (theirs) |
| Followers | Friends |
| Rating | — (don't rate, ever) |

---

## 16. Session Log

### Session 1 — Ideation
- Started with "personal portfolio website for music people"
- Identified three user types: artists, DJs, listeners
- Decided listeners are the wedge, not artists
- Named the product "Liner"

### Session 2 — Competitive Landscape
- Searched the field: Musicboard, Echo, Musiary, Melody Lane, Songfaces
- Identified Echo as closest competitor — but strictly private, no public layer
- Identified Musicboard as market leader — but wrong format (ratings, not memories)
- Confirmed: the exact Liner combination doesn't exist

### Session 3 — Letterboxd Angle
- Explored "Letterboxd for music" lane
- Found it's crowded: Musicboard (400K users), Kora, Sleevd, Musotic, Crate
- Key insight: **the format is broken for music.** Reviews flatten music into taste-signaling. Memory is the better unit.
- Reframe: not "Letterboxd for music" but the opposite — memory not review.

### Session 4 — First Principles Reset
- Went back to why people love music (neuroscience + psychology)
- Four pillars: identity, memory, emotion, belonging
- Designed the product from those pillars up

### Session 5 — Privacy + Feed Design
- Defined Instagram-style privacy model (per-card, not per-account)
- Designed Explore feed (reverse-chron, resonated metric)
- Designed IG share card (9:16, dark, "find yours →")
- Friends model: exactly Instagram (request → approve)

### Session 6 — Card Detail View
- Split layout: dark media side (left) + white journal side (right)
- Tabbed media: album art / photo / video
- Writing toolbar, lyric pull-quote block, mood chips, resonated counter
- "How does this feel?" mood chips instead of star ratings

### Session 7 — Lyrics API Research
- Three-tier strategy: v1 manual paste / v2 Musixmatch commercial / v3 Genius annotations
- Designed full in-app lyric highlighter UI (for v2)
- Copyright principle established: user-generated quotation, not programmatic display

### Session 8 — Competitive Check + MVP Decision
- Final check: nobody building the exact Liner combination
- Biggest risk: Spotify could ship "Spotify Memories" and kill the category
- Decision: build a working webapp MVP, Spotify-integrated
- Researched Spotify API scopes, Feb 2026 changes, PKCE auth

### Session 9 — MVP Build Started
- Decided to build a full working webapp MVP before anything else
- Researched Spotify API Feb 2026 changes (Dev Mode = 25 users max, need Extended Quota for public launch)
- Confirmed PKCE auth flow is correct for client-side web apps
- Began building liner-mvp.html — cut off mid-file

### Session 10 — MVP Complete
- Completed full single-file MVP: `liner-mvp.html` (1,216 lines, 41 functions)
- All 15 core features verified present and working:

| Feature | Status |
|---|---|
| Spotify OAuth PKCE (no backend needed) | ✓ |
| Token callback handler | ✓ |
| Library import (playlists, top, recent, saved) | ✓ |
| Demo mode (works without Spotify credentials) | ✓ |
| Canvas with card grid | ✓ |
| Card creation modal (song search, type, mood, privacy) | ✓ |
| Card detail split view (dark media left, journal right) | ✓ |
| Explore feed with demo cards | ✓ |
| Share modal with IG card preview | ✓ |
| Per-card privacy (private/friends/public/anonymous) | ✓ |
| Filter chips on canvas | ✓ |
| Song search against imported library | ✓ |
| Mood picker | ✓ |
| Privacy selector | ✓ |
| localStorage persistence | ✓ |

**How to run:**
1. Open `liner-mvp.html` in browser
2. Click "Try demo mode →" to use without Spotify credentials
3. OR add your Spotify Client ID to `SPOTIFY_CLIENT_ID` constant and register `http://localhost:PORT/liner-mvp.html` as redirect URI in Spotify Developer Dashboard

**To get real Spotify auth working:**
1. Go to https://developer.spotify.com/dashboard
2. Create an app → get Client ID
3. Add redirect URI = the URL where you're serving the file
4. Replace `YOUR_CLIENT_ID_HERE` in the file with your Client ID

**Next steps:**
- [ ] Test in browser, find any UI bugs
- [ ] Set up Vercel deploy
- [ ] Register `liner.fm` domain
- [ ] Get Spotify Client ID, test real auth
- [ ] Apply for Extended Quota Mode
- [ ] Seed with real personal memory cards
- [ ] Share with 5–10 people for first feedback

---

*Last updated: Session 10*
*Next: Browser test → Vercel deploy → real Spotify auth → seed with real memories*
