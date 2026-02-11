---
name: inventorying-provider-data
description: Research and document all user data available from a provider across API, GDPR/CCPA export, and in-app export. Produces a structured inventory for human review and to inform connector development.
---

# Inventorying Provider Data

## Overview

Systematically research and document ALL user data available from a single provider across every access method (API, GDPR/CCPA bundle, in-app export). The output is a complete inventory readable by humans (product, engineering, partnerships) that also serves as the foundation for connector implementation.

**Output**: A completed `data-availability-inventory.md` in `docs/providers/{provider}/`.

**Reference example**: `docs/providers/spotify/data-availability-inventory.md` — fully populated inventory from live testing.

## Process

### Phase 1: Reconnaissance (Web Research)

Research the provider using web sources. Gather information on ALL three access channels:

**1. API**
- Find official API documentation
- Identify all user-specific endpoints (not catalog/search)
- Document auth method, scopes, rate limits
- Note any partner program or approval gates
- Look for known limitations (e.g. Spotify's 50-track recently-played buffer)

**Endpoint discovery checklist** (don't stop at the main docs page):
- Browse the **full API reference listing**, not just "Getting Started" guides — endpoints like `/me/library/playlist-folders` are often buried in reference but absent from tutorials
- Search the API reference for `/me/` or equivalent user-specific path prefix — this catches endpoints the main docs page doesn't link to
- Look for **variant endpoints** — many APIs have parallel sets (e.g., catalog ratings vs library-specific ratings, `videos.getRating` vs `videos.list?myRating=like`). If you find one form, search for the other. Variant endpoints often serve different use cases (bulk lookup vs individual check) and return different response shapes.
- Search for **recently added endpoints** in changelogs, developer blogs, and conference session notes
- Check community client libraries (e.g., on GitHub) — their endpoint lists often include recently-added or poorly-documented endpoints
- **For unofficial/community libraries**: browse the library's **complete API reference or source code method listing**, not just examples or README. Count the methods — if you're documenting significantly fewer methods than the library exposes, you've missed some. Specifically check for methods related to every major product feature (see Product Feature Cross-Check).
- If the API has an OpenAPI/Swagger spec, download it and grep for all `/me/` or user-scoped paths

**2. GDPR/CCPA Data Request**
- Find the provider's privacy/data request page
- Document the request flow (URL, steps, confirmation)
- Research processing time (check reddit, forums for real reports)
- Find what data is included in the bundle (official docs + user reports)
- Note any options (e.g. "basic" vs "extended" export)
- When documenting GDPR/CCPA data: don't just list what's in the export bundle — cross-check against the provider's **privacy policy** and **all product surfaces** (voice assistants, cloud sync, companion apps). Each product surface may generate data categories the main export doesn't highlight.

**3. In-App Export**
- Check if provider offers a non-GDPR export (e.g. Goodreads CSV, Google Takeout)
- Document location, format, and what's included
- Note limitations vs the GDPR bundle

**4. Additional / Alternative Data Sources**
- Check for **annual recap/review features** (e.g., Spotify Wrapped, Apple Music Replay, YouTube Music Recap) — these often expose data not available elsewhere
- Check for **web dashboard views** that show stats not in the API (e.g., listening time, top genres)
- Search for the provider's participation in the **Data Transfer Initiative (DTI)** at https://dtinit.org/
- Look for **third-party authorized apps** that surface additional data (e.g., last.fm scrobbling partnerships, IFTTT integrations)
- Check if the provider offers **developer analytics dashboards** (e.g., Spotify for Artists) that expose different data

**Research tips:**
- Search `site:reddit.com "{provider} data export"` for real user experiences
- Search `"{provider}" GDPR request days` for processing time reports
- Check `{provider} API changelog` for recently added/deprecated endpoints
- Look for community client libraries — they often document undocumented behavior

### Phase 2: API Exploration (If Applicable)

If the provider has an API and we have credentials:

1. **Test every user-specific endpoint** — don't trust docs alone
2. **Document actual response shapes** — note fields docs don't mention
3. **Test pagination behavior** — cursor vs offset, hard limits, sort order
4. **Test incremental patterns** — does the endpoint support `since` or cursor-based polling?
5. **Count actual data** — record real item counts for the user
6. **Note quirks** — null IDs, deleted content, beta endpoints, undocumented fields

### Phase 3: Fill the Template

**Product feature cross-check**: Before filling the template, list all major features of the provider's product (check their marketing page, app store listing, "What's New" announcements, and any help center feature index). Verify each feature has corresponding data types in your inventory. Common features that get missed:
- **Podcasts / audiobooks** (especially if recently added to a music service)
- **Social features** (collaborative playlists, shared listening sessions, follows, friend activity)
- **Upload/import features** (user-owned content, e.g., YouTube Music uploads, Apple Music iCloud Library)
- **Offline/download features**
- **Voice assistant integrations**
- **Creator/artist tools** (if the platform has a creator side)

If the provider has an unofficial library, verify the library's method list covers all these features. If the library has methods for a feature you haven't documented, that's a gap in your inventory.

Use the template at `docs/skills/inventorying-provider-data/templates/data-availability-inventory.md`. Key rules:

- **Every row must be filled** — use ❓ for genuinely unknown, never leave blank
- **Delete inapplicable sections** — if no API, delete "API Details"; if no GDPR, delete that section
- **Add categories as discovered** — the template categories are starting points, not exhaustive
- **Include gotchas prominently** — hard limits, beta status, deprecated endpoints
- **Links must be real** — verify every URL loads

**Methodology transparency**: The inventory's status field and a note at the top must accurately reflect the research methodology:
- If you did NOT test live API endpoints, state: "Documentation review only — not verified with live API testing"
- If you DID test live endpoints, state which ones and note real observed values vs documentation claims
- Never imply live verification when only docs were consulted

### Sourcing and Confidence Requirements

Every quantitative claim (rate limits, processing times, quota costs, buffer sizes, data retention periods) must carry a confidence tier:

| Tier | Label | Use when | Example |
|------|-------|----------|---------|
| **Official** | State as fact | Published in provider's own docs with a linkable URL | "Rate limit: 100 req/15min ([docs](url))" |
| **Reported** | "Reported as ~X" | From user reports (Reddit, forums) or community testing; include source | "Processing time: reported as ~7 days ([reddit thread](url))" |
| **Estimated** | "Estimated ~X (unverified)" | Your inference from indirect evidence; no primary source | "Rate limit: estimated ~20 req/s (unverified; based on 429 response testing)" |
| **Unknown** | Use ❓ | No evidence at all | ❓ |

**Rules:**
- If the provider publishes **no official numbers**, say "Unpublished" — do NOT state a specific number as the headline
- If you cannot find the **specific page** that states a fact, do not attribute it to "the provider's FAQ" — say "reported by users" or "could not verify source"
- GDPR processing times should cite either the provider's stated maximum or real user reports with links — not both blended into a single unsourced number
- **When simplifying complex systems, state the exceptions**: If a quota/rate system has a general rule with exceptions (e.g., "most reads = 1 unit, but `search.list` = 100 and `captions.list` = 50"), either list the exceptions or explicitly note "with notable exceptions — see [link]". Never state a simplified rule as if it is universal when the official docs describe exceptions.
- **Deprecation claims must be precise**: State exactly what is deprecated (e.g., "the `bulletins` activity type is deprecated" NOT "the activities endpoint is deprecated"). Link to the deprecation notice. Note what still works.

### Phase 4: Cross-Validate

Compare data availability across methods:

| Question | Why it matters |
|----------|---------------|
| What does GDPR give that API doesn't? | Identifies data only available via import |
| What does API give that GDPR doesn't? | Identifies real-time-only data |
| Are there data types in neither? | Identifies gaps or undiscovered endpoints |
| Do counts match across methods? | Validates completeness |

- When verifying URLs, follow redirects and use the **final destination URL**. If the URL you found 301-redirects to a different path, use the canonical version.

### Phase 5: Implementation Recommendations

Based on the inventory, recommend:

1. **Primary access method** — API-first? Import-only? Hybrid?
2. **Which scopes/permissions to request** — only what's needed
3. **Incremental sync strategy per stream** — cursor, timestamp, snapshot, or full
4. **Sync frequency** — based on data velocity and API limits
5. **MeDB manifest fields** — connectivity type, oauth config, import config

## Output Location

```
docs/providers/{provider}/
├── data-availability-inventory.md   # The completed template
└── {topic-specific-notes}.md        # Optional: deep dives (e.g. recently-played.md)
```

## Quality Checklist

Before marking complete:

- [ ] Every data type the provider holds is listed (check their privacy policy for hints)
- [ ] Every user-specific API endpoint is documented (not just the ones we use)
- [ ] GDPR/CCPA request flow is documented step-by-step with real URLs
- [ ] Processing time estimates have sources (not guesses)
- [ ] All links verified working
- [ ] Performance comparison table filled (history depth, rate limits, latency, risk)
- [ ] Implementation recommendations included
- [ ] Manifest JSON snippet ready for connector work
- [ ] Open questions listed (not hidden)
- [ ] **Endpoint discovery was exhaustive** — browsed full API reference (not just guides), searched for `/me/` paths, checked for variant endpoints, reviewed changelogs, counted unofficial library methods against docs
- [ ] **All major product features represented** — checked marketing page, app store listing, help center; verified podcasts, social features, uploads, offline, creator tools all considered
- [ ] **All quantitative claims have sourcing tiers** — rate limits, processing times, quota costs, buffer sizes labeled Official/Reported/Estimated/Unknown
- [ ] **Complex systems not oversimplified** — quota exceptions noted, deprecation scope precisely stated, general rules qualified when exceptions exist
- [ ] **No unsourced attributions** — never say "the provider states X" without a linkable URL
- [ ] **Additional data sources checked** — annual recaps, web dashboards, DTI, third-party integrations
- [ ] **GDPR data categories cross-checked against privacy policy** — every product surface considered
- [ ] **URLs are canonical** — no redirect-only URLs; all links verified working
- [ ] **Method/endpoint names verified** — spot-checked against actual library docs, API reference, or source code (not from memory)
- [ ] **Methodology stated** — status field and intro note reflect whether live testing or documentation-only
- [ ] **Social/collaborative features documented** — collaborative playlists, shared sessions, activity feeds considered

## Providers Without APIs

Many providers (Instagram, LinkedIn, Netflix, Amazon) have no useful public API for user data export. For these:

- **Skip the API sections entirely** — delete them from the template
- **Focus on GDPR/CCPA bundle analysis** — this is the primary/only access method
- **Document the in-app export if available** — e.g. Instagram's "Download Your Information"
- **Research the bundle format thoroughly** — what files, what fields, what format (JSON/CSV/HTML)
- **Note the request flow precisely** — users will need step-by-step instructions
- **Research processing times from real users** — search forums for actual experiences

## Common Provider Patterns

| Pattern | Examples | MeDB Approach |
|---------|----------|---------------|
| Rich API + GDPR | Spotify, GitHub, Strava | API for real-time, GDPR for historical backfill |
| Limited API + GDPR | Spotify recently-played | API for incremental, GDPR for full history |
| No API, has GDPR | Instagram, LinkedIn, Netflix | Import-only connector with file upload |
| No API, has in-app export | Goodreads, some fitness apps | Import-only, document export steps |
| API with partner gate | Spotify (at scale), Twitter | Document gate; build for individual use first |

## Existing Connector Manifests

Reference existing manifests in `electron-app/src/main/plugins/builtin/` for format:
- `com.medb.spotify/manifest.json` — API polling + OAuth example
- `com.medb.instagram-import/manifest.json` — File import example
- `com.medb.goodreads-import/manifest.json` — CSV/ZIP import example
- `com.medb.strava/manifest.json` — API polling + OAuth example

## Related Skills

- `researching-web-sources` — for Phase 1 web research
- `implementing-issue` — for building the connector after inventory
- `writing-specifications` — if the connector needs a spec before implementation
