# Arla × OSCA Forecasting Challenge: a suggested solution

A step-by-step Jupyter notebook that forecasts weekly orders (`Orders Weekly`) for one series,
**Discount 1 – Product 3** (Skyr Flavoured). It uses only the methods from Lectures 10-12 and starts
simple, adding one idea at a time. Every model is scored on the same 13 holdout weeks.

This is a proposal for the study group to discuss and build on, not a finished answer.

## Results (13-week holdout, W22-W34 2026)

| Step | Model | MASE |
|---|---|---|
| 1 | Best benchmark: seasonal naive | 0.513 |
| 2 | ARMA(3, 0) | 0.594 |
| 3 | SARIMA(3,0,0)(0,1,1)52 | 0.418 |
| 4 | **SARIMAX + promotion indicator (final model)** | **0.373** |
| 5 | SARIMAX + promotion + holidays (dropped) | 0.380 |

Main insight: knowing the promotion plan is the most valuable information. The largest miss is the
last week (W34 2026), which looks incomplete in almost all series.

## How to run it

1. Clone the repository.
2. **Put the data file `Forecasting_challenge_data.xlsx` from the course in the same folder as the
   notebook.** The data is not in the repository (see below).
3. Install the packages (Python 3.11 or newer):

   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

4. Open `forecasting_step_by_step.ipynb` and run the cells from top to bottom. In PyCharm: choose
   the `.venv` interpreter as the kernel and use Shift+Enter or "Run All". From a terminal:
   `jupyter notebook forecasting_step_by_step.ipynb`. The whole notebook takes about 1-2 minutes.

The notebook is saved with its output, so you can also just read it on GitHub. When it runs, it
writes a few figures and a table to `results/` (not tracked by git).

## What the notebook covers

| Part | Content |
|---|---|
| 1-2 | Loading and checking the data, reshaping it to one row per week, the special rows (calendars, exception weeks, promotions) |
| 3 | Choosing the series, using the training weeks only |
| 4 | Cleaning decisions and the choice of variables |
| 5 | Looking at the series: time plot, seasonal plot, STL |
| Models 1-5 | Benchmarks → ARMA → SARIMA → SARIMAX (promotions) → SARIMAX (+ holidays) |
| 6-7 | Benchmark table and a rolling-origin check inside the training weeks |
| 8 | Where the forecast goes wrong, and the incomplete last week |
| 9 | Sensitivity check: Manual Cleaned History |
| 10 | What if we forecast half a year (26 weeks) ahead? |
| Summary | Five points for the five slides |

## Choices worth discussing in the group

- **Holdout of 13 weeks** (one quarter), fixed before any model was fitted.
- **Every choice is made on the training weeks** (ACF/PACF, AIC, p-values, residual checks, rolling origin).
  The holdout is only used for the final score, as the brief asks.
- **Promotions** are used only as a yes/no indicator, never the size of the uplift (as the brief asks).
- **Exception weeks** are kept, not deleted, and not used as input (they are not known in advance).
- **Manual Cleaned History** is not used: it mostly replaces the promotion peaks with a baseline.
- **Seasonality Calendar 1-8** are left out, because the brief does not say what they mean.

Ideas to try next: a constant in the SARIMAX (`trend="c"`) to capture growth from one year to the next,
a promotion indicator for the weeks before and after a promotion, and a comparison with a machine
learning model. Choose any change with the rolling-origin check (Part 7), not with the holdout.

## Data

The data is real, anonymised order data that Arla shared for the course, and the assignment text
belongs to the lecturers. Neither is in this repository, and `.gitignore` keeps `*.xlsx`, `*.pdf` and
`results/` out of git. Please keep it that way, and keep the repository private.
