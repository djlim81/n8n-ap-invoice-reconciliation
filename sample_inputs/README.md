# Synthetic sample input workbooks

Each workbook has worksheet `Invoices`, headers on row 1, and numeric amount cells. Vendor cells intentionally left missing are **truly blank** (not strings like `(empty)` or `N/A`).

| File | Meaning | Expected outcome |
| --- | --- | --- |
| `01_mixed_batch.xlsx` | Five valid invoices, one missing vendor | Valid 5, invalid 1; two XLSX outputs; audit PASS |
| `02_all_valid.xlsx` | Five valid invoices | Valid 5, invalid 0; audit PASS |
| `03_all_invalid.xlsx` | Three invoices with truly blank Vendor | Valid 0, invalid 3; data-quality XLSX; audit PASS |
| `04_bad_header.xlsx` | Mixed records with `PO_Amount` (not `POAmount`) | Intentional `Validate Excel Template` error |

All data is fabricated for training and demonstration; do not replace it with real client data when sharing this repository.
