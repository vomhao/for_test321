# KWORDSD2.SQR - Developer Summary

## Purpose

`KWORDSD2` is the download/publish step of the OMS pipeline. It reads orders from `ps_k_pick_stage` (populated by KWORDSD1), formats them into WMS flat-file structures, and writes them via database link (`@aad`) into the WMS staging tables (`t_al_host_order_master`, `t_al_host_order_detail`, etc.). It also handles denied credit card orders, writes catalog requests, and manages the WMS mark-for and queue workflow.

KWORDSD1 = **Select** releasable orders → `ps_k_pick_stage`
KWORDSD2 = **Download** orders from `ps_k_pick_stage` → WMS via DB link

## General Flow

```
begin-program (line 61)
│
├─ init-process
│    define-prcs-vars, get-run-control-parms, init-datetime
│
├─ check-if-not-to-run (line 78)
│    Checks ps_k_syn_date; halts if syn date matches today and runcntlid
│    matches KWORDSD1 or KWORDSD2
│
├─ main-process (line 100)
│   │
│   ├─ get-path-and-open-file (line 2044)
│   │    Reads dbproduction from ps_file_dir_fs to detect prod vs test
│   │
│   ├─ get-run-control-from-panel (line 1863)
│   │    Reads ps_k_runcntl_ordlx (oprid, run_cntl_id, demand_source='OM')
│   │    For each BU → process-by-bu
│   │
│   ├─ email-special-product (line 2169)
│   │
│   ├─ email-question-future-shipdate (line 154)
│   │    Emails dbarney/hvo if req_arrival_dttm > req_ship_dttm on pick-stage lines
│   │
│   ├─ perform-commit
│   │
│   ├─ manipulate-mark4 (line 3540)
│   │    Detects orders with >100 lines sharing the same mark-for text,
│   │    renumbers mark_for_id to distribute across batches
│   │
│   ├─ wms-process-header (line 2672)
│   │    Reads t_al_host_order_master (WMS response) and maps back to Kaplan tables
│   │
│   ├─ wms-process-line (line 3044)
│   │    Reads t_al_host_order_detail (WMS response)
│   │
│   ├─ wms-process-header-comment (line 3226)
│   │    Reads t_al_host_order_comment
│   │
│   ├─ wms-process-line-comment (line 3303)
│   │    Reads t_al_host_order_detail_comment
│   │
│   ├─ wms-process-queue (line 3383)
│   │    Reads t_al_host_sql_import_queue
│   │
│   ├─ perform-commit
│   │
│   ├─ delete-t-tables (line 3403)
│   │    Archives all t_al_host_* tables → adit_host_* tables, then deletes
│   │
│   └─ check-wa-status (line 3459)
│        Polls WA import status with k_sleep(15)
│
└─ end-process
```

---

## process-by-bu (line 1918)

Called once per BU from run control:

