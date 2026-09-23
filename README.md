# Database Optimization — animated infographic deck

An infographic, motion-graphics retelling of a **DBMS Database Optimization**
session, built to be presented. Every concept from the source deck is
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

41 slides, 159 steps. Notes are written for speaking aloud, one per step.

## Vocabulary before mechanics

Both key words are defined in plain English **before** anything uses them.
Chapter 1 closes on *Cut the data up — but where?*, which says only this:

> **Sharding** — cut the data up and put the pieces on **different machines**.
> You reach for it when one machine is not enough.
>
> **Partitioning** — cut one big table up and keep the pieces on the **same
> machine**. You reach for it when one table is too big to scan.
>
> Machines versus tables. That is the whole difference.

The five-level map that follows names both as techniques, so it has to come
second. The title slide's stack withholds its technique column for the same
reason — it would otherwise put both words on screen on slide one, before
either has been explained.

## Sharding vs. partitioning

The one-line version: **sharding splits rows across machines; partitioning
splits one table into pieces inside a single machine.** Chapter 6 lays this out
as its own slide — *The differences, row by row* — built one row at a time, once
both mechanisms have been shown.

| | Sharding | Partitioning |
|---|---|---|
| **What gets split** | the data, across independent database instances | one table, into child tables |
| **Where the pieces live** | different machines | the same machine, the same database |
| **You reach for it when** | one machine is not enough — writes, disk, throughput | one table is too big to scan |
| **Who routes the query** | the **application**, or a proxy — it must know the shard key | the **planner** — partition pruning; the app just queries the parent |
| **Joins across the pieces** | not possible in SQL; you merge in application code | ordinary SQL, the planner handles it |
| **Transactions across pieces** | no single ACID boundary — distributed, or give it up | one instance, so unchanged |
| **Getting the key wrong costs** | re-sharding: migrating live data between machines | re-partitioning: expensive, but local |
| **Schema** | identical on every shard; the *rows* differ | one parent, many child tables |
| **Adds hardware** | yes — that is the point | no |

The routing row is the one that explains all the others. Sharding sends the query
**out of the database**, so the engine can no longer join, plan or transact
across the pieces — that is where cross-shard joins, distributed transactions
and re-sharding pain all come from. Partitioning keeps the query **inside** one
engine, so everything the database normally does for you still works.

The common error is to read this as a choice. It is not — a large system does
both: shard across ten servers, then partition the big table inside each one.

## Structure: it is a story

The deck runs as one continuous narrative rather than a taxonomy. Amazon starts
on a single machine, and every technique in the lecture arrives because
something in the story broke. Eight chapters, six of which open with a
full-width chapter card that states the situation, names the problem, and only
then turns to the fix.

| Ch | Chapter | The problem that opens it |
|----|---------|---------------------------|
| 1 | One machine | A 1995 bookstore, everything in one database — and the two words the lecture runs on. |
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

## The five levels, explained side by side

The levels slide is a split: the stack on the left, and a detail panel on the
right that changes as each level is highlighted. Every level answers the same
four questions, so students can compare them directly rather than hearing five
unrelated descriptions.

| | |
|---|---|
| **Owns** | what that layer is responsible for |
| **You see** | the symptom that tells you the problem lives here |
| **You do** | the fix |
| **It costs** | what you give up to get it |

A pill on each panel says when the course covers it — *today · chapter 3*,
*today · chapter 6*, *next lecture*, *a later lecture* — so the scope of this
lecture is visible rather than assumed. The last step swaps the panel for the
building analogy and the rule that follows from it: you cannot fix the
foundation by rearranging furniture, and you should never start at the
foundation.

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

The connectors are not decorative dashes — a query is a **packet that actually
travels** the path, so the room watches work move between components rather
than inferring it:

| Scene | Motion |
|-------|--------|
| Anatomy | A query rides app &rarr; router &rarr; the one shard holding its key |
| Cross-shard query | Packets fan out to all four shards in blue, then partial results ride back in red |
| Hash sharding | Each computed `id mod 4` sends its row to the shard it hashed to |
| Directory sharding | The looked-up entry travels to the shard it names |
| Partition pruning | The query only travels to the partition it matches; the others dim out |
| Shard split | Rows stream from the one table down into each of the ten servers |
| Re-sharding | Rows migrate from the old shard map to the new one |
| Full table scan | A scan head sweeps every cell, then one month lights up |
| Hotspots | Bars grow from the floor; the newest turns red and keeps pulsing |

Everything respects `prefers-reduced-motion` — with that on, packets are not
emitted at all and entrances resolve instantly.

## Structure of the animation

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

## Nothing off the page

A slide clips rather than scrolls, and an inner wrapper scales the step down if
it would not fit — so nothing is scrollable and nothing hangs off the screen.
Three things had to be true for that to actually work:

