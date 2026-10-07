# A/B Testing Statistical Analysis

A complete A/B test workflow for a website change: check the experiment was set up fairly, test the result several ways, work out what the test could and could not detect, look at segments without fooling yourself, see why peeking at results early is dangerous, and finish with a clear ship / do not ship decision.

Built in Google Colab. No API key needed.

> **The data is made up.** 20,000 users (10,000 per group) simulated for an old checkout page (A) against a new one (B), with a lift planted mostly on mobile. This is a method demo, not a real experiment. The notebook also accepts your own `ab_test.csv` with columns `group, converted, revenue, device, country`.

## What it does

1. **Sample ratio check:** is the split really 50/50? If not, the results cannot be trusted.
2. **Conversion test:** two-proportion z-test, chi-square test and a 95% confidence interval for the difference.
3. **Effect size and power:** Cohen's h, the smallest lift this test could reliably detect, and the users needed per group for different lifts.
4. **Revenue per user:** Welch t-test, Mann-Whitney test and a bootstrap confidence interval, because revenue is full of zeros and has a long tail.
5. **Segments with multiple-testing corrections:** Bonferroni and Benjamini-Hochberg, plus an **interaction test** that asks whether the effect really differs by device.
6. **The peeking problem:** 2,000 simulated A/A tests (no real difference) checked once, and checked 10 times.
7. **Bayesian view:** probability B beats A, expected loss and a credible interval.
8. **Decision rule** that combines everything and prints a recommendation.

## Results from the run

| Measure | Result |
|---|---|
| Conversion A / B | 9.98% / 11.24% |
| Lift | +1.26 percentage points (+12.6% relative) |
| z-test and chi-square | p = 0.0038 (they match, as they should) |
| 95% CI for the difference | 0.41 to 2.11 pp |
| Smallest lift detectable with 10,000 users per group | about 1.22 pp |
| Users per group needed to detect 0.5 / 1 / 2 / 3 pp | 57,656 / 14,720 / 3,829 / 1,766 |
| Revenue per user A / B | 8.02 / 9.27 (Welch p = 0.004, bootstrap CI 0.37 to 2.06) |
| Average order value (buyers only) A / B | 80.38 / 82.47 |
| Probability B is better (Bayesian) | 99.83% |

**Segments:** only **mobile** shows a lift that survives multiple-testing correction (+2.11 pp, adjusted p = 0.0015). Desktop shows -0.52 pp, which is not significant (p = 0.50), so there is no evidence of a gain there. The interaction test (p = 0.0295) says the effect really does differ by device, so one overall average hides the story.

**Peeking:** with a single final look, the false-positive rate was **4.8%** (expected 5%). Checking the results 10 times and stopping at the first "significant" one produced a false winner **19.9%** of the time, even though A and B were identical.

**Recommendation printed by the notebook:** ship variant B on mobile first; desktop showed no gain, so watch or hold it.

## What the evaluation showed

- **A headline average can hide a split result.** The overall lift is significant, but it comes from mobile.
- **A result is only as good as the test's power.** With 10,000 users per group this test could reliably detect lifts of about 1.2 pp or more. Detecting half a point would need almost 58,000 users per group.
- **Peeking inflates false winners about fourfold.** Decide the sample size first and look once, or use a proper sequential method.
- **The notebook does not compute "power of the observed effect".** That number only repeats the p-value in another form.

## Limitations

- **The data is simulated**, so the sample ratio check is trivially clean (exactly 10,000 per group).
- Conversion was chosen as the primary metric. Revenue and segments are secondary checks and were not used to pick the winner, and no correction was applied across the two metrics.
- The segment analysis uses only device and country. Time effects, novelty effects and user-level repeats are not modelled.
- The peeking simulation uses 200 users per look and a 10% base rate. Different settings change the exact numbers but not the lesson.

## Tech stack

pandas, NumPy, SciPy, statsmodels, matplotlib, seaborn.

## How to run

1. Open the notebook in Google Colab.
2. Run the cells from top to bottom. No API key is needed.
3. To use your own experiment, upload `ab_test.csv` with the columns `group` (A or B), `converted` (0 or 1), `revenue`, `device` and `country`.

## Files the notebook creates

- `abtest_data.csv`: the experiment data
- `abtest_segments.csv`: segment results with corrected p-values

---

Built by **Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