```
process-denided-creditcard-ord (line 2250)
│
├─ delete-unauthorized-orders  — removes pick-stage lines for denied CC orders
│                                also resets ps_in_demand in_fulfill_state 31→30
└─ generate-reason-code        — inserts reason code for deleted orders
                                 (uses sysadm.ps_ord_header/line directly)
│
get-ord-from-kpick (line 191)
│
└─ Loops over distinct (business_unit, demand_source, source_bus_unit,
    order_no, ship_to_cust_id, address_seq_ship) from ps_k_pick_stage
    where process_instance = #process_instance
    │
    ├─ insert-queue (line 309)
    │    Inserts t_al_host_sql_import_queue row (status='N')
    │
    ├─ get-ship-addr (line 331)
    │    Reads ps_cust_address + ps_customer for ship-to
    │    Handles address swap when address1 is empty (2025-08-04)
    │    Reads ps_k_cust_md_flags for k_address_type + DPV footnotes
    │    Determines residential flag based on subcust_qual2 (AZ/KT/SH/AT)
    │
    ├─ get-bill-addr (line 506)
    │    Reads ps_cust_address + ps_customer for bill-to
    │
    ├─ get-loc-addr (line 446)
    │    Reads ps_location_tbl for interunit shipments
    │
    ├─ process-header (line 588)
    │   │
    │   ├─ Init-Dlx-Header-Variable (line 668)
    │   │    Sets order_date, release_date, waveable_flg='Y',
    │   │    inside_delivery_flg, priority ('75' for UPSB/UPSR/R-type),
    │   │    ship_id, custcode (KITR/KASSY/KTECH for IN-type)
    │   │
    │   ├─ get-third-party (line 864)    [if carrier is blank]
    │   │    Reads ps_k_3rdprty_car2 for third-party freight bill-to
    │   │
    │   ├─ insert-header (line 703)
    │   │    Inserts into t_al_host_order_master@aad
    │   │    Fields: host_group_id, wh_id='LEWISVILLE', client_code,
    │   │    order_type, customer_code, ship_to_*, bill_to_*, delivery_*,
    │   │    freight_terms, COD amount, insurance, residential flag,
    │   │    pack_and_hold, inside_delivery, require_signature, liftgate,
    │   │    earliest/latest ship/delivery dates
    │   │
    │   ├─ process-back-order (line 1221)
    │   │    Finds lines with in_fulfill_state='20' for same order/shipto,
    │   │    writes back-order comments via write-comment-hdr
    │   │
    │   ├─ write-Drop-ship (line 1680)
    │   │    Writes dropship order records if applicable
    │   │
    │   └─ write-BOL (line 1474)
    │        Writes delivery instruction lines as D/H-type comments
    │        (inside delivery, call before, delivery after/by, dates,
    │        liftgate/ring, pack_hold)
    │
    ├─ process-lines (line 912)
    │    Loops over ps_k_pick_stage lines + ps_k_mark_for_tbl for the order
    │    │
    │    ├─ get-ISBN-number (line 1058)
    │    ├─ get-qty-per-for-kit (line 1077)  [if product_kit_flag='Y']
    │    │    Reads ps_purch_kit_mstr + ps_purch_kit_tbl + ps_prodkit_comps
    │    │    for kit component quantities
    │    ├─ get-key-item (line 1030)         [if KAPASSY customer]
    │    │    Reads ps_prod_item for key item details
    │    │
    │    ├─ write-lines (line 1153)
    │    │    Inserts t_al_host_order_detail@aad
    │    │    Fields: item_number, order_qty, cust_po_line_no, sku_descr,
    │    │    kx_order_line, mark_for_nbr, mark_for_text 1-5,
    │    │    platform ('WOOD' or 'PACK'), product_source='W', isbn
    │    │
    │    ├─ insert-comment-detail (line 1543)
    │    │    Writes kit component breakdown as P-type detail comments
    │    │
    │    ├─ write-gift-note (line 1578)
    │    │    Reads ps_k_ord_message for gift message text
    │    │
    │    ├─ update-dlx-order-no-and-line (line 1100)
    │    │    Updates ps_k_pick_stage with dlx_order_no and kx_order_line
    │    │
    │    └─ update_ps_in_demand (line 1126)
    │         Updates ps_in_demand in_fulfill_state → '31' (processing)
    │
    ├─ write-comment-hdr (line 1730)
    │    Generic writer for t_al_host_order_comment@aad
    │
    ├─ create-dlx-order-num (line 1762)
    │    Reads ps_kx_ord_ship_trc to generate/incr sequence number
    │
    ├─ insert-comment-detail (line 1547)
    │    Writes t_al_host_order_detail_comment@aad for kit messages
    │
    └─ process-catalog (line 2364)
         If order contains catalog items, inserts ps_k_catalog_rqst
         and ps_k_cat_rqst_dtl for fulfillment
│
├─ check-for-number-line-sent (line 2126)
│    Reports if no lines were sent for the run
│
└─ reset-flags (line 2218)
     Updates ps_km_ord_header k_reset_flag and ps_k_ord_aux
```

---

## WMS Round-Trip Processing

After orders are written to the WMS DB link, KWORDSD2 reads back the WMS response:

```
wms-process-header (line 2672)
    Reads t_al_host_order_master@aad
    Maps WMS-assigned fields back to Kaplan order tables
    Inserts into t_al_host_order_master@aad (finalize)

wms-process-line (line 3044)
    Reads t_al_host_order_detail@aad
    Inserts into t_al_host_order_detail@aad (finalize)

wms-process-header-comment (line 3226)
    Reads t_al_host_order_comment@aad → t_al_host_order_comment@aad

wms-process-line-comment (line 3303)
    Reads t_al_host_order_detail_comment@aad → t_al_host_order_detail_comment@aad

wms-process-queue (line 3383)
    Reads t_al_host_sql_import_queue@aad for import status
    Calls wms-insert-queue (line 3511)

manipulate-mark4 (line 3540)
    Detects orders in t_al_host_order_detail with >100 lines
    sharing identical mark_for text → renumbers mark_for_id to spread across buckets

check-wa-status (line 3459)
    k_sleep(15) then checks t_al_host_sql_import_queue for completion status
```

---

## Credit Card Denial Handling

`process-denided-creditcard-ord` (line 2250):

```
For each order in ps_k_pick_stage where:
  - payment_method = 'CC'
  - cr_card_auth_stat in ('U','D')
  - shipped_flag in ('N','T')
  - business_unit = run control BU

  → delete-unauthorized-orders
       DELETE from ps_k_pick_stage where in_fulfill_state='31'
       UPDATE ps_in_demand set in_fulfill_state='30'
       (resets so order can be retried or reason-coded)

  → generate-reason-code
       INSERT ps_k_reasncode_tbl with reason code for denied CC orders
```

---

## Key Variables Set Per Header

| Variable | Source | Notes |
|----------|--------|-------|
| `wh_id` | hardcoded | `'LEWISVILLE'` |
| `host_group_id` | `#process_instance` | Groups orders by SQR run |
| `custcode` | ship_id | For IN-type orders: `'91154715-1'`, `'91067628-1'` |
| `carrier_id` | `&h_carrier_id` or `get-third-party` | Third-party lookup via `ps_k_3rdprty_car2` |
| `priority` | `'75'` if UPSB/UPSR/R-type | Blank otherwise |
| `order_type` | `'CUST'` (OM) or `'INHS'` (IN) | |
| `ship_id` | `ship_to_cust_id || '-' || addr_seq_ship` | |
| `bill_id` | `bill_to_cust_id || '-' || addr_seq_bill` | |
| `residential_flg` | `k_address_type` or `subcust_qual2` | |
| `inside_delivery_flg` | `del_inst1 = 'Inside Del'` | |

