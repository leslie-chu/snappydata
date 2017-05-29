# CREATE SAMPLE TABLE

## SYNTAX

**To Create Row/Column Table:**
```
    CREATE TABLE [IF NOT EXISTS] table_name {
        ( { column-definition | table-constraint }
        [ , { column-definition | table-constraint } ] * )
    }
    USING row | column | column_sample
    OPTIONS (
    COLOCATE_WITH 'string-constant',  // Default none
    PARTITION_BY 'PRIMARY KEY | string-constant', // If not specified it will be a replicated table.
    BUCKETS  'string-constant', // Default 113. Must be an integer.
    REDUNDANCY        'string-constant' , // Must be an integer
    EVICTION_BY ‘LRUMEMSIZE integer-constant | LRUCOUNT interger-constant | LRUHEAPPERCENT',
    PERSISTENT  ‘ASYNCHRONOUS | SYNCHRONOUS’,
    DISKSTORE 'string-constant', //empty string maps to default diskstore
    OVERFLOW 'true | false', // specifies the action to be executed upon eviction event
    EXPIRE ‘TIMETOLIVE in seconds',
    COLUMN_BATCH_SIZE 'string-constant', // Must be an integer
    COLUMN_MAX_DELTA_ROWS 'string-constant', // Must be an integer
    )
    [AS select_statement];
```    
** To Create Sample Table:**
```
    CREATE SAMPLE TABLE table_name ON base_table_name
    OPTIONS (
    COLOCATE_WITH 'string-constant',  // Default none
    PARTITION_BY 'PRIMARY KEY | string-constant', // If not specified it will be a replicated table.
    BUCKETS  'string-constant', // Default 113. Must be an integer.
    REDUNDANCY        'string-constant' , // Must be an integer
    EVICTION_BY ‘LRUMEMSIZE integer-constant | LRUCOUNT interger-constant | LRUHEAPPERCENT',
    PERSISTENT  ‘ASYNCHRONOUS | SYNCHRONOUS’,
    DISKSTORE 'string-constant', //empty string maps to default diskstore
    OVERFLOW 'true | false', // specifies the action to be executed upon eviction event
    EXPIRE ‘TIMETOLIVE in seconds',
    COLUMN_BATCH_SIZE 'string-constant', // Must be an integer
    COLUMN_MAX_DELTA_ROWS 'string-constant', // Must be an integer
    QCS 'string-constant', // column-name [, column-name ] *
    FRACTION 'string-constant',  //Must be a double
    STRATARESERVOIRSIZE 'string-constant',  // Default 50 Must be an integer.
    )
    AS select_statement
```

## Description
 * QCS: Query Column Set. These columns are used for startification in startified sampling. 

 * FRACTION: This represents the fraction of the full population (base table) that is managed in the sample. 

 * STRATARESERVOIRSIZE: Intial capacity of each strata.

 * baseTable : Table on which sampling is done.

Refer to [create sample tables](concepts/sde/stratified_sampling/#create-sample-tables) for more information on creating sample tables on datasets that can be sourced from any source supported in Spark/SnappyData.

## Example: Create a Sample Table 

### Step 1: Create a Base Table

```
 snappy>CREATE TABLE CUSTOMER_SAMPLE ( 
        C_CUSTKEY     INTEGER NOT NULL,
        C_NAME        VARCHAR(25) NOT NULL,
        C_ADDRESS     VARCHAR(40) NOT NULL,
        C_NATIONKEY   INTEGER NOT NULL,
        C_PHONE       VARCHAR(15) NOT NULL,
        C_ACCTBAL     DECIMAL(15,2)   NOT NULL,
        C_MKTSEGMENT  VARCHAR(10) NOT NULL,
        C_COMMENT     VARCHAR(117) NOT NULL)
    USING COLUMN_SAMPLE OPTIONS (qcs 'C_NATIONKEY',fraction '0.05', 
    strataReservoirSize '50', baseTable 'CUSTOMER_BASE')
```

### Step 2: Create a Sample Table On `CUSTOMER_BASE` Table

```
    CREATE SAMPLE TABLE CUSTOMER_SAMPLE on CUSTOMER_BASE     
    OPTIONS (qcs 'C_NATIONKEY',fraction '0.05', 
    strataReservoirSize '50') AS (SELECT * FROM CUSTOMER_BASE);    
```

## Example: Stratified sample Table  

<mark>Need Example with explanation </mark>
```
CREATE TABLE CUSTOMER_SAMPLE ( 
        C_CUSTKEY     INTEGER NOT NULL,
        C_NAME        VARCHAR(25) NOT NULL,
        C_ADDRESS     VARCHAR(40) NOT NULL,
        C_NATIONKEY   INTEGER NOT NULL,
        C_PHONE       VARCHAR(15) NOT NULL,
        C_ACCTBAL     DECIMAL(15,2)   NOT NULL,
        C_MKTSEGMENT  VARCHAR(10) NOT NULL,
        C_COMMENT     VARCHAR(117) NOT NULL)
    USING COLUMN_SAMPLE OPTIONS (qcs 'C_NATIONKEY',fraction '0.05', 
    strataReservoirSize '50', baseTable 'CUSTOMER_BASE')
```