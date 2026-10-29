# Portfolio demo script (about 3 minutes)

## 0:00–0:25 — Explain the business problem

“AP teams receive invoice spreadsheets that can contain missing supplier details, amounts that disagree with PO values, and duplicate invoice identifiers. I built this workflow in n8n to classify exceptions and generate audit-friendly reconciliation outputs.”

## 0:25–1:00 — Show the architecture

Show [`screenshots/workflow-overview.png`](screenshots/workflow-overview.png) or the live n8n canvas. Point to Excel upload, template check, invoice validation, the valid/invalid split, and the two XLSX generators.

## 1:00–1:50 — Run the mixed batch

Upload `sample_inputs/01_mixed_batch.xlsx` in a **local test environment**. Explain that `INV-006` is intentionally missing a vendor. Show the 5/1 classification, three amount differences and two matched invoices, plus the financial totals (7,550 invoice total; 7,200 PO total; +350 net difference).

## 1:50–2:20 — Show audit evidence

Show the `Downstream Integrity Check` output: counts, `MissingRecordKeys: []`, `DuplicateRecordKeys: []`, `RecordIdentityCheck: PASS`, `AuditStatus: PASS`.

## 2:20–2:40 — Demonstrate the negative test evidence

Use the saved screenshot `screenshots/duplicate-detected.png`. Explain that a duplicate replaced a missing record while the count stayed six; the identity check returned `FAIL`. Mention that the simulated mutation was reverted.

## 2:40–3:00 — Close with limitations

“This is a rules-based demo, not production deployment. PO amounts come from the same Excel upload; it doesn’t query an ERP system. Reports are generated inside n8n and are not released or delivered conditionally based on the audit yet.”

## Tips

- Use only synthetic data. Blur any local URLs, secrets, or user account identifiers in new screenshots.
- Prefer actual, repeatable demonstrations to claims about cost savings or throughput that have not been measured.
- Name the solution **Automated AP Invoice Reconciliation & Exception Management** on your resume or portfolio.
