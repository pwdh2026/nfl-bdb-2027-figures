# Force per Pound: A Physics-Grounded Translation From Combine Testing to NFL Rush

*What the Combine forgot to divide by.*

## The question

Every spring the NFL measures several hundred prospects and then argues about the numbers for three months. The 40-yard dash dominates those arguments. But a 40-yard dash is forty yards long, while a pass rush is decided in the first two steps. Rather than ask whether "the Combine works," we asked a narrower and more useful question: **which tested movement actually transfers to regular-season production, for which players, and can the tracking data tell us why?**

## Data and measurement

The competition data cover 510 prospects drafted or signed from 2023 to 2025, with 6,310 Combine drill attempts sampled at 10 Hz, 22.6 million in-game tracking frames and 314,197 player-play records across 1,001 games.

Two measurement checks come first, because every later conclusion depends on them.

**The tracking clock is real.** We reconstructed each 40-yard dash from the 10 Hz Combine tracking — start detection, projection onto the sprint axis, and splits at 5/10/20/30/40 yards. Tracking-derived times correlate with official electronic times at r = 0.93 (n = 368) with a stable +0.27 s offset from our conservative start trigger. The tracking therefore measures the same physical event, but at 0.1 s resolution, which lets us describe the shape of acceleration rather than just its endpoints.

![Figure 1](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig1_tracking_validation.png)

*Figure 1. Tracking-derived 40-yard times versus official electronic times (r = 0.93, n = 368).*

**The NFL outcome is real.** We rebuilt a first-step metric directly from raw game tracking: displacement and speed in the 0.5 s after `ball_snap`. Across all three seasons it correlates with Next Gen Stats' get-off at r = -0.86 (n = 64), confirming that get-off is a genuine movement measurement and not a vendor artifact.

We define **pressure rate** as the share of pass-rush snaps on which a pressure is recorded. It correlates with sack rate at r = 0.69, and unlike sacks it is frequent enough to study (league mean ≈ 8.5%).

## Result 1: the first step is where pass rush is won

Among defensive linemen with at least 100 pass-rush snaps (n = 64), mean get-off predicts pressure rate at **r = -0.69 [95% CI -0.81, -0.53]**. The relationship is monotone across quartiles: 11.1%, 8.0%, 6.2% and 5.2% pressure from fastest to slowest. In football units, **0.1 seconds of get-off is worth about 2.4 percentage points of pressure — roughly 10 pressures and 1.4 sacks per 400 rushes.** Slow first steps are not a stylistic preference; they are a production tax.

![Figure 2](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig2_chain.png)

*Figure 2. NFL get-off time versus pressure rate for defensive linemen (r = -0.69, n = 64).*

## Result 2: first step is force divided by mass

If the first step is what matters, what at the Combine actually measures it? The relevant physics is Newton's second law: acceleration is force over mass. The broad jump is the purest lower-body force test in the battery; body weight is the inertia that force must move. Their ratio is a direct estimate of explosive acceleration:

**EMR = broad jump (inches) ÷ body weight (lb)**

This is not a fitted construct. We evaluated it with leave-one-draft-class-out cross-validation, training on two classes and predicting the third, so every reported number is out-of-sample.

![Figure 3](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig3_models.png)

*Figure 3. Leave-one-draft-class-out R-squared for every candidate predictor of NFL get-off and pressure rate.*

| Predictor of NFL get-off | Out-of-sample R² |
|---|---|
| **EMR (broad jump / weight)** | **0.62** |
| Mass + broad jump | 0.58 |
| Broad jump | 0.51 |
| 40-yard dash | 0.49 |
| 10-yard split | 0.39 |
| Vertical jump | 0.29 |
| NGS athleticism score | -0.08 |

EMR outperforms every raw test, including the 40 (r = -0.80 versus +0.71). In a joint model containing both, EMR is significant (p = 0.001) and the 40 is not (p = 0.55): once force per pound is known, the stopwatch adds nothing. Moving a lineman from the 10th to the 90th percentile of EMR spans about **0.19 seconds of NFL get-off** — twice the full between-player standard deviation.

The mechanism is visible in the raw tracking, not only in vendor metrics. Across **34,416 real pass-rush snaps** in these three seasons, forward displacement in the first 0.5 seconds rises monotonically with EMR: 0.51, 0.54, 0.59 and 0.61 yards from the lowest to the highest quartile (r = +0.71 across 71 rushers). Because this quantity needs only 10 Hz positions and the `ball_snap` tag, a club can compute it after every game instead of waiting for a season-end feed.

![Figure 4](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig4_tracking_burst.png)

*Figure 4. Forward displacement in the first 0.5 s after the snap, measured from 34,416 raw pass-rush tracking snaps, by EMR quartile.*

The ratio also survives the obvious objection that it is a proxy for position. Within edge rushers, r = -0.53 [-0.78, -0.11]; within interior linemen, r = -0.60 [-0.75, -0.40]. And it carries through to production: EMR predicts pressure rate with out-of-sample R² = 0.38 (r = 0.62), rising from 5.2% to 10.8% pressure between the bottom and top EMR quartiles.

![Figure 5](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig5_emr_pressure.png)

*Figure 5. Explosion-to-Mass Ratio versus pressure rate (r = 0.62, n = 57).*