---

## All Tables in KWORDSD2.SQR

Organized by functional area. `(*)` = archived/deleted each run. `(@)` = written via DB link `@aad`.

### Run Control

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_runcntl_ordlx` | Yes | No | BU, demand_source, order_no, max_line_count |
| `psprcsrqst` | Yes | No | dbname, process_instance |
| `ps_k_syn_date` | Yes | No | Stop-run guard |
| `ps_file_dir_fs` | Yes | No | Detects production database |

### Pick Stage Input (from KWORDSD1)

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_pick_stage` | Yes | Yes | Source of orders to download; updated with dlx_order_no |

### WMS Staging — Local (*)

| Table | Archived* | Deleted* | Written | Role |
|-------|-----------|----------|---------|------|
| `t_al_host_order_master` | Yes | Yes | Yes (@aad) | WMS header — read back after WMS processing |
| `t_al_host_order_detail` | Yes | Yes | Yes (@aad) | WMS line — read back after WMS processing |
| `t_al_host_order_comment` | Yes | Yes | Yes (@aad) | Header comments (BOL instructions, gift notes) |
| `t_al_host_order_detail_comment` | Yes | Yes | Yes (@aad) | Line comments (kit breakdown) |
| `t_al_host_sql_import_queue` | Yes | Yes | Yes (@aad) | Import queue control record |

### Archive Tables (* → adit_host_*)

| Table | Written | Role |
|-------|---------|------|
| `adit_host_order_master` | Yes | Archive of t_al_host_order_master |
| `adit_host_order_detail` | Yes | Archive of t_al_host_order_detail |
| `adit_host_order_comment` | Yes | Archive of t_al_host_order_comment |
| `adit_host_order_detail_comment` | Yes | Archive of t_al_host_order_detail_comment |
| `adit_host_sql_import_queue` | Yes | Archive of t_al_host_sql_import_queue |

### Core Order / OMS

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_ord_header` | Yes | No | Order header data, customer PO, req_arrival_dttm |
| `ps_ord_line` | Yes | No | Line data, customer PO line, kit flag, qty |
| `ps_ord_schedule` | Yes | No | Scheduled qty for gift note check |
| `ps_km_ord_header` | Yes | No | Kaplan order type, demand source, catalog flag |
| `ps_k_ord_aux` | Yes | Yes | Auxiliary order data; reset_flag updated |
| `ps_k_ord_message` | Yes | No | Gift note text per order |

### Customer / Address

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_cust_address` | Yes | No | Ship-to and bill-to addresses |
| `ps_customer` | Yes | No | Customer name, subcust_qual (catalog eligibility) |
| `ps_k_cust_md_flags` | Yes | No | Address type, DPV footnotes, residential flag |
| `ps_country_tbl` | Yes | No | Country code conversion (3-char state, 2-char country) |
| `ps_cust_contact` | Yes | No | Contact info for catalog requests |
| `ps_cust_cntct_phn` | Yes | No | Contact phone for catalog |

### Product / Kit

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_prod_item` | Yes | No | Product description, ISBN lookup |
| `ps_master_item_tbl` | Yes | No | Master item description (back-order lines) |
| `ps_prodkit_comps` | Yes | No | Kit component items |
| `ps_purch_kit_mstr` | Yes | No | Kit master for MSR/KAPASSY orders |
| `ps_purch_kit_tbl` | Yes | No | Kit component quantity breakdown |
| `ps_bu_items_inv` | Yes | No | Inventory qty_available for catalog eligibility |

### MSR / Amazon

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_amazon_orders` | Yes | No | Amazon-specific order handling |
| `ps_k_mark_for_tbl` | Yes | No | Mark-for text and number per line |

### Third-Party Freight

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_3rdprty_car2` | Yes | No | Third-party freight bill-to account lookup |

### Billing / Revenue

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_bi_line` | Yes | No | Revenue lines — checked for dropship gift notes |

### Sequence / Tracking

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_kx_ord_ship_trc` | Yes | Yes | Per-order ship sequence number (DLX order tracking) |

### Catalog Request

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_catalog_rqst` | No | Yes | Catalog request header |
| `ps_k_cat_rqst_dtl` | No | Yes | Catalog request detail |

### Inventory / Demand

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_in_demand` | Yes | Yes | Fulfill state transitions (30→31 on download) |

### Location

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_location_tbl` | Yes | No | Interunit address resolution |
| `ps_set_cntrl_rec` | Yes | No | SetID lookups for prod_item |

### Reason Codes

| Table | Read | Written | Role |
|-------|------|---------|------|
| `ps_k_reasncode_tbl` | Yes | Yes | Reason codes for denied CC orders |
