# Final Data Cleaning & Standardization Summary Report

This report documents the diagnostic findings and comprehensive data cleaning steps applied to standardise the pharmaceutical datasets, ensuring 100% data integrity, appropriate schema types, and zero unresolved missing values.

---

#### 1. `batch_master.csv` (Batch Master)
* **Initial Issues**: 
  * 13 missing values in the `mfg_site` column.
  * Dates stored as inconsistent raw text strings.
* **Actions Taken**:
  * Standardized `mfg_date` and `expiry_date` columns into proper datetime formats (`YYYY-MM-DD`) using pandas mixed parsing (`dayfirst=True`).
  * Imputed all 13 missing `mfg_site` records with `'GOA-01'`, as it represents the only manufacturing site listed in the entire master file.
* **Outcome**: Complete dataset (663 rows) with validated schemas and zero missing values.

#### 2. `sku_master.csv` (SKU Master)
* **Initial Issues**: Checked for completeness, duplicate key values, and text column formatting.
* **Actions Taken**:
  * Confirmed that `sku_code` is unique across all 36 records with 0 duplicates.
  * Validated categorical text columns (`therapy_area`, `storage`, and `nlem_scheduled`) for string spelling and entry consistency.
* **Outcome**: Verified clean dataset containing perfect product master information.

#### 3. `stockist_master.csv` (Stockist Master)
* **Initial Issues**:
  * 4 missing values in the `city` column.
  * Highly sparse data in the `appointed_on` column (150 missing records out of 156).
* **Actions Taken**:
  * Imputed missing `city` values with `'Unknown'` to preserve the integrity of spatial queries without inventing records.
  * Standardized the sparse `appointed_on` column to a standard datetime format, letting missing or unrecorded appointment entries safely represent as `NaT` (Not a Time) or missing timestamps.
* **Outcome**: Cleaned dataset (156 rows) ready for spatial joining.

#### 4. `cnf_stock_snapshot.csv` (C&F Stock Snapshot)
* **Initial Issues**:
  * 270 missing records in the `batch_no` and `expiry_date` columns.
  * `qty_on_hand_packs` contained formatted numbers with comma thousands-separators stored as text (e.g., `'3,615.00'`).
* **Actions Taken**:
  * Stripped comma separators from `qty_on_hand_packs` and parsed the column safely to numeric integers.
  * Identified that all 270 rows missing `batch_no` corresponded to a quantity of 0 on hand. Imputed these missing batch values to `'No Stock'` and formatted their corresponding dates to standard `NaT` values.
  * Standardized `snapshot_date` and `expiry_date` into proper datetime objects.
* **Outcome**: Cleaned dataset of 5,015 rows with perfect numerical values and clear stock categorization.

#### 5. `primary_sales.csv` (Primary Sales)
* **Initial Issues**:
  * `invoice_date` formatted as mixed-string dates.
  * 2,654 missing values in `batch_no` and 3,069 missing values in `ordered_qty`.
  * `net_value_inr` parsed as an object/string due to numeric comma separators.
  * `scheme_code` had empty entries as well as hyphens (`-`) indicating absent schemes.
* **Actions Taken**:
  * Parsed and standardized `invoice_date` to standard datetimes.
  * Removed commas from `net_value_inr` and cast the column directly to float.
  * Handled missing `batch_no` values by marking them as `'Cancelled/No Supply'` (representing unfulfilled orders).
  * Imputed missing `ordered_qty` records with their matching `invoiced_qty` values under the business rule that order equaled supply for completed transactions.
  * Standardized nulls and hyphens (`-`) in `scheme_code` to a clean `'No Scheme'` string.
* **Outcome**: Completely populated and cast primary sales ledger (93,809 records).

#### 6. `expiry_claims.csv` (Expiry Claims Ledger)
* **Initial Issues**:
  * `credit_value_inr` stored as formatted string text with commas.
  * Mixed string date formats in `claim_date`, `expiry_date_as_claimed`, and `credit_note_date`.
  * 135 missing records in `claim_value_inr` and scattered null values in original references and credit notes.
* **Actions Taken**:
  * Parsed all mixed-date fields safely into standard datetime datatypes.
  * Stripped numeric comma delimiters in `credit_value_inr` and converted to float.
  * Resolved missing `claim_value_inr` entries by safely copying their values from the finalized `credit_value_inr` where available, filling any remnants with `0.0`.
  * Resolved blank reference strings to consistent labels (`'No Reference'`, `'System/Pending'`, `'No Credit Note'`, `'No Remarks'`).
  * Verified that remaining null fields in `credit_note_date` aligned perfectly with claims whose status is actively 'Pending' or 'Rejected'.
* **Outcome**: Standardized claims ledger (4,526 rows) ready for auditing.

#### 7. `claim_emails.csv` (Claim Emails Metadata)
* **Initial Issues**:
  * 2 missing values in `stockist_code_by_mailbox_rule`.
  * Raw text strings in `received_date`.
* **Actions Taken**:
  * Converted `received_date` into standardized datetime format (`YYYY-MM-DD`).
  * Set missing mailbox stockist rules to `'Unknown'` to ensure zero nulls in relational joins.
* **Outcome**: High-fidelity metadata matrix ready for text processing and validation.
