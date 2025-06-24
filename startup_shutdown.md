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
- do like ps_prod_db
```
  Host       | DB
  -----------|----------
  ps_dev_db  | PSDEV92
  ps_test_db | PSTEST92
  ps_wms_db  | PSWMS92
```



# For Startup
## ps_prod_db
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

# Restart Web,App,Prcs
- restart web, app, prcs server
```sh
# login into these 3 servers: psprodweb, psprodapp and psprodprcs
# enter this command to re-start server for each server
    stop_start.sh
```


