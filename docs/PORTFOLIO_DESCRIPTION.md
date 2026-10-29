# Suggested portfolio description

**Project:** Automated AP Invoice Reconciliation & Exception Management (n8n / JavaScript / Excel)

Built and tested a rules-based invoice reconciliation workflow that imports XLSX records, validates critical fields, identifies invoice-versus-PO amount differences, and produces financial reconciliation and data-quality exception files. Implemented execution-specific record keys and automated count/identity audits to flag missing, duplicate, and unexpected downstream records. Verified mixed, all-valid, all-invalid, malformed-header, and deliberate audit-failure scenarios using synthetic data.

**Scope clarification:** PO amounts were supplied in the same source workbook; there is no independent ERP retrieval, automatic report delivery, or production deployment.

**Possible resume bullets (choose a version that matches your experience):**

- Developed an n8n and JavaScript workflow to validate Excel invoice batches, classify invoice-to-PO amount differences, and generate reconciliation/exception XLSX outputs.
- Added batch-completeness and source-record integrity controls; demonstrated detection of a duplicate record replacing a missing invoice in a six-row synthetic test.

Avoid claiming live production time savings or automation volume without measured data.
