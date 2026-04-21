# Constitution: VS Code AI Feedback Analyzer

## Target audience

**Primary:** VS Code AI product managers deciding what to build next quarter.

**Secondary:** Engineering leads validating pain points before scoping fixes; designers sourcing qualitative quotes for research readouts.

They land on this tool when they need to answer: *"What are users struggling with right now, and who is struggling most?"*

## Vision statement

Turn scattered user feedback into a single view that tells the product team, in under 30 seconds, **what to fix, what to invest in, and who to talk to.**

Three guiding principles:

1. **Insight-first, not data-first** — surface conclusions, not raw rows.
2. **Feature-level granularity** — product decisions happen at the feature level (Agent Mode, Chat Edit, Next Edit Suggestions), so the UI must too.
3. **Evidence on demand** — every chart links to the quotes behind it. PMs need to cite sources.

## Tech stack

Stay with the provided Next.js starter and add two lightweight libraries.

| Layer | Choice | Why |
|---|---|---|
| Framework | Next.js 15 (already in repo) | Zero setup cost. App Router. |
| Language | TypeScript (already configured) | Catches errors before they waste interview time. |
| Styling | Tailwind CSS (already configured) | Utility classes = fast functional UI. |
| Data layer | CSV parsed at build time via `papaparse` | No database needed. 250 rows fits in memory. |
| Charts | `recharts` | Drop-in React components, minimal config. |
| State | React `useState` / URL search params | No Redux/Zustand needed at this scale. |
| Deployment | Local `npm run dev` | Interview is a demo, not a ship. |

**Deliberately excluded from Phase 1:** database, auth, LLM calls, backend API routes, testing framework. Each adds setup time without adding insight.

## Roadmap

Each session is **self-contained**: starts from a known state, ends at a demoable state, and carries no hidden context. A fresh Claude conversation can be opened per session using the session brief.

### Session 1 — MVP for the interview (30 min)

**Starting state:** Fresh Next.js starter, CSV at repo root.

**Goal:** One dashboard page that answers "what's the state of VS Code AI feedback?"

**Scope:**

1. Parse the CSV once at server startup, expose as typed data.
2. Top-of-page summary cards: overall NPS, avg satisfaction, response count.
3. Feature ratings chart: horizontal bar chart of the 7 per-feature average ratings — instantly shows the weakest feature.
4. Sentiment/theme table: the open-text `What_Dont_You_Like` column grouped by simple keyword rules into 5-6 themes (performance, accuracy, workflow interruption, configuration, model choice, other).
5. Filter by user type (Professional / Student / Hobbyist) — proves the tool supports segmented decisions.

**Out of scope:** AI-powered clustering, multi-source data, trends over time, authentication.

**Demo narrative:** "Agent Mode is highest-rated but open-text complaints cluster on 'workflow interruption' — that's where we should invest polish, not new features."

### Session 2 — Smarter theme extraction (60-90 min)

**Starting state:** Session 1 merged.

**Goal:** Replace keyword rules with real theme extraction.

- Call an LLM (Claude / OpenAI) to cluster open-text responses into emergent themes.
- Cache results to disk so the UI stays fast.
- Add a "drill into theme" view showing the quotes behind each cluster.

### Session 3 — Multi-source ingestion (half day)

**Starting state:** Sessions 1-2 merged.

**Goal:** Pull in GitHub issues from `microsoft/vscode` tagged `area:copilot`.

- Add a GitHub Issues data source via their REST API.
- Unify schema: `{source, text, sentiment, theme, timestamp}`.
- Add source filter to the dashboard.

### Session 4 — Trends over time (half day)

**Starting state:** Session 3 merged with ≥2 sources.

**Goal:** Show how themes are moving week-over-week.

- Time-series line chart per theme.
- Spike detection: "workflow interruption complaints up 40% this week."
- Date range picker.

### Session 5 — Hacker News + insights layer (full day)

**Starting state:** Session 4 merged.

**Goal:** Add HN as a source + an auto-generated weekly insights page the PM can paste into a review deck.

- HN Algolia API ingestion.
- Weekly insight digest: top theme, top quote, biggest mover, most-upvoted HN thread.

### Session 6+ — Productionization

Auth, persistent database, saved views, shareable URLs, alerting on theme spikes, Slack digest. Out of scope for a PM prototype.
