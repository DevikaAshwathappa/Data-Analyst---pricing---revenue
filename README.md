Singapore USEP — Merit Order Analysis
Take-home assessment for the Data Analyst — Pricing & Revenue Assurance role.
Rebuilds the Uniform Singapore Energy Price (USEP) from the published merit order (offer stacks) and system demand for January 2023, and checks the estimate against the actual published price — in both pandas and SQLite, with a check that the two agree.

What's in this project

pricing and revenue assurence.ipynb -	Main notebook — all tasks, bonus SQL, findings
DelayedOfferStacks_Energy_01-Jan-2023 to 31-Jan-2023.csv	- Input: merit order / offer stacks
USEP_Jan-2023.csv	- Input: demand and published USEP 
usep.db -	Output: SQLite database built by the bonus SQL section

How to run it
Put the notebook and the two input CSVs in the same folder.
Open pricing and revenue assurence.ipynb in Jupyter (classic Notebook, JupyterLab, or VS Code with the Jupyter extension).
Kernel → Restart & Run All.

No path editing is required — the notebook finds the CSVs automatically from whatever folder it's opened in (Path.cwd(), with a fallback search of Desktop/Downloads if they're not alongside the notebook).

Requirements
pandas
numpy
matplotlib

sqlite3 and unittest are part of the Python standard library — nothing extra to install for the bonus SQL section or the unit tests.

Notebook structure

A clickable table of contents sits near the top. Sections:

Load and clean the data (Task 1) — inspects both raw files first, then cleans them: skips 2 junk header lines in the merit order file, standardizes USEP's column names, and repairs two corrupted rows (see Findings below). Builds a cum_volume column (the merit order curve itself) and a proper datetime index from date + period (period 1 = 00:00).
Merit-order plots (Task 2) — all 48 half-hour periods of one day plotted together, plus individual periods styled like the brief's example, with the demand line and published price marked.
Clearing-price function + unit tests (Task 3) — clearing_price(demand, bid_price, cum_volume) returns the price of the cheapest offer whose cumulative volume covers demand, via binary search. 6 unit tests cover the normal case, a boundary case, unmet demand, missing demand, and invalid input.
Bonus: SQLite — loads both raw CSVs into SQLite as untyped text tables, repeats the same cleaning logic in SQL (including both row repairs), then runs example analysis queries: daily summary, price bands, periods above average, price spikes, and the clearing price itself.

Key findings
The merit order file is clean once its 2 junk header lines are skipped.
The USEP file has two corrupted rows, both repaired from surrounding row order:
a placeholder row (period 99, USEP 99,999) that belongs at 7 Jan, period 27 — the original reading is unrecoverable, so it's left as a missing value
a row dated 40 Jan 2023 that belongs at 21 Jan, period 26
Three periods on 9 Jan (09:00, 09:30, 20:00) have a published USEP higher than the single most expensive offer in that period's stack — these prices can't be reproduced from this file at all, and would need checking with the data owner.
Python and SQL independently compute the same clearing price for every period in the dataset (confirmed by the parity check).
