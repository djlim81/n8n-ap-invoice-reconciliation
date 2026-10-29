# Workflow architecture and control design

## Input contract

The form accepts a **single XLSX** with required column labels `InvoiceID`, `Vendor`, `InvoiceAmount`, and `POAmount`. The workflow expects values to be extracted as individual JSON items by the n8n Extract From File node.

The `Validate Excel Template` Code node recognizes certain header spelling variations after removing punctuation/case differences (`PO_Amount` -> `POAmount`) and intentionally throws an error for those. It does not fully inspect the original worksheet header row and does not detect every missing or arbitrarily renamed column. Invalid row fields are primarily handled by the next validator.

## Node layout

- `On form submission` -> `Extract Invoice Records` -> `Validate Excel Template` -> `Assign Source Record IDs` -> `Validate Imported Invoice Data`.
- From validation, `Batch Intake Control` computes classification counts. `Check Imported Data Quality` splits valid and invalid records.
- Valid records: `Compare Invoice and PO Amounts` -> `Calculate Invoice Exceptions` / `Mark Amounts Matched` -> `Merge Reconciliation Results` -> `Reconciliation Summary` and `Prepare Financial Reconciliation Report` -> XLSX.
- Invalid records: `Flag Invalid Imported Invoice` -> `Prepare Data Quality Exception Report` -> XLSX.
- Both report-preparation branches feed `Merge Audit Records` -> `Downstream Integrity Check`.

## Source record key

`SourceRecordKey = SourceBatchID + ':' + SourceRowNumber` where batch ID is the n8n execution ID, and row number is the 1-based extracted-item index plus one to account for the header. This is an **execution-scoped position identifier**, not a globally stable invoice ID and not duplicate-upload prevention. Skipped or filtered blank worksheet rows could make the displayed row number differ from the original Excel row location.

## Data quality validation

Checks: missing invoice ID; missing vendor; non-numeric or non-finite `InvoiceAmount` / `POAmount`; repeated `(Vendor, InvoiceID)` pairs ignoring case/outer whitespace. Blank vendors are invalid; text placeholders such as `(empty)` are currently treated as nonblank. Business rules such as vendor existence, PO authorization, invoice date, taxes, currencies, and payment approval are out of scope.

## Financial results

Valid records are compared on `InvoiceAmount !== POAmount` (exact numeric comparison). The signed `Difference = InvoiceAmount - POAmount`; absolute difference totals sum the magnitudes of the per-record differences. Mismatch count and rate are calculated among **valid records only**. No currency conversion or tolerance band is modeled.

## Audit

`Downstream Integrity Check` compares records prepared for the two reports with the validated records: it verifies totals, classification counts, unexpected statuses, key uniqueness, and missing/duplicated/unexpected `SourceRecordKey` values. A deliberately modified duplicate key yielded `AuditStatus = FAIL` while the row count was unchanged; that mutation was reverted in the exported version.

**Trust boundary:** reports are converted in parallel before audit results can gate release. A `PASS` in the audit is evidence of the tested comparisons, not proof that vendor/PO master data or invoice values are independently correct. No automatic response to audit `FAIL` is configured.
