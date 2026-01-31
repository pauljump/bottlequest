---
id: IDEA-174
title: BottleQuest
one_liner: AI-native "Pokémon Go for rare items" starting with liquor - scan, sighting, XP, quests, social loops with real infra moat
status: concept
confidence: Very High
directory: bottlequest
related: []
technology: [React Native, Python, FastAPI, Postgres, pgvector, Redis, Computer Vision, Multi-modal ML, PostGIS]
domain: [Consumer Gaming, AI Infrastructure, Computer Vision, Location-based Social, Marketplace]
---

# IDEA-174: BottleQuest

## One-liner
Pokémon Go meets rare item hunting: AI-native mobile game where users scan shelves to find rare liquor bottles (and eventually any collectible), earn XP/badges, complete quests, and share sightings—with the real moat being cross-vertical Item Identity + Sighting Trust + Personalization infrastructure.

## Problem

### For: Collectors and hunters (liquor enthusiasts first, then sneakerheads, card collectors, vintage hunters, etc.)

**Pain Points:**
1. **Hunting is manual and frustrating** - Driving store-to-store, calling ahead, getting "maybe we have it" answers
2. **No shared intelligence** - Everyone hunts alone; sightings aren't shared or mapped
3. **Information asymmetry** - Stores don't advertise rare stock; insiders get first dibs
4. **No game loop** - Hunting feels like work, not play; no progression, no social validation
5. **Discovery is weak** - "I don't know what I should hunt next near me right now"
6. **Identity problem at scale** - Messy variants (store picks, batches, years, packaging revs) make cataloging impossible without AI
7. **Spam/trust problem** - Any crowdsourced map becomes worthless without trust/verification scoring
8. **Liquor-only solutions don't scale** - Need the same hunting infrastructure for sneakers, cards, vintage, limited releases