The practical consequence appears in individual players. The top decile of EMR produced 10.8% pressure against 4.8% for the bottom decile, yet its median draft slot was 144. Isaiah Land (undrafted, 2023) posted the highest EMR in the cohort and a 14.4% pressure rate; Cedric Johnson (pick 214, 2024) led the sample at 16.2%; Javon Solomon (pick 168, 2024) sat in the 96th percentile at 13.7% — all edge rushers at 246 lb or less, the profile the 40 rewards least and force per pound rewards most.

## Result 3: what the draft is actually paying for

The draft is an excellent predictor of opportunity and a weak one of efficiency. Draft slot correlates with log career snaps at **r = -0.59** (n = 383) but with pressure rate at only **r = -0.21** (n = 54, CI crossing zero). Once a lineman is on the field, where he was drafted explains little of how often he wins.

![Figure 6](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig6_market.png)

*Figure 6. What draft slot is actually pricing: opportunity versus per-snap efficiency.*

With roughly 50 observations we cannot resolve the market's exact weighting of individual tests: raw correlations with draft slot are +0.19 (p = 0.18) for the 40 and -0.11 (p = 0.43) for EMR. We therefore claim something weaker and still actionable: **the trait that best predicts the first step is free to compute, and draft position is not a substitute for it.**

## Result 4: where translation stops — receivers

The same framework applied to receivers returns a clean null. Separation at the pass-forward point is a real, stable player trait (split-half reliability 0.76; mean 3.02 yd, SD 0.37 across 93 receivers). But no Combine test moves it: 40-yard r = -0.19 [-0.36, +0.01], broad jump r = +0.16 [-0.05, +0.35], NGS composite r = -0.04 [-0.22, +0.13]. The 3-cone shows a larger point estimate (r = -0.39 [-0.63, -0.13]) on only 27 receivers, which we do not treat as established.

![Figure 7](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig7_wr_forest.png)

*Figure 7. Correlation of each Combine test with NFL separation for receivers, with 95% bootstrap intervals.*

The asymmetry is mechanical. Get-off is a solo movement: mass accelerated from a stance. Separation is dyadic: it depends on the defender, the coverage, the route and the quarterback's timing. No solo drill reproduces a contest. **The Combine translates where the game asks the player to repeat the measured movement, and stops where the game asks for a negotiation.**

That null is a deadline, not a dead end. Stability arrives on very different schedules: a rusher's get-off reaches 0.86 reliability after about **40 rushes**, while a receiver's separation needs about **150 routes** to reach 0.85. A club can grade either trait inside one season, which is precisely why the Combine's blindness to separation is survivable — and why paying for it there is not.

## Result 5: the composite score hides the signal

The NGS athleticism score correlates with the 40 at -0.71 within defensive backs, -0.80 within offensive linemen and -0.71 within receivers — but only **-0.18 when all positions are pooled**. The score is a within-position percentile, so cross-position comparison destroys the information it was built to carry. Its correlation with NFL pressure rate is ≈ 0 (-0.21, out-of-sample R² negative). This is not a criticism of the score's construction, only a warning about its use.

![Figure 8](https://raw.githubusercontent.com/pwdh2026/nfl-bdb-2027-figures/main/fig8_composite.png)

*Figure 8. The NGS athleticism score correlates strongly with the 40 within position but not across positions.*

## Recommendations

1. **For defensive line evaluation, compute EMR.** It uses two numbers already on every scouting report. A prospect near 0.30 is a materially different first-step projection than one near 0.47.
2. **Do not use the 40 alone as a first-step proxy.** It works, but EMR subsumes it and explains why.
3. **Do not draft receivers on Combine athleticism.** Grade separation in-season: 150 routes is enough.
4. **Use position-specific models, not composites,** when comparing players across positions.

**Monday morning, in practice.** Pull the 10 Hz file for last Sunday's game, tag `ball_snap`, and compute every rusher's forward displacement at 0.5 s. After roughly forty rushes that number is stable at 0.86 reliability, so a coordinator can compare a rookie's first step against his draft-day projection by mid-season. The gap is the actionable part: a player beating his EMR projection has earned more pass-rush snaps, and a player missing it needs the movement coached before the role grows.

## Limitations

Samples are small (64–93 players per analysis) and span three draft classes; intervals are reported throughout and several subgroup estimates include zero. Outcomes exist only for players who earned snaps, so estimates describe per-snap efficiency conditional on playing. Pressure is a vendor-defined event. We use no college, scheme or coaching data, and null results are powered to detect moderate effects rather than to prove absence.

## What the tracking data added

The 10 Hz Combine tracking did not create a better predictor: EMR, built from two official measurements, beat every tracking-derived burst feature we tested. Its value was **measurement integrity** — independent reconstructions of the stopwatch (r = 0.93) and of the NFL first step (r = -0.86).

## Appendix

Designed, executed and written by an undergraduate student using only the official competition data and open-source Python. Code and full pipeline: public Kaggle Notebook - https://www.kaggle.com/code/pwdh2026/nfl-bdb-2027-force-per-pound . All statistics reproducible from `combine_results.csv`, `combine_tracking.csv`, `player_play.csv` and `game_tracking_*.csv`; bootstrap CIs use 3,000 resamples; cross-validation is leave-one-draft-class-out.
