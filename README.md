# AniFlix API

 ## Base URL: `[https://aniflix.n1yshi.dev`

---

## 🔍 Search & Discovery

### `GET /search`
Search anime by name.
```
Params: query (required), page=1, per_page=20
Try:   /search?query=naruto&page=1&per_page=5
```

### `GET /suggestions`
Lightweight autocomplete. Max 8 results.
```
Params: query (required)
Try:   /suggestions?query=one piece
```

### `GET /spotlight`
Top 10 trending anime right now.
```
Try:   /spotlight
```

### `GET /filter`
Advanced multi-parameter filter.
```
Params: genre, tag, year, season (WINTER/SPRING/SUMMER/FALL), format (TV/MOVIE/OVA/ONA/SPECIAL), status (RELEASING/FINISHED/NOT_YET_RELEASED/CANCELLED), sort (SCORE_DESC/POPULARITY_DESC/TRENDING_DESC/START_DATE_DESC), page=1, per_page=20
Try:   /filter?genre=Action&format=TV&sort=SCORE_DESC
```

---

## 📊 Collections

### `GET /trending`
### `GET /popular`
### `GET /upcoming`
### `GET /recent`
### `GET /schedule`
All paginated: `page=1, per_page=20`. Schedule includes `airingAt`, `timeUntilAiring`, `next_episode`.

---

## 📖 Details

### `GET /info/{anilist_id}`
Complete anime page — title, description, cover, banner, format, status, episodes, duration, score, genres, tags, studios, trailer, relations, recommendations, stats, external links, streaming episodes, and more.
```
Try:   /info/20 (Naruto) · /info/21 (One Piece)
```

### `GET /anime/{id}/characters`
```
Params: page=1, per_page=25
Try:   /anime/20/characters
```

### `GET /anime/{id}/relations`
```
Try:   /anime/20/relations
```

### `GET /anime/{id}/recommendations`
```
Params: page=1, per_page=10
Try:   /anime/20/recommendations
```

---

## ▶️ Streaming

The API automatically tries providers in order: **Miruro → AnimeKai → ReAnime** until one succeeds.

### Step ① `GET /episodes/{anilist_id}`
Get episodes from available streaming providers.
```
Try:   /episodes/21
```
Miruro returns episodes with `episodeId` (base64-encoded), plus a ready-to-use `id` like `watch/kiwi/21/sub/animepahe-1079`.

### Step ② Get video sources

**Option A — `/watch/{provider}/{anilistId}/{category}/{slug}`**
Use the `id` from Step ① directly (recommended for Miruro):
```
Try:   /watch/kiwi/21/sub/animepahe-1079
```
Or specify a provider directly:
```
Try:   /watch/animekai/21/sub/{token}
Try:   /watch/reanime/21/sub/{slug}|{ep}
```

**Option B — `GET /sources`**
```
Params: episodeId (required), provider (required), anilistId (required), category=sub
Try:   /sources?episodeId=animepahe:4:36600:39&provider=kiwi&anilistId=21&category=sub
```

Provider values: any Miruro stream provider (`kiwi`, `zoro`, `pahe`, etc.) or `animekai`, `reanime`.

Episode ID formats by provider:
| Provider | Format |
|----------|--------|
| Miruro | Base64-encoded (e.g. `animepahe:4:36600:39`) |
| AnimeKai | Token string (from AnimeKai episode data) |
| ReAnime | `{slug}\|{ep}` (e.g. `naruto|1`) |

Category: `sub` or `dub`.

### Step ③ Play
Feed the returned `url` (m3u8) into any HLS player.

---

## 🛡️ Rate Limiting
15 requests / minute, 90s block. Returns 429.

## ⚠️ Errors
| Code | Meaning |
|------|---------|
| 200 | Success |
| 400 | Bad request |
| 404 | Not found |
| 429 | Rate limited |
| 502 | Upstream provider unavailable |
| 503 | Service unavailable |

---
