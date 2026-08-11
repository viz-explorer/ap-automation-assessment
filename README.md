# AP Invoice Processing Pipeline — Case Study Materials

## Overview

These materials form the input dataset for a technical recruitment case study. Candidates are asked to build an automated Accounts Payable invoice processing pipeline that validates, enriches, and routes incoming invoices against a vendor master and historical data.

---

## Files

### Reference Data

| File | Description |
|---|---|
| `vendor_master.csv` | Master list of 25 vendors with categories, payment terms, approved currencies, spend thresholds, and status (3 INACTIVE vendors, 1 requiring dual approval) |
| `historic_invoices.csv` | 120 historical invoice records from Jan 2024 – Jun 2025, used for duplicate detection and pattern validation |
| `data_dictionary.md` | Field-level descriptions for all columns in both CSV files |

### Invoice PDFs (`invoices/`)

#### Clean invoices (processable, no exceptions)

| File | Vendor | Scenario |
|---|---|---|
| `INV-2025-03701_V001_EuroFuel.pdf` | V001 EuroFuel GmbH | Standard fuel invoice, June 2025 |
| `INV-2025-03712_V004_FleetMech.pdf` | V004 FleetMech GmbH | Standard maintenance invoice |
| `INV-2025-03719_V005_AutoServ.pdf` | V005 AutoServ S.R.L. | Standard maintenance invoice |
| `INV-2025-03744_V008_CleanStation.pdf` | V008 CleanStation S.A. | Standard cleaning invoice |
| `INV-2025-03760_V010_StationOps.pdf` | V010 StationOps GmbH | Standard station operations invoice |
| `INV-2025-03771_V012_MediaBoost.pdf` | V012 MediaBoost GmbH | Standard marketing invoice |
| `INV-2025-03779_V013_NordClean.pdf` | V013 NordClean ApS | Standard cleaning invoice |
| `INV-2025-03791_V014_IberMaint.pdf` | V014 IberMaint S.L. | Standard maintenance invoice |

#### Exception invoices

| File | Vendor | Exception Type |
|---|---|---|
| `INV-2025-03788_V002_AlpinaDiesel.pdf` | V002 AlpinaDiesel AG | **High-value clean** — €42,121 fuel invoice, within threshold, legitimate |
| `INV-2025-03847_V003_TotalRoute.pdf` | V003 TotalRoute S.A. | **Near-duplicate** — €8,450, same vendor/amount as INV-2024-00892 in history, slightly different date and number |
| `INV-2025-03901_V007_SecureGuard.pdf` | V007 SecureGuard AG | **Dual approval required** — V007 requires two approvers regardless of amount (€18,500) |
| `INV-2025-03731_V006_BritTech_GBP.pdf` | V006 BritTech Solutions Ltd | **Currency mismatch** — vendor approved for EUR, invoice submitted in GBP (£4,998) |
| `INV-2025-03752_V009_SparkIT_NoPO.pdf` | V009 SparkIT BV | **Missing PO number** — PO reference field is blank |
| `INV-2025-03880_V018_OldGuard_INACTIVE.pdf` | V018 OldGuard Security NV | **Inactive vendor** — V018 is INACTIVE in vendor master |
| `INV-2025-03955_UNKNOWN_LogiTrans.pdf` | LogiTrans Express SRL | **Unknown vendor** — vendor not present in vendor master |
| `INV-2025-SCAN-001_V015_QuickFix.pdf` | V015 QuickFix OÜ | **Scan simulation** — less structured layout simulating a scanned paper invoice |
| `INV-2025-SCAN-002_V016_PolyClean.pdf` | V016 PolyClean Sp.z.o.o. | **Scan simulation** — less structured layout simulating a scanned paper invoice |

---

## Key Relationships and Test Cases

- **Duplicate detection:** `INV-2025-03847` (V003, €8,450, 2025-07-02) is a near-duplicate of `INV-2024-00892` in `historic_invoices.csv` (same vendor, same amount, different date/number).
- **Threshold logic:** V007 has `requires_dual_approval = TRUE`; all V007 invoices should route for dual sign-off regardless of amount.
- **Currency validation:** Each vendor has an `approved_currency` in the master; invoices in any other currency should be flagged.
- **Vendor status check:** V018, V021, V024 are INACTIVE; invoices from these vendors should be rejected.
- **Unknown vendor:** LogiTrans Express SRL has no matching `vendor_id` in the master.
- **PO enforcement:** Missing PO numbers should trigger an exception or hold.
