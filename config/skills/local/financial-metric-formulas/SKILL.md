---
name: financial-metric-formulas
description: Computing derived financial metrics from statement line items — quick ratio, current ratio, working capital, EBITDA and EBITDA margin, gross/operating/net margin, CAGR, free cash flow, capex intensity, inventory turnover, days payable and days sales outstanding, interest coverage, effective tax rate, return on assets and equity. For questions asking whether a company's liquidity, margins, efficiency or growth are healthy, improving or declining.
metadata:
  activation: embedding
---

# Derived financial metrics

Take every input from the statement that reports it, and check the column year before
using a figure — statements print the current and prior period side by side.

## Formulas

| Metric | Formula | Inputs from |
|---|---|---|
| Current ratio | current assets / current liabilities | balance sheet |
| Quick ratio | (cash and equivalents + short-term investments + receivables) / current liabilities | balance sheet |
| Working capital | current assets − current liabilities | balance sheet |
| Gross margin | gross profit / revenue | income statement |
| Operating margin | operating income / revenue | income statement |
| Net margin | net income / revenue | income statement |
| EBITDA (unadjusted) | operating income + depreciation and amortisation | income statement + cash flow statement |
| EBITDA margin | EBITDA / revenue | as above |
| Free cash flow | cash from operations − capital expenditures | cash flow statement |
| Capex intensity | capital expenditures / revenue | cash flow + income statement |
| Inventory turnover | cost of goods sold / average inventory | income statement + balance sheet |
| Days sales outstanding | (receivables / revenue) × 365 | balance sheet + income statement |
| Days payable outstanding | (payables / cost of goods sold) × 365 | balance sheet + income statement |
| Interest coverage | operating income / interest expense | income statement |
| Effective tax rate | income tax expense / pre-tax income | income statement |
| Return on assets | net income / total assets | income statement + balance sheet |
| Return on equity | net income / shareholders' equity | income statement + balance sheet |
| CAGR | (ending / beginning) ^ (1 / years) − 1 | any two dated figures |

## Conventions that change the answer

- **Quick ratio** excludes inventory and prepaid expenses. Where a strict form is wanted,
  use cash + short-term investments + receivables rather than (current assets − inventory);
  the two differ whenever prepaid expenses are material. State which form you used.
- **EBITDA** here is unadjusted: operating income plus D&A, with no add-backs for
  restructuring, impairment or stock compensation. "Adjusted EBITDA" is whatever the
  company defines it to be — if the document defines it, use the document's definition and
  say so.
- **Depreciation and amortisation** comes from the cash flow statement, not the income
  statement, where it is usually embedded in cost lines.
- **Averages**: turnover and return ratios are conventionally computed on average balances
  ((opening + closing) / 2). With only one balance sheet date available, use the closing
  balance and say that is what you did.
- A ratio a company defines for itself in the filing beats the textbook definition. Quote
  the company's definition when it exists.

## Answering

Quote each input figure with its line-item name, its statement, and its period. Compute
with the script tool rather than mentally, and give the formula alongside the result so
the arithmetic is checkable. Where a benchmark is expected ("is this healthy?"), state the
value first, then the judgement, and name the comparison you are making.
