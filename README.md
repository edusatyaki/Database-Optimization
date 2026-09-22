# Database Optimization — animated infographic deck

An infographic, motion-graphics retelling of **DBMS Lecture 13 · Database
Optimization**, built to be presented. Every concept from the source deck is
here, but the static diagrams are replaced with animated SVG scenes that build
themselves one step at a time as you talk.

## Run it

```bash
python3 -m http.server 8104 --directory db-optimization
```

Then open <http://localhost:8104>. Also registered in `.claude/launch.json` as
`db-optimization`.

## Driving it

The whole deck runs on the arrow keys.

| Key | Action |
|-----|--------|
| `→` / `Space` / click | next step — advances the animation, then the slide |
| `←` | previous step |
| `↓` / `↑` | skip to next / previous slide |
| `S` | speaker notes drawer |
| `O` | run of show — jump to any slide |
| `T` | light / dark theme |
| `F` | full screen |
| `+` / `−` / `0` | type size, for the room you are in |
| `Home` / `End` | first / last slide |

32 slides, 120 steps. Notes are written for speaking aloud, one per step.

## The judgement and real-world track

The source lecture explains the mechanisms. These five slides answer the
question students actually ask next — *where would we really use this?*

| Slide | What it gives them |
|-------|--------------------|
| **The escalation ladder** | Six rungs from "tune the query" (hours, reversible) to "shard" (months, one-way door). Most "we need to shard" problems are a missing index. |
| **When to reach for sharding** | Three conditions that must all hold — writes are the wall, the data outgrew the machine, you have a natural key — plus the four cases where sharding is the wrong answer. |
| **When to reach for partitioning** | Three signals: one table dominates, a shared filter key (almost always time), scheduled deletion. Plus where it buys nothing. |
| **In the wild** | Publicly documented architectures — Instagram (Postgres by user ID), Notion (by workspace), Shopify pods, Vitess out of YouTube, TimescaleDB. The common thread: they all shard on the tenant. |
| **Putting it together** | The ladder applied to ShopFast: partition first, then replicas, then shard by `customer_id` only if writes are still the wall — and explicitly what *not* to do. |

## What is animated

| Scene | Motion |
|-------|--------|
| The five levels | A building assembles from the foundation up; each level lights when you name it |
| Load growth | The latency curve draws itself and bends into the timeout |
| Scale up | The server box grows through four sizes and hits the ceiling |
| Scale out | One box splits into five, each at full speed |
| Shard split | 1B rows fan out to ten servers by `customer_id` |
| Anatomy | A query packet flows app → router → the one shard that holds the key |
| Range sharding | A contiguous range resolves to a single shard |
| Hash sharding | `id mod 4` computes on screen and the rows scatter to four shards |
| Directory sharding | The lookup row highlights and points at its shard |
| Hotspots | Four bars grow; the newest one turns red and pulses at 100% |
| Cross-shard query | A query fans out to every shard and the partials merge back |
| Re-sharding | Three shards become four and existing rows migrate |
| Full table scan | 60 monthly cells all turn hot, then all but one go quiet |
| Pruning | The query beam lights one partition; the others dim and are skipped |
| Sharding vs partitioning | Many machines beside one machine, built side by side |

Motion respects `prefers-reduced-motion` — with that setting on, everything
appears instantly instead of animating.

## Sizing it for the room

Type is a share of **screen height**, not fixed pixels, so the deck keeps its
physical size whatever resolution the projector runs at — the root is `3.5vh`,
so body copy lands around 30px at 1080p. `+` / `−` adjust live and the setting is
remembered per machine. Checked for overflow across all 120 steps, in both themes.

## Design

Built to match the **Normalisation Lab** (`edusatyaki/Normalization`) — navy
masthead under a gold rule, light blue paper (`#f4f8fb`), white cards with a
coloured left rail, solid navy blocks carrying a gold eyebrow for definitions,
and blue letterspaced eyebrow labels. Semantic colours follow the Lab too: red
`#b9364b` for hotspots and pitfalls, green `#147a58` for benefits, cyan for
secondary emphasis.

**Type is Arial throughout**, matching the other teaching sites in this
workspace. The display / editorial / UI roles are still separate design tokens,
so the hierarchy is carried by size, weight and colour rather than by three
typefaces. SQL and query output stay in JetBrains Mono, where column alignment
is load-bearing — the only face fetched from Google Fonts.

Every slide carries an italic kicker beside the title ("one shard saturated, nine
idle"), and the five levels each get a stroked line-art icon in a tinted tile —
no emoji, so it holds up on a projector.

Dark mode is a deep navy drawn from the same family rather than a neutral black,
so the palette keeps its character in a dim hall. Press `T`.

## Source

`DBMS Lecture 13.pdf` — 46 slides. Structure preserved: the hook, the five
levels, sharding (need → definition → anatomy → three strategies → pitfalls →
checkpoint), partitioning (need → definition → vs sharding → three types →
pruning → benefits → limitations → checkpoint), and the hand-off to the
Transaction Level.

The Transaction Level lecture this deck ends on is built out separately as the
**Transaction Cafe** practical and deck.