- The wrapper needs a **definite height**, or percentage heights inside it
  (the chart and stack rows) fall back to their intrinsic ratio.
- It must be measured **top-aligned**. A centred flex column splits its
  overflow above and below, and `scrollHeight` only counts what hangs below —
  so a centred measurement under-reports the true need by about half.
- It must carry **no transition on `transform`**. The fit clears and re-sets the
  scale several times per render; with a transition declared, each reset
  restarted it and the browser held the from-value, so the scale never rendered
  at all.

The rail is constrained the same way: the grid row is `minmax(0,1fr)` and both
columns clip internally, so the rail can never burst its cell and push the
progress bar off-screen. Below 820px of height it drops its key legend, and
below 600px it drops the wordmark, rather than overflowing.

Audited by walking every slide and asking whether any element's bounding box
falls outside the viewport — at 863px and at 620px of height, in both themes.

## No scrollbars, ever

A slide clips rather than scrolls, and an inner wrapper scales the step down if
it would not fit — so nothing is scrollable and nothing is silently cut off. At
the default size no step needs it; the fit exists as a guard for high zoom and
unusual aspect ratios. The notes drawer and run of show still scroll, with their
scrollbars hidden.

Slide titles are never clipped: the title block owns the full width of the
document column, and everything that used to compete with it for a header row
— identity, chapter position, step counter, key legend — lives in the rail.

## Sizing it for the room

Type scales with the **smaller** viewport dimension — `min(3.5vh, 2.08vw)` —
not with height alone. Height alone looks right on 16:9 and then fails on a
4:3 or 16:10 projector: the narrower screen wraps more text, the content grows
taller, and slides overflow. Tying the scale to whichever dimension is tighter
makes the deck adapt to the *shape* of the screen, not just its resolution.

The level detail panel sizes itself the same way and expresses its internals in
`em`, so it compacts as a unit on a short screen instead of forcing the global
auto-fit to shrink the entire slide.

Verified with real viewport resizes at 1024x768, 1280x800, 1512x982 and
1920x1080: no clipping on any of the 41 slides, and the auto-fit never reaches
its floor.


Type is a share of **screen height**, not fixed pixels, so the deck keeps its
physical size whatever resolution the projector runs at — the root is `3.5vh`,
so body copy lands around 30px at 1080p. `+` / `−` adjust live and the setting is
remembered per machine. Checked for overflow across all 159 steps, in both themes.

## Design

A **sketchnote**: cream paper, marker headings, highlighter ribbons and
hand-drawn boxes, rather than a corporate slide template.

- **Paper** `#FBF3E4` with two soft wash gradients, no flat white anywhere.
- **Caveat** for headings and big numerals, **Patrick Hand** for body and
  labels, JetBrains Mono kept for SQL where alignment matters.
- Slide titles sit on an **amber highlighter stripe**, drawn as a background
  gradient sized to the text so it hugs the words and is rotated half a degree
  off true.
- Boxes use the uneven-corner trick — `border-radius:255px 15px 225px 15px/
  15px 225px 15px 255px` — with a 2.5px ink border, an offset hard shadow and a
  fraction of a degree of rotation that alternates by position, so no two
  cards sit quite square.
- Diagrams are roughened by an SVG `feTurbulence` + `feDisplacementMap` filter,
  which makes straight edges wobble like pen on paper. Two strengths: a coarser
  one for shapes, a finer one for connector lines.
- The rail is the notebook margin — dashed rules, no fill, the current chapter
  boxed in highlighter.
- Real artefacts (the Amazon wordmark) are **taped in**: a white printout with
  washi-tape corners, rotated off true, captioned in hand. It keeps a crisp
  corporate mark from fighting the drawn page.

Colour is used semantically, not decoratively: **pencil blue** for machinery,
**amber** for emphasis, **flame** for hotspots and pitfalls, **leaf** for
benefits. Warm-toned dark mode keeps the character. Press `T`.

Contrast is checked against the paper rather than assumed: the fill colours
(flame, amber, leaf) have darker text-safe variants for anywhere they set type.

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

The Amazon wordmark appears on the three story slides — the opening, the growth
arc, and the Dynamo chapter — presented as a printout taped into the notebook,
so it reads as the subject of the case study rather than as the deck's own
identity. It is Amazon's trademark, used here to identify the company being
discussed.

This is a case study for teaching. The deck is not affiliated with, endorsed by,
or produced in association with Amazon.

## Source

The source deck — 46 slides. Structure preserved: the hook, the five
levels, sharding (need → definition → anatomy → three strategies → pitfalls →
checkpoint), partitioning (need → definition → vs sharding → three types →
pruning → benefits → limitations → checkpoint), and the hand-off to the
Transaction Level.

The Transaction Level lecture this deck ends on is built out separately as the
**Transaction Cafe** practical and deck.
