# Test plan and evidence

All invoice names and amounts are synthetic. Results below reflect the observed n8n executions in the training workflow; numbers are not production performance measures.

| ID | Test | Input fixture / method | Observed outcome |
| --- | --- | --- | --- |
| T01 | Mixed batch | `01_mixed_batch.xlsx` | 6 received; 5 valid, 1 held; 3 amount mismatches, 2 matched; audit PASS; two binary XLSX outputs generated. |
| T02 | All valid | `02_all_valid.xlsx` | 5 valid, 0 held; audit PASS; data-quality export branch skipped. |
| T03 | All invalid | `03_all_invalid.xlsx` | 0 valid, 3 held; audit PASS; financial branch skipped; data-quality XLSX opened and verified in Excel. |
| T04 | Missing invoice simulation | Temporarily use `.slice(1)` inside audit | Source 6, actual 5; audit FAIL. Code restored; PASS. |
| T05 | Same-count duplicate simulation | Temporarily copy one `SourceRecordKey` onto another record in audit | Source/actual counts both 6; missing and duplicate keys identified; audit FAIL. Code restored; PASS. |
| T06 | Incorrect header | `04_bad_header.xlsx` with `PO_Amount` | `Validate Excel Template` intentionally fails with a descriptive error. |
| T07 | Regression after header gate | Re-run `01_mixed_batch.xlsx` | 5 valid, 1 held; audit PASS and record identity PASS; both XLSX conversions executed. |

## Expected financial totals (T01)

- Valid invoice totals: `1800 + 1500 + 800 + 2250 + 1200 = 7550`.
- Valid PO totals: `1500 + 1500 + 950 + 2250 + 1000 = 7200`.
- Signed differences: `+300, 0, -150, 0, +200`; net `+350`.
- Absolute differences: `300 + 0 + 150 + 0 + 200 = 650`.
- Mismatch rate = `3 / 5 = 60%` (valid invoices only).

## Practical manual acceptance criteria

1. XLSX extraction preserves the invoice ID, vendor, amounts and row cardinality.
2. Invalid/missing fields are classified as invalid; valid numeric rows continue to financial comparison.
3. Financial report includes matching records and mismatches with signed differences.
4. Data-quality report contains held records and issue reasons; empty branches do not create fictitious records.
5. `ActualProcessedRows` equals `ReceivedRows` for the valid test, and both `RecordIdentityCheck` and `AuditStatus` are `PASS`.
6. Incorrect header test fails before normal validation.

## Evidence screenshots

- [`screenshots/workflow-overview.png`](screenshots/workflow-overview.png) — successful end-to-end execution.
- [`screenshots/audit-pass.png`](screenshots/audit-pass.png) — expected PASS classification and keys.
- [`screenshots/duplicate-detected.png`](screenshots/duplicate-detected.png) — deliberately induced missing/duplicate-key detection.

**Not automated:** The tests were performed manually in n8n and reviewed through screenshots/Excel downloads. No independent test runner, scalability benchmarks, or third-party data verification are claimed.
