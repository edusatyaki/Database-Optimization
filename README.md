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
| `F` *(or the **Present** button)* | full screen |
| `+` / `−` / `0` | type size, for the room you are in |
| `Home` / `End` | first / last slide |

39 slides, 148 steps. Notes are written for speaking aloud, one per step.

## Structure: it is a story

The deck runs as one continuous narrative rather than a taxonomy. Amazon starts
on a single machine, and every technique in the lecture arrives because
something in the story broke. Eight chapters, six of which open with a
full-width chapter card that states the situation, names the problem, and only
then turns to the fix.

| Ch | Chapter | The problem that opens it |
|----|---------|---------------------------|
| 1 | One machine | A 1995 bookstore, everything in one database. |
| 2 | A map of where things break | Several things broke at once — each has a home. |
| 3 | The machine runs out | Reads spread to replicas; writes cannot. → **Sharding** |
| 4 | Deciding where each row lives | Which rows go where — the choice you cannot undo. |
| 5 | What sharding broke | A join, adding a machine, and even traffic — all stopped being free. |
| 6 | Still too big for one table | A tenth of a billion is still 150M rows. → **Partitioning** |
| 7 | Amazon's own answer | They solved it first, then published how. |
| 8 | What you should actually do | You know *how*. The harder question is *when*, and in what order. |

Each chapter card has four steps: the situation, the problem, why it hurts, and
the turn into the technique. The masthead always shows which chapter you are in,
and the run of show (`O`) marks the chapter cards in squid ink.

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

Coloured in **Amazon's palette** — squid-ink masthead (`#232F3E`) under an
Amazon-orange rule (`#FF9900`), Amazon page grey (`#EAEDED`) for the paper, teal
`#007185` for links and labels, price red `#B12704` for hotspots and pitfalls,
success green `#067D62` for benefits. Structure follows the Normalisation Lab
(`edusatyaki/Normalization`): white cards with a coloured left rail, solid
squid-ink blocks with a gold eyebrow for definitions, tight 12px radii.

Orange is used as a **fill only** — `#FF9900` on white is about 2.2:1 and fails
as body text, so small text uses squid ink or the teal.

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

## The worked example

The running example is **Amazon**, and the arc is its real one: a 1995 bookstore
on a single relational database, growing into a catalogue that no single machine
could serve. Growth figures on the hook slide are illustrative — the exact
internal numbers are not public — but two things in the deck are documented fact
and worth stating as such in class:

- Amazon ran its core systems on Oracle for years; in **2018** AWS announced the
  consumer business had shut down its last Oracle database, moving onto DynamoDB,
  Aurora, RDS and Redshift.
- The **2007 Dynamo paper** describes partitioning a key space across a ring of
  nodes with consistent hashing — the ancestor of DynamoDB, which is why every
  DynamoDB table must declare a partition key, and why a low-cardinality key
  still produces a hot partition today.

That second point is the spine of the lecture: managed infrastructure does not
remove the shard-key decision, it removes your ability to fix it later.

This is a case study for teaching. The deck carries Newton School branding and
is not affiliated with or endorsed by Amazon.

## Source

`DBMS Lecture 13.pdf` — 46 slides. Structure preserved: the hook, the five
levels, sharding (need → definition → anatomy → three strategies → pitfalls →
checkpoint), partitioning (need → definition → vs sharding → three types →
pruning → benefits → limitations → checkpoint), and the hand-off to the
Transaction Level.

The Transaction Level lecture this deck ends on is built out separately as the
**Transaction Cafe** practical and deck.
