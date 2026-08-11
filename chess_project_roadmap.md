# Project: Lichess Matchmaking Fairness Analysis
**Framing:** You're a data analyst at Lichess investigating matchmaking quality and player experience.
**Core question:** Does rating difference reliably predict game outcome, and where does matchmaking break down?

---

## Phase 1 — Data Prep (Python / pandas)
- [x] Load `chess.csv`, check shape, dtypes, nulls (you've mostly done this — confirm no surprises)
- [x] Convert `created_at` and `last_move_at` from epoch ms → readable datetime
- [x] Parse `increment_code` (e.g. "15+2") into two numeric columns: `base_time`, `increment` (done in working notebook; dead-end analysis, dropped from report)
- [x] Create `rating_diff` column = `white_rating - black_rating`
- [x] Create `rating_diff_abs` = absolute value of rating_diff (for "how mismatched" regardless of color)
- [x] Decide: filter to `rated == True` only? **Decision: keep both.** Rated vs. unrated comparison is now a supporting angle (see Phase 3) — document this reasoning in the notebook.
- [x] Split `opening_name` into two columns: `opening` (text before the colon, e.g. "Sicilian Defense") and `variation` (text after, e.g. "Najdorf Variation") — use `.str.split(':', n=1, expand=True)`, watch for rows with no colon (no named variation)
- [x] Strip leading/trailing whitespace on both new columns after splitting
- [x] Sanity check `winner` values (white/black/draw) and `victory_status` values (mate/resign/outoftime/draw) — confirm no typos/unexpected categories

## Phase 2 — Core EDA (answer the spine question)
- [x] Plot distribution of `rating_diff` — how close are typical matchups? (histogram)
- [x] Bucket `rating_diff` into bins (e.g. 0-50, 50-100, 100-200, 200-300, 300+) and calculate win rate per bin
- [x] Plot win rate vs. rating gap bucket — does the trend look "fair" (steadily increasing) or does it break down somewhere?
- [x] Find the threshold where predictions start being unreliable or where sample size gets too thin to trust
- [x] Check what % of total games fall into each bucket — is the "unfair" tail large or rare?

## Phase 3 — Supporting angles (deepen the story)
- [x] Cross-tab `victory_status` against rating gap buckets — do close games end more in resignation/mate, mismatched ones in timeout/abandon?
- [x] Look at `opening_ply` (book-move depth) — any correlation with game length (`turns`) or decisiveness?
- [x] For your most-played base `opening` values, calculate win rate by `variation` — this is the "which response works best against this opening" analysis that becomes your dashboard's core feature. Set a minimum game count threshold (e.g. 20+) so thin samples don't produce misleading win rates
- [x] Compare rated vs. unrated games: does win-rate-by-rating-gap pattern differ between the two? (checks if filtering would've mattered)
- [x] Compare game length (`turns`) between rated and unrated — do unrated games end shorter, e.g. via early resigns/aborts, suggesting lower engagement/seriousness?
- [x] Cross-check `victory_status` split (mate/resign/outoftime/draw) between rated and unrated — do unrated games show a higher share of early resignations?

## Phase 4 — Findings & Recommendation
- [x] Write 3-5 numbered findings, each with a specific number and a "so what" (see example from earlier: "Games with gap >300 pts → white wins 71%, but only 4% of games — investigate matchmaking queue tradeoffs for low-density rating bands")
- [x] Write one paragraph of honest limitations (e.g. no session/churn data, so "player experience" is inferred not proven)
- [x] Write a short intro paragraph reframing the project as platform analytics, not "chess fan EDA"

## Phase 5 — Packaging (Python project, v1)
- [x] Clean up notebook: remove dead cells, add markdown headers matching the phases above
- [x] Write README.md: business question at the top, then findings, then methodology, then limitations
- [ ] Push to GitHub with a clear repo name (not "chess-eda")

## Phase 6 — Dashboard version (later, once BI tool is ready)
- [ ] Decide Power BI or Tableau
- [ ] Recreate: win-rate-by-rating-gap chart, victory_status breakdown, rating distribution — as interactive filters
- [ ] Add a rating-band drilldown so a "stakeholder" could explore matchmaking fairness themselves
- [ ] Build the opening/variation matchup view: select a base `opening`, see win rates for each `variation` played against it — this is your "which opening do you struggle against" feature
- [ ] Publish live (Tableau Public / Power BI published report) and link it in the README

---

**How to use this:** Work top to bottom, one unchecked box at a time. Don't jump to Phase 4 findings before finishing Phase 2 — the findings should come *from* what you actually see in the data, not be written first and then justified.
