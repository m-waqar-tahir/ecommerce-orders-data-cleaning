# E-commerce Orders: Data Cleaning & Preparation

A reproducible Python (pandas) workflow that audits, cleans and verifies a raw e-commerce orders dataset. The main walkthrough is a Jupyter notebook, backed by an equivalent command-line script and a PDF change log documenting every decision.

Built for **Project 1: Data Cleaning & Preparation** of the DecodeLabs Data Analytics Internship (Batch 2026).

## Results at a glance

| Metric | Result |
|---|---|
| Rows in / out | 1,200 / 1,200 (nothing deleted) |
| Columns | 14 |
| Order date range | 2023-01-01 to 2025-06-30 |
| Duplicate OrderIDs | **0** |
| Incorrectly formatted dates | **0** |
| Missing values remaining | **0** |
| Automated tests | 8 passing |

The raw file was in better condition than a typical extract. The audit found one substantive gap (309 blank `CouponCode` values) plus formatting inconsistencies. Everything else was checked and confirmed clean. The change log reports both what was fixed and what was tested and found fine.

## What was changed

| ID | Change | Records |
|---|---|---|
| CR001 | Blank `CouponCode` replaced with the label `No Coupon` | 309 |
| CR002 | `UnitPrice` and `TotalPrice` fixed to 2 decimals (e.g. `224` to `224.00`); no numeric value altered | 260 rows |
| CR003 | Saved as UTF-8 with LF line endings (raw used Windows CRLF) | file-level |

**Start with the notebook:** [`data_cleaning_project1.ipynb`](data_cleaning_project1.ipynb) walks through every step with outputs. Full detail, including the audit checks that found nothing to fix, is in [`/Data_Cleaning_Change_Log.pdf`](Data_Cleaning_Change_Log.pdf).

### Why blanks became "No Coupon" instead of being deleted or imputed

A blank `CouponCode` means the customer did not use a coupon. That is information, not missing data. Deleting those 309 rows would throw away 25.8% of the orders, and filling them with the most common code (mode imputation) would invent discounts that never happened. An explicit label keeps every row and keeps the meaning.

## Dataset

| Column | Type | Description |
|---|---|---|
| `OrderID` | text | Unique order identifier, format `ORD######` |
| `Date` | date | Order date, ISO 8601 (`YYYY-MM-DD`) |
| `CustomerID` | text | Customer identifier, format `C#####` |
| `Product` | text | Laptop, Phone, Tablet, Monitor, Printer, Desk or Chair |
| `Quantity` | integer | Units ordered (1 to 5) |
| `UnitPrice` | decimal (2 dp) | Price per unit |
| `ShippingAddress` | text | Delivery address |
| `PaymentMethod` | text | Online, Cash, Credit Card, Debit Card or Gift Card |
| `OrderStatus` | text | Pending, Shipped, Delivered, Cancelled or Returned |
| `TrackingNumber` | text | Unique shipment reference, format `TRK########` |
| `ItemsInCart` | integer | Items in the cart at checkout |
| `CouponCode` | text | SAVE10, FREESHIP, WINTER15 or `No Coupon` |
| `ReferralSource` | text | Instagram, Email, Google, Facebook or Referral |
| `TotalPrice` | decimal (2 dp) | `Quantity x UnitPrice` |

## Things to know before analysing this data

These were flagged but deliberately **not** altered, because changing them would mean inventing data:

- `TotalPrice` equals `Quantity x UnitPrice` on every row, so coupon discounts and shipping are not reflected in it.
- 497 orders (41.4%) are Cancelled or Returned but still carry a `TotalPrice`. Filter on `OrderStatus` before calculating revenue.
- All 487 Cancelled or Pending orders have a `TrackingNumber`, which is unusual for orders that never shipped.
- `UnitPrice` varies widely within one product (Laptop ranges from 18.20 to 699.93), so it is not a stable catalogue price.
- 11 customers placed more than one order. These are repeat customers, not duplicates.

## Verification

The pipeline re-reads the **saved** cleaned file and checks it, rather than trusting the in-memory result. The two gate checks come straight from the training kit's Project 2 threshold:

1. Zero duplicate IDs
2. Zero incorrectly formatted dates

Eight further checks cover duplicate rows and tracking numbers, remaining nulls, ID formats, price precision, numeric types, `TotalPrice` arithmetic and row count. The script exits with an error if any check fails.

The last notebook cell confirms its output is identical to the script's. The `tests/` folder runs the same logic against a deliberately messy synthetic table (duplicates, mixed date formats, inconsistent case, stray whitespace, missing values), which shows the pipeline handles real mess and not just this file.


## How to run

```bash
git clone <your-repo-url>
cd <repo-folder>
pip install -r requirements.txt

jupyter notebook notebooks/data_cleaning_project1.ipynb   # run all cells


```

Output is deterministic. Re-running produces identical files, and the SHA-256 hashes of the raw and cleaned files are recorded in the change log.

## Tech stack

Python 3.10+, pandas, Jupyter.

## Author

Muhammad Waqar Tahir
