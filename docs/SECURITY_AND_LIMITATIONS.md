# Security, scope and known limitations

## Public export sanitation

The public workflow JSON is based on the provided 19-node export. Workflow-level ID, version/instance metadata, pin data and tag metadata were removed. Per-node UUIDs and the form-trigger webhook ID were **regenerated** before distribution. JavaScript, visible node names, parameters and connections were intentionally retained. No configured credentials appeared in the supplied export.

**Re-import caution:** The sanitized export is structurally validated as JSON with resolvable connection names, but has **not been live-imported into an external n8n instance** during packaging. After import, check node compatibility, form settings, and local permissions before enabling it.

## Current boundaries

1. **No ERP integration:** `POAmount` is a spreadsheet field, not an independent lookup of an approved purchase order.
2. **Report release is not gated by the audit:** XLSX conversion nodes run separately from `Downstream Integrity Check`. A failed audit does not automatically retract or block previously created files.
3. **No automated delivery:** XLSX files are generated as binary outputs inside n8n. Download from the execution UI; form upload does not return the reports to the uploader automatically.
4. **Template validation is narrow:** It recognizes punctuation/case variants of known headers, but not every arbitrary misspelling, completely absent header, empty worksheet, or corrupt XLSX. Row validation is relied on for most missing-value errors.
5. **Value checks are limited:** Numeric fields must be finite numbers, but the system does not validate business rules for negative amounts, currencies, dates, payment terms, taxes, PO line quantities, or vendor master data.
6. **No independent duplicate-upload prevention:** The `SourceRecordKey` changes for each n8n execution and is used for one-batch identity auditing, not cross-batch deduplication.
7. **No tolerance policy:** Amount mismatch uses exact inequality; this may be inappropriate for currency/rounding workflows without a defined tolerance.
8. **No measured operational impact:** No runtime benchmarks, invoice throughput, money saved, or deployment usage claims are made.
9. **Not for confidential uploads in a public demo:** Configure authentication/access restrictions, logging retention, credential handling, and secret management before using sensitive invoice data.
10. **No automated continuous integration:** Test cases were run manually and verified with screenshots.

## Production-minded future enhancements (not part of Version 1.0)

- Gate all report release on a successful audit.
- Add explicit failure response and notification to uploader, including malformed file and unexpected runtime errors.
- Enforce a strict input schema based on actual worksheet headers, including empty input and file-size checks.
- Introduce persistent audit logging, immutable batch ID, duplicate upload detection, and configurable tolerance.
- Validate POs and vendors against trusted ERP/master-data systems with permissioned credentials.
