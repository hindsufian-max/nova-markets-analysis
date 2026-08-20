# Nova Markets Approval-Rate Investigation

## What's going on

Nova Markets is right that its visible approval rate declined after June 1. Using final SALE attempts that reached a payment provider, and converting timestamps to Nova's UTC+8 timezone, the approval rate fell from **80.1% in May (4,996 of 6,240 attempts)** to **72.4% during June 1–28 (5,904 of 8,156 attempts)**, a decline of **7.7 percentage points**. I excluded June 29–30 because the operations log states that the latest two extract days are incomplete.

The decline is mainly caused by a change in traffic mix. The new NBLX-07 route launched on June 2 for previously unavailable IDR and VND bank-transfer traffic. It had a **53.7% approval rate**, while Nova's established routes remained at **79.2%**.

However, there is also a genuine performance issue. Approval for Japanese cards processed through SORVA-14 fell from **74.7% in May to 56.1% after June 11**, matching the date of the Japanese BIN reallocation reported in the operations log. I estimate approximately **64 fewer approved attempts** during June 11–28 compared with the May approval rate.

NBLX-07 also had **655 risk-filtered attempts** that were not visible in the merchant dashboard. Including these attempts reduces its end-to-end success rate to **41.3%**.

## What we should do

We should tell Nova that its dashboard correctly shows a decline, but explain that most of the headline movement comes from adding lower-performing traffic from new IDR and VND markets rather than a general collapse across existing routes.

We should investigate the affected Japanese BIN ranges with SORVA and temporarily reroute eligible Japanese-card traffic if possible. We should also review the tightened NBLX risk rules and determine whether the prevented chargeback risk justifies the lower customer conversion.

Internally, approval monitoring should be segmented by route, currency, and payment method. The merchant dashboard should also distinguish provider approval rate from end-to-end conversion, which includes attempts stopped by internal risk screening.

## Assumptions and open questions

- Approval rate is defined as `APPROVED / (APPROVED + DECLINED)` for final SALE attempts that reached a provider.
- FILTERED attempts are excluded from provider approval because they never reached a processor. PENDING attempts are excluded because they have no final outcome.
- Nova's timestamps were converted from UTC to its configured UTC+8 timezone.
- June 29–30 were excluded because the operations log identifies them as incomplete.
- The supplied data cannot establish Nova's exact financial loss. Approximately 64 approvals may have been lost on affected Japanese traffic, but calculating actual monetary loss requires merchant margin, processing fees, successful later retries, and chargeback costs.
- I would also ask whether cascaded retries share an original customer-intent identifier, because per-attempt approval may differ from customer-level conversion.

## How to reproduce

Run the cells in `analysis.ipynb` from top to bottom using Python 3 with pandas and NumPy installed:

```bash
pip install pandas numpy

Then open the notebook and run all cells from top to bottom.

The notebook:

Loads the supplied orders and dashboard data.
Checks the dataset size, missing values, and duplicate records.
Normalizes inconsistent casing and whitespace in categorical columns.
Filters the data to Nova Markets' SALE attempts.
Converts UTC timestamps to the merchant's UTC+8 timezone.
Excludes incomplete dates and non-final outcomes.
Calculates approval rates for May and June 1–28.
Breaks June performance down by route.
Compares the new NBLX-07 route with established routes.
Investigates SORVA-14 Japanese-card performance after June 11.
Measures the effect of internally filtered NBLX attempts.

All source files should remain in the same directory as analysis.ipynb.

## Assumptions and open questions

### Assumptions

- I defined provider approval rate as `APPROVED / (APPROVED + DECLINED)` for final SALE attempts that reached a payment provider.
- I excluded `FILTERED` attempts because they were stopped internally before reaching a provider, and excluded `PENDING` attempts because they did not yet have a final outcome.
- I analysed SALE transactions only, matching the merchant dashboard's focus on deposits.
- I converted timestamps from UTC to the merchant's configured UTC+8 timezone.
- I excluded June 29–30 because the operations log states that the final two days of the extract are incomplete.
- I treated the May SORVA-14 Japanese-card approval rate as the benchmark for estimating lost approvals after June 11.
- The timing of the SORVA and NBLX performance changes is consistent with the events in the operations log, but the available data does not prove causation by itself.

### Open questions

Before taking action, I would want to confirm:

- Can SORVA provide the exact Japanese BIN ranges affected by the June 11 change?
- Can affected Japanese-card traffic be temporarily redirected to another route?
- How much fraud and chargeback loss did the tightened NBLX risk rules prevent?
- Do cascaded attempts have a shared customer-intent or original-order identifier? Without this, per-attempt approval may not represent the true customer-level conversion rate.
- Did the customer or transaction-value mix change within the affected routes?
- How many declined customers later completed a successful payment through another attempt or route?
- What are the merchant's margin, processing fees, and chargeback costs? These are required to translate lost approved volume into actual financial loss.

The supplied data supports an estimate of approximately 64 potentially lost approvals on the affected Japanese traffic, but it does not support a reliable estimate of the merchant's final profit loss.

### Further work

Due to the one-hour limit, I prioritised data validation, dashboard reconciliation, identification of the main drivers, and a reproducible core analysis. With additional time, I would:

- Build a daily approval-rate chart with annotations for the June 2, June 8, and June 11 operational changes.
- Perform a formal traffic-mix decomposition to quantify how much of the total decline came from NBLX-07 versus deterioration in existing routes.
- Analyse decline reasons before and after each operational change.
- Reconstruct USD values using the FX table, including inverse JPY rates and missing weekend rates.
- Estimate lost approved payment volume by route and currency, with sensitivity ranges rather than a single estimate.
- Measure customer-level conversion by linking initial attempts with cascaded retries.
- Test whether the observed changes are statistically significant.
- Build automated monitoring by route, currency, payment method, and risk-filter rate.


```markdown
## How I used AI

I used ChatGPT to help structure the investigation, challenge my definition of approval rate, accelerate Python code development, and review whether the conclusions followed from the available evidence.

One initial AI-assisted calculation checked only the uppercase value `APPROVED`. The raw data also contains lowercase status values such as `approved`, so this understated the number of successful attempts. I caught the problem when my calculated results did not reconcile with the merchant dashboard. I then inspected the unique categorical values, normalized casing and whitespace, and reran the analysis.

AI also initially encouraged treating the full decline as performance deterioration. Breaking the results down by route showed that this interpretation was incomplete: most of the headline decline came from the addition of lower-performing NBLX-07 traffic, while a separate deterioration existed in SORVA-14 Japanese-card traffic.

I reviewed the calculations and used the operational log to validate the timing rather than accepting the AI-generated explanation without checking it.

I also spent approximately 30 minutes setting up a public GitHub account/repository and resolving repository and notebook-environment issues. Given the remaining time, I prioritised a clear, reproducible core analysis and documented the additional work I would complete next rather than presenting unfinished analysis as confirmed.