**Current Solutions:**
- Facebook groups / Discord servers (manual, no verification, messy)
- Driving around (time-consuming, no fun)
- Store relationships (unfair, doesn't scale)
- BrickSeek-style inventory checkers (inaccurate, no ground truth, not a game)

## Solution

**BottleQuest** - An AI-native game platform that makes hunting addictive and scalable:

### Core Game Loops
1. **Moment-of-Use Loop** (primary):
   - Open app while out → Scan Mode
   - Point camera at shelf → app detects bottles/items in real-time
   - Highlights items on your Wish Dex, rare/featured items, quest completions
   - Tap "Log Sighting" (1-tap, minimal friction)
   - Earn XP, badges, streak progress, "next hunt" recommendations
   - Feed/Radar updates; shareable card generated ("Found Pappy 15! +250 XP")

2. **Curation Loop** (friend console):
   - Approve new items, fix matches, tag rarity/flavor/category
   - Define "similar items" edges for personalization
   - Create quest templates and event drops
   - Every correction feeds the Identity Graph and improves scan accuracy

3. **Trust / Anti-Spam Loop** (ops console):
   - Low confidence sightings → review queue
   - Repeated reliable users gain trust scores
   - Spam patterns detected and throttled
   - System learns which sources/users are reliable per context

### The Real Moat: AI-Native Infrastructure

Think of BottleQuest as an **AI mapping system for rare items in the physical world**.

#### 1. Item Identity Graph (Canonicalization at Scale)
**Goal:** Map messy observations (partial barcode, blurry photo, user typo, variant packaging) → canonical item with variant attributes and confidence score.

- **Canonical Item** = "Brand + Expression" (e.g., "Buffalo Trace Bourbon")
- **Variant** = size/proof/year/batch/store pick/packaging/label revision
- **Outputs:** `canonical_item_id`, `variant_id`, `confidence` (0-1), `evidence` (what matched)

This becomes the cross-vertical engine (swap liquor schema for "sneaker model + colorway + size + release").

#### 2. Multi-modal Recognition Pipeline (Progressive)
**Phase A (Weekend MVP):** Barcode scan + OCR + embedding match to catalog

**Phase B (2-4 weeks):** Vision embeddings for front-label recognition, object detection for "bottle-ish rectangles," better OCR on key regions

**Phase C (6-12 weeks):** Multi-object detection + tracking across frames; shelf video → inventory map from 5-second pan

#### 3. Sighting Trust + Evidence Ledger (Anti-Garbage)
A consumer map is worthless if it's spammy. We need:
- **Confidence scoring** for each sighting
- **Dedup** across time + nearby tiles
- **User trust scores** that evolve
- **Anomaly detection** (someone spamming rare sightings)

**Evidence ladder** (optional, keeps UX fun):
- Level 0: tap-only (low confidence)
- Level 1: +photo (medium)
- Level 2: +short video quest (high)
- Level 3: verified scout (highest)

#### 4. Personalization + "Next Hunt" Engine
- Users build preference embedding from likes, kept items, dwell time, optional swipe taste cards
- Reco engine outputs: items likely to be enjoyed AND recently sighted nearby, quests matching profile

#### 5. Cross-Vertical "Quest Pack" Framework
To scale beyond liquor:
- Keep infra generic (ItemDex + Scan + Sighting + Quest + Trust)
- Add new verticals as **Quest Packs**: SneakerQuest, CardQuest, VintageQuest
- Each pack ships: starter catalog/taxonomy, rarity tiers, quest templates, similarity rules

## Target User

**Primary:**
- **Liquor hunters** (bourbon/whiskey/mezcal collectors) who love the hunt but hate the manual grind
- Age: 25-50, mostly male, willing to drive 30+ min for rare bottles
- Currently active in Facebook groups, Discord servers, calling stores
- Willing to share sightings IF they get XP/status/rewards

**Secondary (future verticals):**
- **Sneakerheads** hunting limited releases and rare colorways
- **Card collectors** hunting valuable vintage/graded cards
- **Vintage hunters** for rare clothing/items
- **General "drop culture" enthusiasts** who love limited/exclusive items

**Buyer:** Individual consumers (B2C game with in-app purchases, sponsored quests)

## Why Now

1. **"Drop culture" is mainstream** - Pokémon Go proved location-based hunting works; StockX/GOAT proved collectible marketplaces work
2. **AI vision is cheap enough** - Multi-modal embeddings + OCR + object detection are commoditized (Vertex AI, Replicate, Roboflow)
3. **No one owns "rare item hunting"** - Pokémon Go is fictional creatures; this is real items people actually want
4. **Liquor market is hot** - Bourbon/whiskey collecting exploded during COVID; $83B U.S. spirits market
5. **Personalization is table stakes** - Users expect "what should I hunt next?" to be AI-powered
6. **Cross-vertical platform play** - Build once for liquor, extend to sneakers/cards/vintage with Quest Packs

## Core Value Proposition

Unlike manual hunting or Facebook groups:
- **Addictive game loop** - XP, badges, streaks, quests, leaderboards make hunting fun
- **Real-time shared intelligence** - Crowd-sourced sightings with trust scoring (not spam)
- **Personalized recommendations** - "What should I hunt next near me right now?"
- **Works across verticals** - Build identity + trust infra once, apply to any rare item
- **AI-native from day one** - The moat is the Item Identity Graph + Trust Scoring + Personalization, not just the app

## Technology Approach

### System Architecture
```
[Mobile App] → API Gateway → Auth Service
                               ↓
            ┌──────────────────┼──────────────────┐
            ↓                  ↓                  ↓
      Item Service      Scan Service       Sighting Service
      (catalog +        (OCR/vision/       (tiles + dedupe +
       identity)         embeddings)        trust scoring)
            ↓                  ↓                  ↓
      Quest Service     Social Service     Media Service
      (progress +       (friends/squads/   (S3/GCS for
       rewards)          leaderboards)      images/videos)
                               ↓
                        ML Pipeline
                        (training + eval + deployment)
```

### Tech Stack
- **Mobile:** React Native (iOS/Android)
- **Backend:** Python (FastAPI), Node.js (API Gateway)
- **Database:** Postgres (core relational) + pgvector (embeddings + ANN)
- **Geo:** PostGIS (tile-based location privacy)
- **Cache/Queue:** Redis (rate limits, queues, leaderboards)
- **Storage:** S3/GCS (media)
- **Workers:** Celery/Temporal (background jobs)
- **ML:** Pluggable detector interface (start with COCO vehicle detector-style approach, fine-tune later)

### Data Model (Vertical-Agnostic)

**Core Tables:**
- `users(id, handle, trust_score, home_tile, settings...)`
- `items(id, vertical, name, brand, category, rarity_tier, tags[], ...)`
- `item_variants(id, item_id, size_ml, proof, year, batch_code, store_pick, upc, ...)`
- `item_media(id, item_id, variant_id?, type, url, embedding_vec, source, ...)`
- `scans(id, user_id, tile_id, timestamp, media_url, ocr_text, barcode, ...)`
- `scan_matches(scan_id, item_id, variant_id?, confidence, method, evidence_json)`
- `sightings(id, user_id, tile_id, item_id, variant_id?, timestamp, confidence, evidence_level, dedupe_group_id, ...)`
- `tiles(tile_id, last_active_at, hot_score, rarity_score, ...)`
- `quests(id, season_id, name, rules_json, rewards_json, active_window, ...)`
- `quest_progress(user_id, quest_id, progress_json, completed_at?)`
- `badges(id, name, rule_json, rarity)` + `user_badges(user_id, badge_id, earned_at)`
- `friends(user_id, friend_id, status)` + `squads(id, name, owner_id)` + `squad_members(squad_id, user_id, role)`

**Vertical Extension via `item_variants.attributes_json`:**
- Liquor: `{proof: 90, size_ml: 750, year: 2023, batch: "B523", store_pick: true}`
- Sneakers: `{size: 10.5, colorway: "Chicago", release_date: "2023-11-15"}`
- Cards: `{set: "1989 Upper Deck", grade: "PSG 9", player: "Ken Griffey Jr."}`

### Recognition + Matching Pipeline

**Inputs:** image/video frames, OCR text blocks, barcode/UPC, context (tile, time, user trust)

**Matching Pipeline:**
1. If UPC present → exact match to `item_variants.upc`
2. OCR normalize → candidate retrieval via text search
3. Compute embedding of image (and optionally OCR text)
4. ANN search in pgvector → top-K candidate items
5. Re-rank candidates using learned model: OCR overlap, brand match, visual similarity, rarity priors, geo priors
6. Output top match + confidence

**Human-in-the-Loop Correction:**
- If confidence < threshold: show user "Is it A / B / C / none?"
- Store as labeled example
- Curator console handles "unknown item" creation, merge/split duplicates

### Sighting Scoring, Dedupe, Trust

**Confidence Score Components:**
```
final_conf = sigmoid(
  w1 * match_confidence +
  w2 * evidence_level +
  w3 * user_trust_score +
  w4 * tile_prior -
  w5 * anomaly_penalty
)
```

**Dedupe Strategy:**
Group sightings into `dedupe_group` if:
- Same `item_id` (or variant)
- Same `tile_id` (or neighboring tile)
- Within time window (6-24 hours depending on rarity)

**Abuse Prevention:**
- Rate limit sightings per hour/day
- Require scans for rare tier (no manual entry)
- Shadow-ban spammers (their sightings don't influence public heat)
- Device fingerprint + account age weighting

### Quests (The Content Engine)

**Quest Rule Language (Data-Driven JSON):**
Quests should be definable without code:
- "Scan 10 bottles this week"
- "Log 3 sightings of wheated bourbon"
- "Find a store pick"
- "Complete a 'Smoke' set (peated/mezcal/smoked cocktail)"

Each quest references item tags/attributes: category tags, rarity tiers, brand families, special flags

**Reward Structure:** XP + badge + cosmetic unlock + leaderboard points + occasional "loot drop"

### Location Privacy Design (Important)

- Use **tile geohashes** (~1km tiles) for public display
- Store raw GPS only if needed, keep private, expire quickly
- Never show exact store pin by default
- "Nearby" = tiles, not addresses

## MVP Scope

### In Scope (Weekend V0 - Fun Demo)
- ✅ Mobile app skeleton (Home, Scan, Item, Quests, Profile screens)
- ✅ Barcode + OCR scanning (no fancy CV yet)
- ✅ Static starter catalog (50-200 items)
- ✅ Log sightings to tiles + leaderboard points
- ✅ 5 quests + 10 badges
- ✅ Share card generator (image with item + XP + badge)

### In Scope (V1 - 2-4 weeks)
- ✅ Vector embeddings for images + text; ANN matching via pgvector
- ✅ Curator console for approvals + merges + tags
- ✅ Dedupe + trust scoring
- ✅ Radar heat map tiles (Mapbox/Leaflet)
- ✅ Squad leaderboards + shared quests
- ✅ Basic quest rule engine (JSON-driven)

### In Scope (V2 - 6-12 weeks)
- ✅ Shelf object detection (multi-item from single photo/video)
- ✅ Better variant resolution (year/batch/store pick detection)
- ✅ Sponsored quests + cosmetics store
- ✅ More vertical packs (SneakerQuest, CardQuest)
- ✅ Advanced personalization ("Next Hunt" engine)

### Out of Scope (V1)
- ❌ Multi-city configs (single city for demo)
- ❌ Real-time video streaming (still images only)
- ❌ Advanced ML training pipeline (use pre-trained models)
- ❌ In-app purchases / real payments (coming in V2)
- ❌ Public API for third parties

## Monetization (NO Subscription Required)

Make money like a game + a platform, not SaaS:

### Best Fits for This Vibe

1. **Sponsored Quest Drops (Brand/Retailer/Creator)**
   - "Week of Peated Smoke" quest pack sponsored by a brand (clearly labeled)
   - Payout: campaign fee (CPM-ish) + completions

2. **Cosmetics / One-Time Purchases**
   - Avatar frames, rare badge skins, themed Dex pages
   - "Quest Pass" for special event weekend (one-time, like Fortnite Battle Pass)

3. **Affiliate on Non-Regulated Gear**
   - Glassware, bitters, books, bar tools, display cases
   - Avoids liquor resale regulation headaches

4. **B2B "Scout Insights"**
   - Aggregated trend dashboards for retailers/brands (no exact store location; privacy-preserving)
   - E.g., "heat map of interest and sightings by metro + week"

5. **API Licensing (Later)**
   - License Item Identity Graph / recognition / trust scoring to other apps

## Roles + Internal Tooling ("Make Tools for My Friends")

### Curator Console (Tinder-Like)
**Purpose:** Keep the catalog clean + fun + consistent

**Key Workflows:**
- Approve new item suggestions from users
- Merge duplicates / split variants
- Tag rarity + flavor + category
- Define "Similar items" edges (for personalization + quests)
- Create quest templates and event drops
- Review "unknown scans" queue

**UX Ideas:**
- Swipe to accept/reject suggested matches
- 2-choice labeling ("closer to A or B?") to train embeddings

### Ops/Data Console
**Purpose:** Trust, abuse prevention, data quality

**Workflows:**
- Sighting review queue (low confidence, high rarity)
- User trust scoring dashboard + bans/throttles
- Source reliability stats (which tiles get spam)
- Model performance monitoring (precision/recall by category)
- Content moderation (photos that violate rules)

### Finance/SQL Console
**Purpose:** Run the economy like a game studio

**Workflows:**
- XP economy tuning (reward curves, quest difficulty)
- Retention cohorts (D1/D7/D30)
- Virality: shares → installs
- Monetization dashboards (sponsored quests, cosmetics)
- Fraud patterns (alt accounts farming rewards)
- A/B testing knobs (feature flags)

## Technical Decisions

### Critical Constraints
1. **Mobile-first** - iOS + Android via React Native
2. **Privacy-first location** - Tile geohashes (~1km), not exact pins
3. **Trust-first sightings** - Confidence scoring + dedupe + user trust required
4. **AI-native identity** - Item Identity Graph is core, not an add-on
5. **Vertical-agnostic data model** - Must work for liquor/sneakers/cards/vintage with Quest Packs

### Platform Requirements
- Postgres 14+ with pgvector extension + PostGIS
- Redis 6+ (or Postgres-based job queue)
- Python 3.10+
- Node 18+
- React Native CLI + Expo
- S3/GCS for media storage

### Integration Points
- **Vision APIs:** Vertex AI, Replicate, Roboflow (pluggable)
- **OCR:** Google Cloud Vision, Textract, Tesseract (fallback)
- **Maps:** Mapbox GL JS (requires key) or Leaflet (open)
- **Push Notifications:** Firebase Cloud Messaging
- **Analytics:** Mixpanel, Amplitude, or self-hosted (PostHog)

## Key Assumptions & Risks

### Assumptions
1. **Collectors want gamification** - Hunting is fun IF it's a game with progression
2. **Crowdsourcing works with trust** - Users will share sightings IF rewarded and spam is controlled
3. **AI recognition is "good enough"** - Multi-modal embeddings can ID items at 80%+ accuracy for common cases
4. **Privacy-preserving tiles are acceptable** - Users OK with coarse location (1km tiles) for public view
5. **Quest Packs can scale** - Same infra works for sneakers/cards/vintage with different catalogs

### Risks & Mitigations

**Risk:** Item recognition too inaccurate (high false positives/negatives)
**Mitigation:** Pluggable model interface, human-in-the-loop correction, confidence thresholds, curator review queue

**Risk:** Spam/abuse ruins the map
**Mitigation:** Trust scoring, rate limits, dedupe, anomaly detection, shadow bans, manual review queue

**Risk:** Low adoption (collectors don't want to share)
**Mitigation:** XP/badges/leaderboards incentivize sharing; private "ghost mode" for power users who want to lurk

**Risk:** Regulatory issues with liquor (promoting sales, age verification)
**Mitigation:** No purchases in-app; just sighting/hunting game; 21+ age gate; terms prohibit resale coordination

**Risk:** Scale beyond liquor fails (sneakers/cards don't work)
**Mitigation:** Validate liquor first; Quest Pack framework makes adding verticals low-risk; test with one vertical at a time

**Risk:** Monetization doesn't work (users expect free forever)
**Mitigation:** Free core game loop; cosmetics + sponsored quests are optional; B2B insights have clear value

**Risk:** Performance issues at scale (recognition pipeline is slow)
**Mitigation:** Batch processing, async workers, caching, horizontal scaling, progressive rollout

## Success Metrics

### MVP Success (V0)
- ✅ 50+ users actively scanning in first month
- ✅ 500+ sightings logged
- ✅ 10+ quests completed per user on average
- ✅ 20%+ users share a sighting card on social
- ✅ Recognition accuracy >70% on common items

### V1 Success (2-4 months)
- 1,000+ Weekly Active Hunters (WAH)
- 10+ scans per WAH per week
- 50%+ quest completion rate
- 30%+ share rate (viral growth)
- 85%+ sightings above confidence threshold
- <5% spam rate
- If monetizing: 10+ sponsored quests with 500+ completions each

### V2 Success (6-12 months)
- 10,000+ WAH
- 3+ verticals live (liquor + sneakers + cards)
- $10K+ MRR (sponsored quests + cosmetics)
- 90%+ recognition accuracy
- Quest Packs sold to brands/retailers

## Open Questions

### Technical
1. **Detection Model:** Pre-trained COCO-style or fine-tune on liquor bottles from day one?
2. **Queue Choice:** Redis Streams vs Celery vs Postgres-based (pg_job)?
3. **Map Library:** Mapbox (costs $) vs Leaflet (free) vs deck.gl?
4. **Anomaly Method:** Z-score sufficient or use EWMA/Prophet for trust scoring?
5. **Real-time Requirements:** Is batch processing OK or need <1min scan → result latency?

### Product
6. **Age Gate:** How strict should 21+ verification be? (Just birthdate input vs ID scan?)
7. **Liquor Laws:** Can we gamify sighting hunting without violating promotion laws?
8. **Privacy Complaints:** Will users complain about public tile-based sightings revealing their location?
9. **Curator Incentives:** How do we recruit/retain curators? Pay them? Give them special status/badges?
10. **Cross-Vertical Timing:** Launch liquor-only and prove it, or launch 2 verticals simultaneously?

### Business
11. **Sponsored Quest Pricing:** What should brands pay per quest campaign? CPM model or flat fee?
12. **Cosmetics Willingness-to-Pay:** Will users actually pay $2-5 for avatar frames/badges?
13. **B2B Insights:** Will retailers/brands pay for aggregated trend data? What's the price point?
14. **Regulation Risk:** Could liquor boards shut this down for promoting alcohol sales?
15. **Competitive Moat:** How defensible is the Item Identity Graph? Could Meta/Google easily replicate?

## Repository Structure
```
bottlequest/
├── README.md
├── docs/
│   ├── 00_overview.md
│   ├── 01_item_identity_graph.md
│   ├── 02_trust_and_sighting_scoring.md
│   ├── 03_quest_rule_dsl.md
│   ├── 04_curator_console_guide.md
│   ├── 05_liquor_taxonomy.md
│   └── 06_ops_runbook.md
├── infra/
│   ├── docker-compose.yml
│   └── k8s/ (optional, for prod)
├── mobile/
│   ├── ios/
│   ├── android/
│   ├── src/
│   │   ├── screens/ (Home, Scan, Item, Quests, Profile)
│   │   ├── components/
│   │   ├── services/ (API client, camera, location)
│   │   └── state/ (Redux/Zustand)
│   └── package.json
├── api/
│   ├── gateway/ (Node/Express API gateway)
│   ├── services/
│   │   ├── auth/
│   │   ├── items/ (catalog + identity graph)
│   │   ├── scan/ (OCR + vision + matching)
│   │   ├── sightings/ (tiles + dedupe + trust)
│   │   ├── quests/ (rules engine + progress)
│   │   ├── social/ (friends + squads + leaderboards)
│   │   └── media/ (S3/GCS uploads)
│   └── shared/ (types, utils)
├── workers/
│   ├── celery_config.py
│   ├── tasks/ (background jobs: embeddings, dedupe, rollups)
│   └── Dockerfile
├── ml/
│   ├── recognition/
│   │   ├── ocr_pipeline.py
│   │   ├── vision_embeddings.py
│   │   ├── object_detection.py (shelf multi-item)
│   │   └── model_interface.md
│   ├── trust_scoring/
│   │   ├── confidence_model.py
│   │   ├── anomaly_detection.py
│   │   └── user_trust_scoring.py
│   └── training_stub/ (fine-tuning workflows for later)
├── console-curation/
│   ├── web/ (Next.js app)
│   ├── components/ (Tinder-style swipe UI, tag editor, quest builder)
│   └── package.json
├── console-ops-finance/
│   ├── web/ (Next.js app)
│   ├── dashboards/ (trust scores, retention cohorts, fraud detection)
│   └── package.json
├── db/
│   ├── migrations/ (Postgres + PostGIS + pgvector)
│   ├── seed/ (starter catalog: 200 liquor items, quest templates, badges)
│   └── schema.sql
└── scripts/
    ├── dev_bootstrap.sh
    ├── load_taxonomy.py (liquor starter catalog)
    ├── generate_quest_pack.py (templating for new verticals)
    └── synthetic_data_generator.py (for demo/testing)
```

## Next Steps

### Immediate (Pre-MVP)
1. **Validate demand** - Interview 20 liquor hunters; confirm game loop resonates
2. **Select city** - Pick 1 metro for MVP (Austin? NYC? SF?)
3. **Build starter catalog** - Manual curation of 200 common + rare bottles with images
4. **Legal review** - Confirm age gate + "sighting only" approach complies with liquor laws
5. **Tech stack decisions** - Lock in map library, vision API, queue system

### Weekend V0 Development
1. Set up monorepo structure
2. Build mobile app skeleton (5 screens: Home, Scan, Item, Quests, Profile)
3. Implement barcode + OCR scanning (Google Vision API)
4. Build simple catalog matching (string + embedding search)
5. Tile-based sighting storage + display on map
6. Basic XP + badges + leaderboard
7. Share card generator (image with item + XP)
8. Deploy to TestFlight/Play Store beta

### V1 Development (2-4 weeks)
1. Build Item Identity Graph (canonical items + variants)
2. Implement multi-modal recognition (vision embeddings + ANN search)
3. Add confidence scoring + dedupe + trust scoring
4. Build curator console (Tinder-style approval flow)
5. Implement quest rule engine (JSON-driven)
6. Add squad system + squad leaderboards
7. Radar heat map with tile aggregation
8. User testing with 50 beta users
9. Iterate based on feedback

### Post-V1 (6-12 months)
1. Launch sponsored quest pilot (1-2 brands)
2. Add cosmetics store (avatar frames, badges)
3. Build SneakerQuest pack (test cross-vertical)
4. Improve recognition accuracy (fine-tune models)
5. Add B2B insights dashboard for brands
6. Scale to 3-5 cities
7. Plan v2 features based on data

## Related Ideas

- **IDEA-020:** MOAR Adoption Dashboard (analytics pattern for adoption tracking)
- **IDEA-034:** TheGeneral AI Chat Platform (multi-tenant chatbot infra - could power quest hint bots)
- **IDEA-101:** Drop-Alert Platform (availability/openings watcher - similar "notify when rare thing appears" mechanic)
- **IDEA-112:** Unmet Need Radar (mining complaint streams - could power "what should we hunt next?" suggestions)
- **IDEA-162:** Fleet Signals Lab (privacy-first location aggregation pattern)

## Notes

### Why This Could Be a Real Business
- **Unit economics are game-like, not SaaS** - Low COGS (just infra + recognition API), high engagement → viral growth
- **The moat is the data flywheel** - Item Identity Graph + Trust Scoring + Sightings improve over time; harder to replicate than app UI
- **Cross-vertical expansion is clear** - Liquor proves the game loop; sneakers/cards/vintage are obvious next steps with Quest Packs
- **Monetization doesn't require subscription** - Sponsored quests + cosmetics + B2B insights are all high-margin
- **AI-native from day one** - Not a chatbot wrapper; real infra for multi-modal recognition + canonicalization + trust

### Why This Is Hard
- **Recognition accuracy must be "good enough"** - If users get frustrated by bad matches, game loop breaks
- **Trust/spam is existential** - A spammy map is worthless; must nail this from V1
- **Regulatory risk with liquor** - Age gate + compliance + "we don't facilitate sales" positioning required
- **Crowdsourcing cold start** - Need critical mass in each city; chicken-and-egg problem
- **Competitor risk** - Meta/Google could add "scan shelf for items" to their apps

### What Makes This Different from Pokémon Go
- **Real items people want** - Not fictional creatures; actual valuable collectibles
- **Supply-side value** - Retailers/brands get insights from sighting data
- **Cross-vertical platform** - Not just one category; works for any rare/limited item
- **AI moat** - Item Identity Graph + Trust Scoring are real technical challenges, not just game design

---

**Status:** Concept → Ready for demand validation
**Confidence:** Very High (clear market need, proven game loop, real AI moat, strong monetization)
**Risk Level:** Medium-High (recognition accuracy, trust/spam, liquor regulation, cold start)

🤖 Generated with Idea Factory
