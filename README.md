# Lichess Matchmaking Fairness Analysis

As a data analyst at Lichess, I investigate **matchmaking quality and player experience**: does the rating system produce fair matchups, does a rating gap reliably predict who wins, and where does matchmaking break down?

**Core question:** Is rating difference a dependable predictor of game outcome — and where does that relationship weaken or fail?

Dataset: 20,058 Lichess games (`data/chess.csv`). Analysis in `notebooks/Lichess Matchmaking Fairness Analysis Report.ipynb` (clean narrative version of the full working notebook).

---

## Findings

**1. Rating gap is a strong, monotonic predictor of outcome — but close games are genuinely competitive.**
Higher-rated players win just 48.7% of games when the gap is within 34 points, rising steadily to 80.3% when the gap exceeds 284 points (quintile buckets, roughly 4,000 games each). *So what:* the matchmaker behaves the way an Elo system should — outcomes become more lopsided as mismatch grows, while tight matches stay near coin-flips, a healthy sign for competitive play.

**2. A real mismatch tail exists: roughly 1 in 5 games is effectively pre-decided.**
18.2% of all games are played with a rating gap above 300 points, and in the widest quintile the higher-rated player wins 80.3% of the time. *So what:* the platform should monitor whether that tail is driven by matchmaking-queue necessity (off-peak hours, thin rating bands, new-account variance) rather than being an intentional feature of the ladder.

**3. Mismatch changes who wins, not how games end.**
The victory_status mix is almost flat across rating-gap buckets: checkmate endings rise modestly from 30.4% to 35.1%, out-of-time endings dip from 8.5% to 7.3%, and resignation stays dominant (~55-56%) everywhere. *So what:* imbalanced matchups don't produce visibly broken games — their cost shows up as predictable, frustrating losses, a retention risk that outcome balance alone won't surface.

**4. Rated games are more predictable and slightly longer.**
The higher-rated player wins 83.8% of rated vs 73.5% of unrated games in the widest gap bucket, and rated games average 62.0 turns vs 54.3 (medians 57 vs 50). *So what:* unrated play is a lower-commitment, noisier environment; matchmaking-quality monitoring should weight rated games more heavily.

**5. Book depth barely relates to game length, but variation win rates show large — as-yet unvalidated — spreads.**
`opening_ply` correlates with `turns` at only 0.056, yet deeper-book games end more in resignation (51.6% → 62.0%) and less in mate (34.6% → 24.9%). White win rates range from 80.0% (Russian Game: Damiano Variation, 40 games) to 30.0% (Sicilian Defense: Modern Variations Main Line, 30 games). *So what:* the variation spread is the natural basis for a "which lines work against this opening" feature, but small samples make these candidates for validation.

---

## Methodology

1. **Data prep** — load `chess.csv` (20,058 rows, 16 columns, no nulls); convert timestamps to datetimes; derive `rating_diff` and `rating_diff_abs`; split `opening_name` into base `opening` and named `variation` on the first colon.
2. **Core EDA** — distribution of rating gaps; quintile bins via `pd.qcut`; favorite-win-rate per bucket with per-bucket game counts and share of all games; headline win-rate-vs-gap chart.
3. **Supporting analysis** — victory_status cross-tab by gap bucket; `opening_ply` vs `turns` correlation and decisiveness crosstab; opening/variation white-win-rate table filtered to ≥30 games; rated vs unrated comparison for win-rate-by-gap, game length, and how games end.
4. **Report** — a clean narrative notebook (`...Analysis Report.ipynb`) with question-driven markdown, identical numbers to the working notebook.

Note: rated and unrated games are deliberately **kept together** — the rated/unrated comparison is itself a supporting angle rather than a filter.

---

## Limitations

Four caveats. First, `created_at` and `last_move_at` are identical for ~43% of rows even though many of those games run dozens of turns, pointing to a low-precision timestamp collection artifact; the analysis never treats timestamp deltas as a measure of game duration. Second, `higher_rated_won` counts a draw as "the favorite did not win" — a deliberate simplification that keeps interpretation clean but slightly understates how often the favorite avoids losing. Third, the opening/variation table is filtered to ≥30 games, and many entries sit right at that threshold; only rows with 100+ games should be treated as reliable. Finally, there is no session, churn, or retention data, so claims about player experience are inferred from proxies (game length, resignation behavior, outcome predictability) rather than directly measured.

---

## Structure

```
data/                     chess.csv (20,058 games)
notebooks/
  Lichess Matchmaking Fairness Analysis.ipynb      working notebook (full exploration)
  Lichess Matchmaking Fairness Analysis Report.ipynb  clean narrative report
```

## Quick start

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt
jupyter notebook notebooks/
```
