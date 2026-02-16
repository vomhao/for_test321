### BUSINESS_UNIT
```
    Business Unit
```
### PROCESS_INSTANCE
```
    Process Innstance: each run have an procoess instance assgined
```
### JOURNAL_DATE
```
    Date the Journal go to GL
```
### FISCAL_YEAR
```
    Fiscal Year
```
### ACCOUNTING_PERIOD
```
    Accounting Period
```
### JOURNAL_ID
```
    Journal ID
```
### INVOICE
```
    Invoice for Order. One order may have multiple invoices. One invoice may have multiple lines
```
### INVOICE_TYPE
```
    ACR    Credit Bill
    ADJ    Adjustment Only bill
    ARB    Rebill Bill
    REG    Regular Bill
```
### BILL_TYPE_ID
```
    REG  Regular
    BIO  Bill ONly
    ONL 
    PBL  Prebll

```
### BILL_SOURCE_ID
```
    OM      Order Managment
    ONL     Only
    Empty   Not filled
```
### LINE_SEQ_NUM
```
    Invoice's line_seq_num
```
### PRODUCT_ID
```
    Product ID of an order line or bi_line
```
### DESCR
```
    Product's description
```
### QTY
```
    Billed Quantity
```
### UNIT_OF_MEASURE
```
    Product's Unit of Measure
```
### ORDER_NO
```
    Order No 
```
### ORDER_INT_LINE_NO
```
    Order Line
```
### GROSS_EXTENDED_AMT
```
    ps_bi_acct_entry.monetary_amount(account: ('400110', '400111', '400140', '260000') ) -- I think there is no tax*  I will check later.
```
### LINE_COST
```
    Cost of product * Qty
```
### RMA_ID
```
    Rma Id (if invoice is an RMA invoice)
```
### RMA_LINE_NBR
```
    Rma line (if invoice is an RMA invoice)
```
### INV_ITEM_ID
```
    Product's inv_item_id 
```
### DROP_SHIP_FLAG
```
    Y: Dropship line 
```
### K_COMM_TYPE
```
    R     - Regular
    P     - Playground
    Empty
 
```
### k_training_flg
```
    Training Flag: From ps_prod_pgrp_lnk.prod_grp_type = 'RPT' &  ps_prod_pgrp_lnk.product_group like 'TN%'
```
### BILL_TO_CUST_ID
```
    BillTo Cust ID
```
### ADDRESS_SEQ_NUM
```
    BillTo's location
```
### SHIP_TO_CUST_ID
```
    ShipTo Cust ID
```
### SHIP_TO_ADDR_NUM
```
    Shipto's Location
```
### REGION_CD
```
    Territory (at the time this line created)
```
### SALES_PERSON
```
    Sales Persons(at the time this line created)
```
### K_REGION_ID_BIG
```
    Region Code(at the time this line created)
```
### PROD_GRP_TYPE
```
    PROD_GRP_TYPE: Empty
```
### PRODUCT_GROUP
```
    Product Group
```
### SUBCUST_QUAL1
```
    BillTo's Subcust_Qual1
```
### SUBCUST_QUAL2
```
    BillTo's Subcust_Qual2
```
### WORK_ORDER_SOURCE
```
    ps_km_ord_header.work_order_source
        Possible values:
            XM SR K1 BY AF PK AD VO CH ND TG
            MA PD GR KR XC WM RK EB KT XL OC
            GO OS QT KA IN AZ JT PA WS GC AO
            ML GH
```
### SOURCE_CD
```
    ps_ord_header.source_cd
        Possible values:
            EMAIL  MAIL   SHIP   SHOP   RMA    WALMRT
            FAX    TARGET VOIC   NORD   SHOWRM LOST
            SLSREP RAKUT  MCHLS  KTOYS  DEF    CSC
            GOOGLE MACY   KWANIS WEB    AMAZON EBAY
            JET    BID    AMROUT KROGER PHONE  DMGD
            KAPQUO SALES  ENTRY  WISH   OVRSTK CUSAT
```
### STATE
```
    Shipto's State
```
### K_BILLRPT_SUBGRP1
```
    bi_hdr.invoice_type; also for REG , it's broken down to 014 to 017
        011     Credits
        012     Credit/Rebill
        013     Bill Only
        014     Dropships
        015     Playground
        016     Training
        017     Stock
```
### K_BILLRPT_SUBGRP2
```
    Region:  
        021	    Eastern Region
        022	    Western Region
        023	    Midwest Region
        024	    Key Accounts
        025	    Marketplaces
        026	    Other
        027	    CA / NYC
```

### K_BILLRPT_SUBGRP3
```
    Product's sub group: check from ps_k_billrpt_prdgr.cp_group
        031	    General
        032	    CAPLS
        033	    Playground
        034	    Technology
```
### K_BILLRPT_SUBGRP4
```
    subcustqual      : subcust_qual1|| subcust_qual2
        041	    Head Start
        042	    Schools
        043	    For Profit
        044	    Agencies
        045	    Military
        046	    Other
```
### K_BILLRPT_SUBGRP5

```
    ord_source        : ps_km_ord_header.work_order_source
    051	    Public Web
    052	    Platinum
    053	    cXML
    054	    KapQuotes
    055	    Grants
    056	    Marketplaces
    057	    Other (Paper, email, fax)
```

### K_BILLRPT_SUBGRP6
```
    For future
```
