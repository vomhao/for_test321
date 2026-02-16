- 01 -- bill type        : check for bi_hdr.invoice_type; also for REG , it's broken down to 014 to 017 
```
    011     Credits
    012     Credit/Rebill
    013     Bill Only
    014     Dropships
    015     Playground
    016     Training
    017     Stock
```

- 02 -- k_region_id_big  : check for k_region_id_big  
```
    021	    Eastern Region
    022	    Western Region
    023	    Midwest Region
    024	    Key Accounts
    025	    Marketplaces
    026	    Other
    027	    CA / NYC
```

- 03 -- product sub group: check from ps_k_billrpt_prdgr.cp_group
```
    031	    General
    032	    CAPLS
    033	    Playground
    034	    Technology
```

- 04 -- subcustqual      : check for subcust_qual1|| subcust_qual2
```
    041	    Head Start
    042	    Schools
    043	    For Profit
    044	    Agencies
    045	    Military
    046	    Other
```

- 05 --ord_source        : check for ps_km_ord_header.work_order_source
```
    051	    Public Web
    052	    Platinum
    053	    cXML
    054	    KapQuotes
    055	    Grants
    056	    Marketplaces
    057	    Other (Paper, email, fax)
```
