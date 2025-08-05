# KAPCO - Normal
## UPS - USA - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : USA
    ```
- ``Less than $7500``
    ```
    Non-Truck Order:
        Percentage     : 15%
        Mininum Charge : $6.75
    Truck Order:
        Percentage     : 15%
        Mininum Charge : $62.5
    ```
- ``More than $7500``
    ```
        Free Freight
    ```
## UPS - CAN - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : Canada
    ```
- ``Less than $1500``
    ```
    Non-Truck Order:
        Percentage     : 25%
        Mininum Charge : $14
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```
- ``More than $1500``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```

## UPS - HI/AK/PR - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    State         : HI/AK/PR
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Percentage     : 25%
        Mininum Charge : $5
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```
## 2DAYAIR - USA - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : 2DAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : USA
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Percentage     : 25%
        Mininum Charge : $14.56
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```

## 2DAYAIR - CAN - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : 2DAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : Canada
    ```
- ``Any Amount``
    ```
        Non applicable --> Manually Firm Freight 
    ```

## 2DAYAIR - HI/AK/PR - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : 2DAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    State         : HI/AK/PR
    ```
- ``Any Amount``
    ```
        Non applicable --> Manually Firm Freight 
    ```


## NXTDAYAIR - USA - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : NXTDAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : USA
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Percentage     : 36.4%
        Mininum Charge : $34.4
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```

## 2DAYAIR - CAN - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : NXTDAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : Canada
    ```
- ``Any Amount``
    ```
        Non applicable --> Manually Firm Freight 
    ```

## 2DAYAIR - HI/AK/PR - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : NXTDAYAIR
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    State         : HI/AK/PR
    ```
- ``Any Amount``
    ```
        Non applicable --> Manually Firm Freight 
    ```

# KAPCO - Special
## UPS - USA - D  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : D -- FOB Destination  (Seller Pay Shipping)
    Country       : USA
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Free Freight
    ```

## UPS - HI/AK/PR - D  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : D -- FOB Destination  (Seller Pay Shipping)
    State         : HI/AK/PR
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Free Freight
    ```

## 2DAYAIR - All - FA  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : 2DAYAIR
    FREIGHT_TERMS : FA -- Free Air for Expedited Orders
    Country       : All 
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```

## NXTDAYAIR - All - FA  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : NXTDAYAIR
    FREIGHT_TERMS : FA -- Free Air for Expedited Orders
    Country       : All 
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Non applicable --> Manually Firm Freight 
    ```


## UPS - USA/CAN - FREE_W_TRK 
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : FREE_W_TRK (Free All Even Truck)
    Country       : USA/CAN
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Free Freight
    Truck Order:
        Free Freight
    ```


## Null - All - L  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : Empty  
    FREIGHT_TERMS : L (Locked)
    Country       : All 
    ```
- ``Any Amount``
    ```
    Non-Truck Order:
        Freight Locked(From Web)
    Truck Order:
        Freight Locked(From Web)
    ```

# GRYPH - Normal
- ``Note``: All GRYPH rules are mostly copied from KAPCO exept this rule below
## UPS - USA - O  
- ``Parameters``
    ```
    SHIP_TYPE_ID  : UPS
    FREIGHT_TERMS : O -- FOB Origin (Buyer Pay Shipping)
    Country       : USA
    ```
- ``Less than $7500``
    ```
    Non-Truck Order:
        $0  to $50        : Charge $6.00
        $51 to $100       : Charge $9.00
        Greater than $100 : Charge %15 
    Truck Order:
        Percentage     : 15%
        Mininum Charge : $62.5
    ```
- ``More than $7500``
    ```
        Free Freight
    ```
