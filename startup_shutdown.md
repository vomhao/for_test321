key: oracle -- servers -- Server & Mount Points & Startup_Shutdown
# For Shutdown
## ps_rman_netbackup
- ``root``: stop netbackup
    ```
    netbackup stop
    ```

- ``oracle``: check listener
    ```
    ps -ef | grep LISTENER
    ```
- ``oracle``: check listener result
    ```
    oracle   17950     1  0 Jun28 ?        00:00:59 /u01/app/oracle/product/12.1.0/bin/tnslsnr LISTENER -inherit
    oracle   26004 21553  0 08:42 pts/0    00:00:00 grep LISTENER
    ```
- ``oracle``: check how many oracle pmon process
    ```sql
    pmon
    -- oracle    4046     1  0 Jun26 ?        00:02:32 ora_pmon_RMANCAT2
    -- oracle    4617     1  0 Jun26 ?        00:01:54 ora_pmon_RMANCAT
    ```

- ``oracle``: shutdown rman_cat
    ```sql
    at linux prompt:
        rman_cat
        sqli
    at sql prompt:
        alter system switch logfile;
        shutdown immediate;
        exit;
    ```


## ps_prod_db
- ``oracle``: check listener
    ```
    ps -ef | grep LISTENER
    ```
- ``oracle``: check listener result
    ```
    oracle    14184      1  0 Jun17 ?        00:00:30 /u01/app/oracle/product/19.3.0/bin/tnslsnr LISTENER_GTW19C -inherit
    oracle    14993      1  0 Mar06 ?        00:34:59 /u01/app/oracle/product/12.1.0.2//bin/tnslsnr LISTENER -inherit
    ```

- ``oracle``: check how many process
    ```sql
    pmon
        ora_pmon_PSPROD92
    ```

- ``oracle``: Shutdown psprod92
    ```sql
    at linux prompt, enter command:
        psprod92
        sqli
    at sql prompt:
        alter system switch logfile;
        shutdown immediate;
        exit;
    ```
- ``oracle``: stop the main listener
    ```
    lsnrctl stop
    ```

- ``oracle``: stop the other listener
    ```
    export ORACLE_HOME=/u01/app/oracle/product/19.3.0
    export PATH=/u01/app/oracle/product/19.3.0/bin:$PATH
    export LD_LIBRARY_PATH=/u01/app/oracle/product/19.3.0/lib
    export TNS_ADMIN=/u01/app/oracle/product/19.3.0/network/admin
    lsnrctl stop LISTENER_GTW19C
    ```

## other databases
```
  Host       | DB
  -----------|----------
  ps_dev_db  | PSDEV92
  ps_test_db | PSTEST92
  ps_wms_db  | PSWMS92
```

- ``oracle``: check listener
    ```
    ps -ef | grep LISTENER
        oracle     4892      1  0 Apr13 ?        00:06:51 /u01/app/oracle/product/12.1.0.2//bin/tnslsnr LISTENER -inherit
        oracle    16565      1  0 Apr13 ?        00:01:52 /u01/app/oracle/product/19.3.0/bin/tnslsnr LISTENER_GTW19C -inherit
    ```

- ``oracle``: check how many oracle pmon process
    ```sql
    pmon
        oracle     2329      1  0 Jul07 ?        00:00:52 ora_pmon_PSTEST92
        4 Database(s)
    ```

- ``oracle``: Shutdown all DB: pstest92, pswms92, psdev92, pslink92
    ```sql
    at linux prompt, enter command:
        pstest92
        sqli
    at sql prompt:
        alter system switch logfile;
        shutdown immediate;
        exit;
    ```

# For Startup
## panther
- ``oracle``: start other listener, enter these commands
    ```
    export ORACLE_HOME=/u01/app/oracle/product/19.3.0
    export PATH=/u01/app/oracle/product/19.3.0/bin:$PATH
    export LD_LIBRARY_PATH=/u01/app/oracle/product/19.3.0/lib
    export TNS_ADMIN=/u01/app/oracle/product/19.3.0/network/admin
    lsnrctl start LISTENER_GTW19C
    ```

- ``oracle``: start main listener -- enter these commands:
    ```
    psprod92
    lsnrctl start
    ```

- ``oracle``: startup database -- enter these command at linux prompt:
    ```
    psprod92
    lsnrctl start
    sqli

    at sql prompt:
        startup;
        alter system register;
        exit;
    ```

## ps_rman_netbackup
- ``oracle``: start listener
    ```
    lsnrctl start
    ```
- ``oracle``: start RMAN_CAT database
    ```
    at linux prompt, enter command:
        rman_cat
    at sql prompt, enter command:
        startup
        alter system register;
        exit;
    ```

- ``root``: start netbackup
    ```
    netbackup start
    ```

## Other databases
- do like ps_prod_db 

# Servers
```
PSPROD92 files:
    ------------------------------------------------------------------
    ps_prod_db -- PSPROD92
    ------------------------------------------------------------------
    Backup:               |/NETBACKUP_PROD92/Oracle/PSPROD92/
    Backup:               |/NETBACKUP_PROD92/Oracle/PSPROD92/CTL_AUTO/
    ADR Home              |/u01/app/oracle/diag/rdbms/psprod92/PSPROD92
    Datafile_ControlFile: |/u02/app/oracle/oradata/PSPROD92/
    Online Logs:          |/u03/app/oracle/oradata/PSPROD92/
    Online Logs:          |/u04/app/oracle/oradata/PSPROD92/
    Archived Logs:        |/u05/app/oracle/oradata/PSPROD92/arch
    Archived Logs:        |/u06/app/oracle/oradata/PSPROD92/arch

All Database Servers IP:
    -------------------------------------------------------------------------------
    Enviroment    |Host Name          |IP1             |IP2            |DBID
    -------------------------------------------------------------------------------
    PSPROD92 DB   |ps_prod_db         |10.177.235.51   |10.10.10.145   |2871989713
    PSPROD92 DB   |ps_prod_db         |10.177.235.51   |10.10.10.145   |2871989713
    PSDEV92 DB    |ps_dev_db          |10.177.235.40   |10.10.10.144   |2919785323
    PSTEST92 DB   |ps_test_db         |10.177.235.7    |10.10.10.143   |3797203043
    PSWMS92 DB    |ps_wms_db          |10.177.235.5    |10.10.10.139   |1483878796
    standby DB    |ps_standby_db      |10.177.235.57   |10.10.10.137   |2008096677
    Backup DB     |ps_rman_netbackup  |10.177.235.1    |10.10.10.140   |548345367

All App Servers IP:
    -------------------------------------------------------------------
    Enviroment    |Host Name          |IP1             |IP2            
    -------------------------------------------------------------------
    PSPROD92      |ps_prod_app        |10.177.235.151  |               
    PSPROD92      |ps_prod_prcs       |10.177.235.155  |10.10.10.135   
    PSPROD92      |ps_prod_web        |10.177.235.153  |               
    PSDEV92       |psdevawp           |10.177.235.23   |               
    PSTEST92      |pstestawp          |10.177.235.8    |               
    PSWMS92       |pswmsawp           |10.177.235.174  |               
```
wh|batch>else
consearchcat@sea
