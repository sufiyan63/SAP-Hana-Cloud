- Concepts
    -  Persistence Layer >>>
        - In-Memory
            - The HANA database operates in-memory, providing fast data processing and real-time analytics.
            - [DRAM(Dynamic Random Access Memory)](SAP%20HANA/Concepts/DRAM(Dynamic%20Random%20Access%20Memory).md) and [PMEM (Persistent Memory)](SAP%20HANA/Concepts/PMEM%20(Persistent%20Memory).md) for high-speed data processing.
        -  **Multi-Core Architecture** 
            -  Providers such as Intel and others are coming up with new hardware and chips that take advantage of multicore processors
            - SAP Hana is built to take advantage of this innovations 
    - Native Storage Extension (NSE) >>>
        - **Disk/**SSD (**Solid State Drive)** 
        - for less frequently used data.
        - This layer is designed to scale the data volume by extending the HANA in-memory storage with disk-based storage, enabling efficient handling of larger datasets without compromising performance.
        - **Relational Data Lake**
        - The data lake supports petabyte-scale data storage for structured, semi-structured, and unstructured data. It provides managed access and is integrated with the HANA database for analytics and processing.
        - Supports **elastic scaling**, enabling dynamic resource allocation based on demand.
        -  **Integrated Cloud Storage Options (Bottom Layer): **
        - The data lake is further extended with cloud-based storage options such as:
        - HDFS (Hadoop Distributed File System)
        - ADLS (Azure Data Lake Storage)
        - S3 (Amazon Simple Storage Service)
        - GCS (Google Cloud Storage)
        - Swift (OpenStack Object Storage)
    - DRAM(Dynamic Random Access Memory) >>>
        - **Role:** stores and processes data for ultra-fast access.
        - **Speed:** with low [Latency]() and high bandwidth, which allows real-time analytics and transactions.
        - **Volatility:** meaning data is lost when power is turned off. To prevent data loss, SAP HANA regularly saves (persists) data to disk.
        - **High Cost:** expensive compared to traditional storage (SSD or HDD).
        - **Limited Scalability:** Expanding beyond a certain limit is costly and inefficient.
        - .
            ```
            CREATE TABLE SALES_CURRENT 
  (ID INTEGER, 
   PRODUCT VARCHAR(50), 
   QUANTITY INTEGER)
  {{USING MEMORY_TYPE 'ROW'}} 
            ```
    - PMEM (Persistent Memory) >>>
        - **Non-volatile memory (NVM)** that bridges the gap between DRAM and SSD.
        - **Speed:** It is slower than DRAM but **much faster than SSDs**, providing a balance between cost and performance.
        - **Persistence:** Unlike DRAM, PMEM **retains data even after a power loss**. This helps in fast database restarts after shutdowns or crashes.
        - .
            ```
            CREATE TABLE SALES_HISTORY 
  (ID INTEGER, 
   PRODUCT VARCHAR(50), 
   QUANTITY INTEGER)
  {{USING MEMORY_TYPE 'PERSISTENT'}} -- Uses for cost-effective storage
            ```
    -  Slice-and-Dice >>>
        - Is a term used in data analysis to describe how you can look at your data from different angles or perspectives.
        - **Slice:**
            - This means **selecting a subset** of your data.
            - Involve filtering data to only look at revenue from a specific country.
            - **Example:** Imagine you have a large cake and you cut out just one slice to taste a specific part.
        - **Dice:**
            - This means **cutting that subset into smaller pieces** to see more details.
            - **Example:** Now, with that slice of cake, you cut it further to see the layers or different flavours in each smaller piece.
            - Involve breaking down that country's revenue by city, product, or time period.
    -  HANA Engine >>>
        - Supports multiple [Data models](SAP%20HANA/Concepts/Data%20models.md) and processing engines:
        - A Data Model is a blueprint that tells a database how to organize, store, and manage data
        - Combine both OLAP and OLTP in a single platform
        - **SQL**, **Document Store**, **Graph**, **Spatial**, and **Text** processing capabilities.
        - [Multi-Core Architecture ](SAP%20HANA/Concepts/Persistence%20Layer/Multi-Core%20Architecture.md)
        - Hybrid Extension to on-premise SAP HANA systems
        - Online Analytical Processing Engine
            - Is specifically designed to handle complex analytical queries efficiently. When a cube with star join calculation view is executed, the OLAP Engine processes the view to perform operations such as aggregations, calculations, and multidimensional analyses. This ensures high performance and optimized query execution for analytical workloads.  
    - Data models >>>
        - Document Store Model
        - Is designed to handle data in a flexible, JSON-like format. Can store information without needing a fixed structure, which is great for data that doesn’t always follow the same pattern.
        - Spatial Model
        - Is designed to handle geographic and location-based data
        - **Text Processing Model** 
        - Analysing and searching through large amounts of text
        - **OLAP Model** 
        - Think of it as a Model for analysing large amounts of data to find trends or patterns. For example, a company might use to analyse sales data over many years to decide which products are most popular.
        - **OLTP Model** 
        - This is designed to handle everyday transactions quickly and efficiently, such as processing a customer’s order online. It ensures these transactions are smooth and accurate.
        - Graph Model
            - used to represent **relationships** between data entities (like people, locations, or products) for network analysis
            - Is designed to handle data as nodes (vertices) and edges (connections between things). This is useful when relationships are as important as the data itself.
            - Social networks  
            - Logistics and route optimization  
            - Organizational structures  
            - .
                ```
                CREATE GRAPH WORKSPACE EmployeeGraph
  VERTEX TABLE Employee
  EDGE TABLE ReportsTo;
  
  //Find path from Intern to CEO

SELECT *
FROM GRAPH_SHORTEST_PATH(
  GRAPH Workspace_EmployeeGraph,
  START_VERTEX = '5',   -- Intern
  END_VERTEX = '1'      -- CEO
);
                ```
    - Delta Buffers and Merge >>>
        - Allows SAP HANA [Column Tables](SAP%20HANA/Views/Column%20Tables.md) to handle fast data inserts and updates without slowing down the operations.
        - In a sales application, every new sale record is first written to the delta buffer. This ensures that your system remains responsive even when many transactions happen simultaneously.
        - Delta Merge
            - Periodically, SAP HANA merges the data from the delta buffer into the main [Column Tables](SAP%20HANA/Views/Column%20Tables.md) . This makes sure that your data stays organized and optimized for queries.
    - SAP HANA Cloud Host/Port >>>
        - Go to SAP Hana Cloud Central
        - Select the chevron of SAP Hana DB Instance
        - You can find in header
        - Copy's the Host and Port
    - Scheduler Job >>>
        - SAP HANA enables stored procedures to be scheduled.
        - CRON
            - It has **6 parts**, and each part means something related to **time**:  
            - 1 - Minute (0–59)  
            - 2 - Hour (0–23)  
            - 3 - Day of month (1–31)  
            - 4 - Month  (1–12)  
            - 5 - Day of Week  (0–6, Sun=0)  
            - 6  - Year   - (e.g., 2023)  
            - .
                ```
                //run scheduler every min at 30 sec
Create scheduler job fillsnap2023 CRON '0 2 * * * 2023'
ENABLE PROCEDURE "demosnapshot/Query_USData/SNAP/SNAPSHOT/INSERT" PARAMETERS IP_YEAR='2023';

//see all the scheduler jobs
Select * from {{M_SCHEDULER_JOBS}} where scheduler_job_name = 'fillsnap2023';

//drop the scheduler job
Drop scheduler job fillsnap2023

                ```
- MONITOR
    - Database diagnostic files >>>
        - We store all the logs and traces in this folder
        - In database explorer if you expand the database container, you can see a folder
    - Audit >>>
        - In SAP Hana Cloud allows monitoring and recording specific actions in the database
        - It doesn't directly enhance database security but helps identify security vulnerability and potential breaches, Health, detect unauthorized privilege grants attempts to bridge security and protect against data misuse
        - Audited actions include changes to user authorization, creation or deletion of database objects, system configuration changes and access to sensitive data
        - The audit trail stores audit entries when an audit policy is triggered by a specific action
        - Audit entries are written to an internal SAP Hana database table ensuring quick querying and analysis of audit information
        - Access to audit entries is limited to the public system view AUDIT_LOG and only users with the system privilege AUDIT_OPERATOR and AUDIT_ADMIN can perform select operation on this view
        - SAP Hana Cloud Central ⇒ Auditing tile ⇒ Create Audit policy
        - Select Audit Actions like CREATE TABLE
        - Select Users
        - Audit Level like info, error and warning
        - Retention period
        - Enable Policy
    - Show Data linage >>>
        - Visualization of **all data sources** (like tables and views) that are used to build a calculation view.  
        - Right click hdbcalculation view in BAS and select the ? option
        - It shows the complete flow of data into and within the calculation view, including the processing steps.  
        - Shows how changes to the source data could impact the calculation view.  
    - Manage Users >>>
        - SAP Hana Cloud Central ⇒ right click and select SAP Hana Cockpit ⇒ Add all the tiles from ⇒ click on user icon ⇒ Select Manage cards
        - Is different from the SAP BTP users
        - Only Users created in SAP Hana Cockpit or SQL console shows up here
    - Manage Roles >>>
        - SAP Hana Cloud Central ⇒ right click and select SAP Hana Cockpit ⇒ Add all the tiles from ⇒ click on user icon ⇒ Select Manage cards
        - Is different from the SAP BTP roles
        - Only Roles created in SAP Hana Cockpit or SQL console shows up here
    - Column Lineage >>>
        - when you want to see quickly where a calculated or restricted column originates from
        - It also helps you to avoid mixing up columns with the same name.
        - ![](https://remnote-user-data.s3.amazonaws.com/QuCs3Jbwau64BEYOJJMtX0182CWNMV5Ci6fdSZmp2YPL5lFWtHj0cUdVhAD6Z8UfgA6zoiMxIhSHSh0Z-lRiJxO2Z5ATOem8zECPHC5LmybpApxN76VlNQ9dlgG9CkGs.png)
        - **Where-Used** 
            - Input Parameters
            - Calculated Columns
            - Restricted Columns
    - Debug Query mode >>>
        - Button in Graphical calculation view editor
        - In each node you will get Debug Query tab to examine the runtime SQL of a node
        - You can execute at each node to display the interim results
        - One of the most useful things that you can discover when using Debug Query mode is to see how each node is pruned of columns and even complete data sources under various query conditions  
        - Remember that the Debug Query mode calls the active version of the calculation view. This means that if you have made changes to the calculation view but not yet rebuilt it, then you switch on Debug Query mode, the debug results will not reflect those latest changes.
        - Previewing the Output of Intermediate Nodes
            - To troubleshoot problems in view design, preview the data of an intermediate node rather than the whole calculation view.
        - Replacing a Data Source
            - For the top node, it's also possible to switch to a Star Join node.
            - It's possible to convert a Projection node into an Aggregation node and the other way around without losing the reference to the data sources of the node.
    - SAP HANA Performance Tools >>>
        - To enable this view, when creating your SAP Business Application Studio development space, or later on, you must add the extension  to this space.
        - This is a set of views, tables, graphs that you can use to analyze any SQL Query. You can drill-down in a graph execution, analyze the timeline of query compilation and execution, visualize how many tables, and which tables, were used, and so on.
        - In addition to analysing the SQL generated by the calculation views (either the standard query or a modified query), it can be used wherever SQL is defined, for example in table functions.
        - ![](https://remnote-user-data.s3.amazonaws.com/Lr_LZkXjS1ArRbjMKTrZ6Kt8JgxQ1KG_hq69OEZxlaCIe70Gq63eU4JGblmJz6xIoVZbob4lebGSUyV0TVudE_uzcKQzO4IqYGqg2YzJaFyO0HNVDhjoapoyvrypjEEN.png)
        - You must first locate the plan file in the folder where you uploaded it.
        - Add the plan file to ?.
        - SYSTEM privileges:
            - TRACE_ADMIN
            - INIFILE_ADMIN
    - SQL Analyzer Plan File >>>
        - If you have generated the SQL plan file from an SQL Console opened from the  *SAP HANA Database Explorer*  view of SAP Business Application Studio, the plan file opens immediately in the  *HANA SQL Analyzer*  view.  
        - ![](https://remnote-user-data.s3.amazonaws.com/rteirW6Lpw9uUcMJDk_z83YTZcvJXPSLbBcCyaYlxvg8UkIWWQ05yxD2QyFx-TcLtRmbofKluJD1xsQzZhErsrGUDaUO0WfK_me5wolFXN_FmQeP1DpQcJgIuhgFNP-z.png)
        - To get started, you generate a file that collects the run-time information of your SQL query. Use the menu option  *Analyze ⇒ ?* . Choose a file name prefix for the plan file, and choose  *Save* . The location is already set for you and you cannot change it at this stage.  
        - If you have generated the SQL plan file from another tool, such as the Data Preview of SAP Business Application Studio, or from the external SAP HANA Database Explorer (outside of SAP Business Application Studio), you need to download the SQL plan file to your computer. Choose Download, and this is then handled by your web browser. Choose a convenient location to store the SQL plan file.
        - You access these plan files from the External SAP HANA DB Explorer, within the HDI container, in the section  *Catalog ⇒ Database Diagnostic Files*  ⇒  *<DB Instance ID>*  ⇒  *other* . They must be downloaded, and then uploaded to SAP Business Application Studio.  
        - ![](https://remnote-user-data.s3.amazonaws.com/pDzWh35ixccuJ9UCvwtE25A-yuwy4alZhixA_z0HGaXdYiWHgLBUnfBArS1uIq5kFJ0dAvNyN7d0_7-sxq8vHmExhAgTKJVxrC-qTd6EIb0x8hDKkauAu0ygsPXdB495.png)
        - The next stage is to upload the plan file to the  *Explorer*  view of SAP Business Application Studio. Upload the plan file to any project folder by using the context menu  *Upload* .  
    - 
- SQLSCRIPT
    - [Update](https://help.sap.com/docs/SAP_HANA_PLATFORM/4fe29514fd584807ac9f2a04f6754767/20ff268675191014964add3d17700909.html)
        - FROM in Update >>>
            - `FROM` clause is used in an `UPDATE` statement when updating a table by joining it with another table. This allows you to specify which rows to update based on a condition involving data from multiple tables.
            - .
                ```
                ```
-- Creating two tables
CREATE ROW TABLE T (KEY INT PRIMARY KEY, VAL INT);
INSERT INTO T VALUES (1, 1), (2, 2);

CREATE ROW TABLE T2 (KEY INT PRIMARY KEY, VAR INT);
INSERT INTO T2 VALUES (1, 2), (3, 6);

-- Using the FROM clause to update table T based on matching keys in table T2
UPDATE T SET VAL = T2.VAR 
FROM T, T2 
WHERE T.KEY = T2.KEY;

-- Result: 
-- T will now have updated values where keys matched
-- SELECT * FROM T;
-- KEY  VAL
-- 1    2
-- 2    2
```
                ```
            - In this example, only the row in `T` where `KEY = 1` was updated to match the `VAR` value from `T2`. The row with `KEY = 2` remained unchanged because it did not have a matching `KEY` in `T2` 
    - SYNTAX >>>
        - Using Double quotes is case sensitive
    - SQL Expressions in calculation views >>>
        - SQL is used to implement custom logic in calculation views where standard features of the graphical calculation view are not sufficient. SQL provides a very large number of functions that can provide users with access to complex data processing tasks. 
        - you should NOT create an SQL-based object (function or procedure) to achieve a result that a graphical Calculation View can achieve, even if you have advanced SQL knowledge. Calculation views provide more optimization possibilities than SQL as they generate their own SQL code at runtime based on the variety of query conditions and implement advanced pruning to generate the leanest code to provide the best performance.  
        - Calculated columns
        - Filters
        - Restricted columns
        - SQL expressions in calculation views uses plain SQL and not the syntax of the SAP enhanced version, SQLScript.  
        - From QRC 1/2023 onwards, it is possible to include User-Defined Functions in the expression of Calculated Columns and Filters. This feature can help in scenarios where you want to reuse logic in different calculation views or pre-process values of input parameters before using them in expressions.  
        - ![](https://remnote-user-data.s3.amazonaws.com/AopRQKKjfrKANkY2kTVD5kWLnTwAMTZA1CaYZ65ow1THhIW78SNA6wIaM90ceSMo1udMWjFxFnTHdceCYMgvNQCF-g1Uj0Vf_KQ3uJTpRklMbuQoZ6nB4XWF0ZZvc_y6.png)
        - You can access both User-Defined Functions created inside your local container, and the ones from an external HDI container that are made available using a synonym.  
    - Query Hierarchies using SQL >>>
        - Reading hierarchies using SQL is supported for both  *level*  and also  *parent-child*  types.  
        - you want to calculate the total number of absence days for the Service line of business. The service line of business sits within the organization hierarchy and has many levels below it that represent all departments. If you roll up all the lower levels we can calculate the total for the service line of business.
        - We do not need to read all the lower levels separately to reach the total. The lower levels are automatically rolled up.  
        - **But before you can write the SQL query, you need to work on the following setup:** 
        - The hierarchy you wish to query must be created in a dimension calculation view. The dimension calculation view is then consumed in the star join node of a cube with star join calculation view. This is because only shared hierarchies can be read by SQL.
        - If you want to enable SQL access to all shared hierarchies of the current version of the calculation view, then:  
        - ![](https://remnote-user-data.s3.amazonaws.com/UrTi4lMQidjOy0YX1iar0yFuH27nyftDA1cnw0tdotZLm6aQ5sKuo6Xt5a1T9M_UqRBajlad_OAlju-KxZFDU-sa7BEf00ytxJLe32R31sjqvWllVLEfV0zJdU4dg9eJ.png)
        - You must then enable the shared hierarchy to be exposed to SQL by setting a parameter in the Properties tab of the semantics node of the cube with star join calculation view.
        - If you want to enable SQL access to only a selected list of shared hierarchies:  
        - ![](https://remnote-user-data.s3.amazonaws.com/p9JtU4HZV-KKDipDDgZiFIVTayMUxQ4pO7kgx39CGGQqLJXRXMppBQ1B2vOSr0y94Jyddz3S96jvWR_OsOzlRzBs0Z6QuIKHUCQIG5_Ki7N1htqcPtN3OQcqMV2kAIp6.png)
        - In the  *SQL Access*  section of the  *Hierarchy*  tab of the semantic node, you should locate the name that is given to the node column, because you will need to refer to this in the SQL statement.
        - After you enable SQL access to shared hierarchies, the tool generates a Node column and a Hierarchy Expression Parameter for the shared hierarchy with certain default names. You can use the node column to filter and perform SQL GROUP BY operation and use the hierarchy expression parameter to filter the hierarchy nodes (for example, if you want query only the children nodes of a parent-child hierarchy).
        - Hierarchy Expression Parameter has been deprecated. We recommend you to not use this parameter in your calculation view.
        - .
            ```
            SELECT "H_GEONode", sum("SALARY_AMOUNT") from "_SYS_BIC"."CVC_EMPLOYEES" group by "H_GEONode"  
            ```
    - SQLScript >>>
        - SQLScript is a database language developed by SAP. It is based on standard SQL but adds additional capabilities to exploit the advanced features of SAP HANA Cloud.
        - SQLScript enables you to access SAP HANA-specific features like [Column Tables](SAP%20HANA/Views/Column%20Tables.md), parameterized calculation views, [Delta Buffers and Merge](SAP%20HANA/Concepts/Delta%20Buffers%20and%20Merge.md), working with multiple result sets in parallel, built-in [Standard SAP Currency Conversion Tables](SAP%20HANA/Project%20Structure/Assign%20Semantics/Currency%20Conversion/Standard%20SAP%20Currency%20Conversion%20Tables.md) at database level, fuzzy text searching, spatial data types, and predictive analysis libraries.
        - SQLScript also adds extra [data types](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/working-with-sqlscript_cd4b02d8-fad9-4a26-99d7-8d9ec61691a5) to help address missing features from standard SQL - for example, spatial data types and text data types.
        - .
            ```
            books_per_publisher = SELECT publisher, COUNT (*) AS num_books FROM BOOKS GROUP BY publisher;

publisher_with_most_books = SELECT * FROM :books_per_publisher WHERE num_books >= (SELECT MAX(num_books) FROM :books_per_publisher);
            ```
        - The SQLScript compiler and optimizer determine how to best execute these statements, whether by using a common sub-query expression with database hints or by combining this into a single complex query.  
        - [Conditional Logic](SAP%20HANA/SQLSCRIPT/Conditional%20Logic.md)
        - [Variable](SAP%20HANA/Data%20Model%20Concepts/Variable.md)
        - [Input Parameter](SAP%20HANA/Data%20Model%20Concepts/Input%20Parameter.md)
        - [Table functions](SAP%20HANA/Views/Table%20functions.md)
        - [Table Function Node](SAP%20HANA/Nodes/Table%20Function%20Node.md)
        - [Scalar Function](SAP%20HANA/Views/Scalar%20Function.md) 
    - Conditional Logic >>>
        - The FOR loop iterates a range of numeric values – denoted by start and end in the syntax – and binds the current value to a variable (loop-var) in ascending order. Iteration starts with value start and is incremented by one until the loop-var is larger than end.
        - Therefore, if start is larger than end, the body loop will not be evaluated. For each enumerated value of the loop variable the statements in the body of the loop are evaluated. The optional keyword REVERSE specifies to iterate the range in descending order.
        - .
            ```
            IF <bool-expr1> 
THEN 
  {then-stmts1} 
{ELSEIF <bool-expr2> 
THEN 
  {then-stmts2}} 
{ELSE 
  {else-stmts3}} 
END IF 

//while loop

WHILE <bool-stmt> DO
  {stmts}
END WHILE 

//for loop

FOR <loop-var> IN {REVERSE} <start>..<end> DO
  {stmts}
END FOR 
            ```
- Data Model Concepts
    - Fundamentals >>>
        - Measure
            - A numeric value, such as a price or quantity, on which you can process arithmetic or statistics operations, such as sum, average, top N values, and calculations. eg Quantity, Sales Revenue
        - Attribute
            - A descriptive element such as 'country' that is used the provide context for a measure. For example 'Sales Revenue by  *France'* . 
        - Data Source Aliases
            - In some cases, you need to use the same data source more than once in a calculation view. Although it is possible to include the same data source multiple times in the same calculation view, each instance needs a unique name to identify it.
            - ![](https://remnote-user-data.s3.amazonaws.com/FeaZWEWLDseODBUMnJmomMS3y28Mop6zgaF-LUWALqDQ08zRE4aiZMZRvdoYqTsKI5hK2hEaGAeXeiCtbisX1xh-LAnHsSjKMDkSchu62XK7FX5HttCsFFamxSXIQnsX.png)
    - Variable >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/SYcKTUHlGOiW_I8q1T02TCpBfq9_9PFR-JgXCAZu9Lsuv3DTi3FcA3cbb2YEelM8FwPbSSyxKFaD-lV-ilD4yjmCDFQdWHdzzkYE2QHIVZ7cJsTZpzyfKNgPRa0vMrof.png)
        - Variables apply filter after execution of all nodes till the semantics (at the top level) whereas Input parameters can apply filters at any projection level.
        - Variables are bound to attributes/specific fields whereas an input parameter is independent of any field in the view.
        - Prompted to user when to input when used in reporting tool such as SAC
        - You define in the Semantics node of a Calculation View, in the Parameters tab.
        - **Selection Type:** Whether selections should be based on intervals, ranges or single values.
        - **Multiple Entries: **Whether multiple occurrences of the selection type are allowed. - Each enter is appended with AND, OR logical expression
        - **Mandatory:** You can also define whether specifying the variable at runtime is Mandatory and/or if it should have a Default Value.
        - **View/Table Value Help:** The  setting is used to define which table/view SAP HANA will fetch the data to show in the prompt. The Reference Column defines which (unique) column in this table/view will provide the possible values for the variable.
        - [Value Help Based on Hierarchies](SAP%20HANA/Data%20Model%20Concepts/Value%20Help%20Based%20on%20Hierarchies.md)
        - Apply Filter ⇒ Attribute ⇒ Column name where to apply Filter
        - In the semantics of a view, you can see, and also define, which variable is assigned to which attribute.
        - ![](https://remnote-user-data.s3.amazonaws.com/k3y0PQDZ_I2bODRfiWsrw19-vQCV9XwPCT4LkbUymfHQigJ8mdH1faEA_15ECwIoTc4BVNGtxUWcv8DpUZtCdrKd0etilu4oQ2lrwlE1V7CI3mm_aE6D5Bij4U1F6SGt.png)
        - Cannot be applied to measures
        - For non-mandatory variables, if nothing is specified at runtime, all the data for the corresponding attributes is returned by the view without filtering if no default value is given.
        - .
            ```
            DECLARE <variable_name> <type> [NOT NULL] [= <value>]

<variable_name> = <value> or <expression>

//Use a variable in an expression
:<variable_name> 
            ```
    - Value Help Based on Hierarchies >>>
        - When creating a variable, In the Reference Column you specify a column that is associated with one or several hierarchies, you can specify one of the hierarchies in the variable definition. With this option, the user can navigate the hierarchy, rather than a flat list, to select the values in the value help. This makes navigation much easier when there are a lot of values; imagine being able to first select your country, then your department, before the list of employees appears? You can use either parent-child or level hierarchies.
        - ![](https://remnote-user-data.s3.amazonaws.com/oLpdFp2P8ancP8NRpdg4ypWcAvEUV8z30NSxIU3-FobNsXnrXWJfCnAP09EsFh-T5TYKj4m_AfKwSBuaUlk0fQhL9MbnqJi5J9Q3lz5H8XLncTTxhv-9Pe7vxclycR_8.png)
        - If you refer to a parent-child hierarchy, the variable reference column must be defined as the parent attribute and not the child. Why? Because the system uses the parent column to build the entire tree from the top down. If you start from the child, HANA will not know how to display the upper levels in a properly nested way. ‒ This is the SAP Official ways of doing
        - If you refer to a level hierarchy, the variable reference column must be defined at the leaf level; that is, the bottom level. - ‒ This is the SAP Official ways of doing
    - Input Parameter >>>
        - Can be used in different places to provide dynamic values, in particular, calculated columns, restricted columns and filter expressions
        - Can apply filters at any Node.
        - If the user selects GROSS, the calculated column (of type Measure) will display the GROSS_AMOUNT measure in the AMOUNT column. Any other selection will result in NET_AMOUNT being displayed.
        - .
            ```
            if('$$GROSS_OR_NET$$'='Gross',"GROSS_AMOUNT","NET_AMOUNT")

SELECT "PRODUCT", sum("TAXED_AMOUNT")
      FROM "_SYS_BIC"."MyPackage/SalesResults" 
       ('PLACEHOLDER' = ('$$Tax Amount$$', '17.5')) 
GROUP BY "PRODUCT"  
            ```
        - Can also be used when you have scalar or table functions in your Calculation View. The parameters of these functions can be fed with values that you enter at runtime using the [Parameter Mapping](SAP%20HANA/Data%20Model%20Concepts/Parameter%20Mapping.md), when querying your calculation view
        - Value taken from a user and is sent to placeholder $$$$   $$
        - **Parameter type** 
            - Direct ⇒ you want the user to enter a parameter without choosing it from a predefined list.  
            - Currency ⇒ For currency conversion, when you want the end user to specify a source or target currency.
            - Date ⇒ To retrieve data based on a date entered by the end user (or chosen in a calendar type input box).
            - Unit of Measure ⇒ To retrieve data based on a unit of measure chosen by the end user
            - Column ⇒ Value help from other views in the same view
            - Static List ⇒ Fixed values
            - Derived from table ⇒ from table outside of this view
            - Derived from procedure ⇒ When you want the parameter value to be passed to the calculation view based on the scalar output of a stored procedure  
    - Restricted Columns >>>
        - A restricted column is a generated column that is made from a measure filtered by one or more attributes values  
        - The value in a restricted column is 0 for all rows that do not match the defined filter condition(s).  
        - The benefit of a restricted column is that it provides the modeler with a ready-made object that is useful when you are creating calculated columns as the base objects are already filtered. This simplifies the creation of calculated columns 
        - .
            ```
            CASE 
    WHEN Sales > 100 THEN Sales 
    ELSE NULL 
END

            ```
        - The filtering happens on every row. Only those rows that satisfy the condition (e.g.,  *Sales > 100* ) provide a valid number. All other rows effectively have “no value” (0 or NULL) in that column. This way, when you later create calculated columns based on this restricted column, you’re already working with filtered data.  
        - From SAP HANA Cloud QRC 3/2023 onwards, it is possible to define the restriction based not only on attribute columns, but also on measure columns.  
        - You have the option to hide the restricted column, for example to reuse it in a calculated column and thereby make it unavailable for reporting.
        - If there are different lines in the restriction, all the lines defined on the same column are combined with the logical operator  *OR* , and then, all the sets of restrictions for different columns are combined with the logical operator,  *AND* .  
        - ![](https://remnote-user-data.s3.amazonaws.com/MFcD4eS-U95KFRyQv4SnsrpLWxQ8_HE4VtYZtSrSv-vWfkqMavnROtwZKizP8jRLLUgUFpCXjqeu-mHktaKLBDXdde5GFPt3Ubw2vhEYNItntSArVVdboIaK9YhpThBM.png)\
        - Suppose if a table has data from different region let's say US, EU
        - With the help of restricted column US employees can only see U.S. data and for other countries it is null
        - Select any node in **Cube **⇒ Go to Restricted Column ⇒ You can select Base Measure ⇒ TOTAL_SALES
        - Restrictions ⇒ pick Columns or Expression
        - Check Include
        - is used to specify whether the records that meet the defined restrictions should be included or excluded in the aggregation. For example, if you restrict the column to include only records where the year is 2021, only those records will be aggregated.
        - SELECT SUM(Revenue) FROM Sales WHERE Year = 2021  
        - Unchecked Include
        - The restricted column will exclude the records where the Year is 2021 from the aggregation. This means the total revenue will be calculated for all years except 2021.
        - SELECT SUM(Revenue) FROM Sales WHERE Year != 2021  
        - For values that don't fit the filter condition of restricted column is shown as null
        - ![](https://remnote-user-data.s3.amazonaws.com/C0cl10jOHwo0j2IXQ3EDAMINneLasZgDSeRs3IxPfOk55sNO-qz65ZxJHAp_bst7oXZ_Dbnf77m3KsSWE2HpnfiKW6-RBy47bOUdtHPMKaLo1BGtZDM8Gnn7oljZiOdY.png)
    - Calculated Columns >>>
        - It is possible to create additional calculated columns in any type of calculation view node.
        - Calculated columns are generated on the fly at runtime and do not persist the result.
        - It is possible to nest calculated columns, so that one calculated column is based on other calculated columns.
        - A calculated column can use the string functions, mathematical functions etc. available in the editor.
        - ![](https://remnote-user-data.s3.amazonaws.com/UWHVKjFlJs40WLXlJFPDCKlYb87kdwNXsSfuMuQtZ1gxSD8eRRJskfg5Of-kLqmjyXfbRui1X-_3FFdXKmJP51r7SmGq7ODxdOchWenFEIM8CzTqkKcdG09_JqJhpWAr.png)
        - You can define a calculated column as measure or attribute.
        - For certain measures, it is not possible to perform the calculations when the measures are already aggregated
        - **Enable Client Side Aggregation** 
            - Is a technique used to perform data aggregation on the client side rather than on the server side. This can be useful in scenarios where you want to offload some of the processing load from the server to the client,  
            - Consider an example where you have created the calculated measure MAXIMUM_GROSS_AMOUNT, giving you the maximum value of the gross amount. By selecting the Enable client side aggregation checkbox, you propose to the reporting client to also apply a maximum aggregation on the client side as defined in the Aggregation Type dropdown list.
            - ![](https://remnote-user-data.s3.amazonaws.com/C6ECY5Y3TNW5MnnQ09v6lbpQ8KqipHYJfGBlb2mUV4UuMv_aj0f_CD8_nrnrQjt813hlsyq9DAAdO_HpSvCrG6pHGIGh-Kyeg39WoMZzbiGFkyzOIVCBhVtOgzYhEJvq.png)
        - [Consider Granularity When Creating Calculated Columns](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/generating-calculated-columns_ade323bc-6ab2-4667-aded-3db6c32f6586)
            - For certain measures, it is not possible to perform the calculations when the measures are already aggregated. The aggregated granularity of, for example, Price does not mean anything.
            - ![](https://remnote-user-data.s3.amazonaws.com/ge3JDfYdSO48pSfhkJ16Q2PcYvkLPlIdC0QjE2XE01B78KJ0fmVSGNPRiOEOr7hvvujOp-0aKTLZzE52soh2bxV3_1cPUM1dtlhQSD4u7p-K6vB4yTYPO46tfdIFeu-q.png)
            - In the figure, in the sum line highlighted in red, the units as well as the price have been aggregated. Multiplying these to aggregates does not give a meaningful result.
    - Parameter Mapping >>>
        - In some cases, your calculation view needs to pass information provided by the user, via a variable or input parameter, to another object. This could be another calculation view or even a procedure or function.
        - To enable parameter mapping, you must use the  *Input Parameter/Variables Mapping*  feature. You can find this feature in the  *Parameters*  tab in the calculation view.  
        - ![](https://remnote-user-data.s3.amazonaws.com/hV5Kb9cyt8IYwFORp5ceMpK2dXwbBkSX8h2hl1Ot90hXXgM_Oa4dMDjLiZjGG-BpBPejGxRa1zubtSQDgBXECQbNkyOsQ9PuDNvLAiRDoM77TqQhWtqAq9vR9tx2g6zF.png)
        - > You can only map Variables to Variables and Input Parameters to Input Parameters. Cross-Mapping (such as an Input Parameter to a Variable) is not possible.
        - From SAP HANA Cloud QRC 1/2024 onwards, it is possible to define an input parameter and at the same time propagate this parameter to other views within the same HDB module that consume this calculation view. Similarly, it is possible to delete an input parameter and propagate the deletion to the consuming calculation views.
        - From SAP HANA Cloud QRC 1/2022 onwards, it is possible to pass input parameters from a Calculation View to an SQL View or a Calculation View that is accessed remotely as a Virtual Table. This ensures that filters are pushed down and executed as close to the data sources as possible.  
        - This new feature requires Smart Data Access (SDA), relying on the odbc adapter – it is not possible with remote sources accessed through the Smart Data Integration (SDI) adapter. The scenario supports remote SAP HANA databases running on SAP HANA Cloud, or SAP HANA On-Premise version 2.0 minimum.  
        - Besides, in SAP HANA On-Premise, Input Parameters in SQL Views are supported only from version 2.0 SPS02 onwards.
        - Select Type >>>
            - **Data Sources** 
            - Map input parameters of the underlying data source like calculation views to input parameters of the current calculation view
            - **Procedures/Scalar Functions for input parameters** 
            - if you want to map the input parameters defined in the procedure or scalar function to the input parameters of the current calculation view
            - **Views for value help for variables/input parameters** 
            - If you want to map input parameters or variables defined in the external views which are used as value help to the input parameters or variables of the current calculation view.
            - **Views for value help for attributes** 
            - If you have defined a value help view or a table that provides values to filter the attribute at runtime to the input parameters or variables of the current calculation view.
    - Generate Properties File for Calculation Views >>>
        - file that contains the name and description of all calculation views objects such as columns, input parameters and variables.
        - To generate the properties file, select a calculation view and choose the menu option Modelling ⇒ Generate Properties File.
        - You first generate the properties file for the calculation view and then you must build the SAP HANA Database Module that contains this generated file so that the names and description values are then stored in the BIMC_DESCRIPTION table.
        - One of the key reason for doing this is to enable translation of the name and description values to multiple languages by updating the BIMC_DESCRIPTION table.
        - Client tools can read the BIMC_DESCRIPTION table and display values in the reporting tools respectively.
    - Filter >>>
        - In Calculation Views, filters can be applied to many node types. These include the following: Projection, Union, Join, Rank, Aggregation, Star Join
        - > When a column from a data source (for example COUNTRY), is mapped to the node output with a different name (for example, COUNTRY_CODE), the output column name must be used in the expression. In this case, the correct expression would be something like "COUNTRY_CODE"='US'. If you enter the expression "COUNTRY"='US', the expression might validate but building the calculation view will fail.
        - **Filters defined in the top node** 
        - In an Aggregation or Star Join node, the filter is applied to the data before the aggregation is executed.
        - **Filters defined in other nodes** 
        - In other nodes (NON top nodes), the filter expression is generally applied to the output of the node.
        -  Pushing Down Filters >>>
            - Filter Pushdown is a technique used in databases to make data retrieval faster and more efficient. Pushdown in this context means that a filter that is defined on a node or in a query becomes effective in lower nodes. This reduces the amount of data the database has to process, saving time and resources.
            - Filters are generally pushed down during query execution if this does not change the semantics of the query (The **result** of the query must stay **exactly the same** even after applying filter pushdown)
            - Filter pushdown does not occur per default for [Rank Node](SAP%20HANA/Nodes/Rank%20Node.md), [Window function nodes](SAP%20HANA/Nodes/Window%20function%20nodes.md), and nodes that feed into two other nodes  
            - However, you can force a pushdown even in these situations by using one of the following options:  
            - **Allow Filter Push Down** – for [Rank Node](SAP%20HANA/Nodes/Rank%20Node.md) and [Window function nodes](SAP%20HANA/Nodes/Window%20function%20nodes.md)
            - **Ignore Multiple Outputs for Filter** – when a node is consumed by two nodes
            - ![](https://remnote-user-data.s3.amazonaws.com/fToe_xE_A6jxHt5EuF57LiQ_AXbVC8slUslTV3XOIDoECLE8Zr5W4BUlcDl4UbuqJ5TVwZUZItssSRW3gBAw6ciywfL9yJSZVLoulASjNGZesqVn09_0XsTpFBCSIU4j.png)
            - In your calculation view, if you have defined a node that is a source to more than one other node (in other words the data flow splits), then you need to be aware that this can potentially block filter push-down. The SQL optimizer does not want to break the semantics of the query and so by default it keeps the filter at the node at which it is defined (and this node might be high up in the stack causing large data sets to travel up the stack).  
            - If push-down does not violate semantic correctness, flag **Ignore Multiple Outputs for Filter **can be set to enforce filter push-down.
            - In SAP HANA Cloud, you often use **calculation views** to combine and analyse data from multiple tables. Suppose you have a calculation view that unions two tables—say, sales data from two different regions—and you only want rows where the status is "active."
            - [Example of Enforced Pushdown](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-performance-guide-for-developers/example-ignore-multiple-outputs) 
            - > A similar setting can be applied at the view level in the semantic node View Properties > Advanced. It has the same effect but applies to scenarios where one calculation view is consumed multiple times by another calculation view.
        - Verify that the filters are pushed down to the base tables
        - Avoid including calculated columns in filter expressions
    - Sequence >>>
        - It is a database object that automatically generates the incremental list of numbers according to the rules as specified in the sequence specification
        - In SAP HANA, a sequence is an object that maintains its own internal counter, When you call the function **NEXTVAL** on a sequence, it generates the next number based solely on its internal state, not by looking at the table’s current values.  
        - .
            ```
            CREATE TABLE my_table (
    id INT DEFAULT seq_id.NEXTVAL,
    name VARCHAR(50)
);

INSERT INTO my_table(name) VALUES('John');
            ```
        - Every time you insert a new row without explicitly providing a value for the `id` column, the **NEXTVAL** function is called. This increments the sequence’s internal counter and assigns the new number.  
        - .
            ```
            create sequence schema.nameofseq START WITH 1 INCREMENT BY 1 NO MAXVALUE;

INSERT INTO MY_TABLE (ID, DATA) VALUES (schema.nameofseq.NEXTVAL, 'Some data');

SELECT mySequence.CURRVAL FROM DUMMY; 
            ```
        - **Sequence Parameter** 
        - .
            ```
            START WITH <start_value>
 | INCREMENT BY <increment_value>
 | MAXVALUE <max_value>
 | NO MAXVALUE
 | MINVALUE <min_value>
 | NO MINVALUE
 | CYCLE
 | NO CYCLE
 | CACHE <cache_size>
 | NO CACHE  
            ```
        - **CACHE **- Refers to a mechanism that pre-allocates a set of sequence values and stores them in memory for faster access  
        - **NO CYCLE ** - it means that the sequence will not restart from its initial or minimum value once it reaches its maximum value, Instead, it will stop generating new values and return an error if an attempt is made to generate a value beyond its defined range.
- Joins
    - Referential Join >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/Q2nFLOtl1VuGglPnaxd2qFngSxpdb9lQglBhocfhby8B9RJKPDZ6AncVoUSkZvuCQe7mGC1ZZIQ2xng6R1SLv3CjzgE9bWsjHbtxri63DQ-IPZ6GT2NWAMVzmTaqkaE-.png)
        - In Join node when you connect one table to other. Click on the link between the tables ⇒ general properties ⇒ Select Referential Join
        - A **Referential Join** in SAP HANA is a type of join used in calculation views when there is a logical relationship between two tables, typically where one table contains a foreign key referencing a primary key in another table. This type of join is optimized for performance because it assumes that the joined data is always consistent, meaning every foreign key in the referencing table corresponds to a valid primary key in the referenced table.
        - **Default Behavior**: If no fields from the right table (referenced table) are requested in the query, the join can be completely skipped, further improving performance.
        - **Output Handling**: If fields from the right table are required, the join behaves like an inner join.
        - Cardinality must be specified. If it is not, the Referential Join cannot be optimized.
        - A referential join can be implemented in a standard join node as well as a star join node.
        - Consider two tables:
        - **Orders** (OrderID, CustomerID, OrderDate)
        - **Customers** (CustomerID, CustomerName, Country)
        - The **CustomerID** in the Orders table is a foreign key referencing the primary key **CustomerID** in the Customers table.
        - .
            ```
            ```
SELECT O.OrderID, C.CustomerName, C.Country
FROM Orders O
REFERENTIAL JOIN Customers C
ON O.CustomerID = C.CustomerID;
```
            ```
        - If the query requests only fields from the `Orders` table:
        - .
            ```
            ```
SELECT O.OrderID
FROM Orders O
REFERENTIAL JOIN Customers C
ON O.CustomerID = C.CustomerID;
``` 
            ```
        - The referential join is skipped entirely, and only data from `Orders` is returned because fields from `Customers` are not needed.
        - Conditions for Referential Join Optimization >>>
            - A join defined as a Referential Join between two tables or sources, A and B, is pruned (not executed) when all three following conditions are met:
            - No field is requested from B
            - Integrity is placed on A
            - The cardinality on the B side is ..1
            - When the cardinality on the B side is not ..1, the join will always be executed, even if no column from B is requested.
        - Integrity Constraint >>>
            - This setting defines in which direction the referential integrity is guaranteed.
            - Left: Every entry in left table has at least one match in right table. N..1
            - Right: Every entry in right table has at least one match in left table. 1..N
            - Both: Every entry in both tables has at least one match in the other table. 1..1
    - Inner Join >>>
        - It returns rows when there is at least one match on both sides of the join.  
        - ![](https://remnote-user-data.s3.amazonaws.com/doUITP9onjun_0p_Injh5BrCVTNKO7gOyKLQyM4UGB3OqNs_3sDXyVr2tSKIYGxYPDz-d5KrMC865IMBVrVhRhz3YQ9fx9kmh-EM0HJwWNt4HUkC1HijpxbSeR7TaFwY.png) 
    - Left Outer Join >>>
        - A Join returns all rows from the left table, even if there are no matches in the right table.
        - ![](https://remnote-user-data.s3.amazonaws.com/F_XXkBQhESD6rm3oAw1nuREt3adqnRX1ylqVayfo3A9tjyYDdeKQSQD7qwr2_31kc50YZUE6En_13dwVy37pPlaA1yDsKE6PqEDwD9De73dsj9P6yggPi-660B3O822Y.png)
        - Try to use N..1 and 1..1 joint cardinality ⇒ Optimal Performance
        - Try to reduce number of joint fields
        - Avoid joining on calculated columns
        - Avoid type conversions at runtime
    - Right Outer Join >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/hWPou30rSNueYSstElcAzmsX55uhiAXK5wUi4q-8UGgKk-EXE_lzdmcMmeiRz4sNKr29vjp_1sQtDpiW2mOE8wQJaVnppPgQpeVEJx3LiUh7CeHLEuwWM1CHI6X6UFnL.png)
        - Try to use 1..N and 1..1 joint cardinality ⇒ Optimal Performance
        - Try to reduce number of joint fields
        - Avoid joining on calculated columns
        - Avoid type conversions at runtime
    - Spatial Join >>>
        - Create joins to query data from data sources that have spatial data.
        - **Point** – Represents a specific location on Earth (latitude and longitude).
        - Example: `POINT(12.9715987 77.5945627) =>`Bangalore location.
        - You can insert POLYGON(latitude, longitude) - https://arthur-e.github.io/Wicket/sandbox-gmaps3.html 
        - **Example:**
        - Table A contains customer locations as POINT data (latitude, longitude).
        - Table B contains city boundaries as POLYGON data.
        - A spatial join helps you find which customer belongs to which city by comparing the POINT to the POLYGON.
        - ![](https://remnote-user-data.s3.amazonaws.com/ImRM3gPvF8BLO5HqeiOfvEl_-xpzX13fF2D4Zg51GjVr-Zq7UAZa9vX2QYHfzjQDwALoxVEMNuThyfG9UOILFYAV7PTlXYemKJR7-YhnLzI9Oh7QFFgieMVdaFjyHWGx.png)
        - You use the regular join node in a calculation view to create spatial joins by joining two database tables on columns of spatial data types.  
        - In any regular join node ⇒ select the join ⇒ You can see spatial properties below the general properties
        - In the **Spatial** **Join** section, define the spatial join properties.  
        - Select predicate ⇒ equals
        - Select predicate ⇒ Relate >>>
            - If you select as the predicate value, in the **Intersection Matrix** text field enter the required values.
            - **Example:**
            - Table A ⇒ Contains customer locations (as POINT).
            - Table B ⇒ Contains city boundaries (as POLYGON).
            - If you want to check whether a customer’s location is exactly **inside the city boundary** or **touching** the boundary, you can define a specific Intersection Matrix.
            - .
                ```
                SELECT a.customer_id, b.city_name
FROM customer_locations a
JOIN city_boundaries b
ON ST_Relate(a.location, b.boundary, 'T*F***FF*');
                ```
            - 'T*F***FF*' means:
            - T ⇒ Touching
            - F ⇒ False (no overlap)
            - star ⇒ Ignore
        - Select predicate ⇒ Within Distance >>>
            - if you select  as the predicate value, in the **Distance** field, select a value. You can provide the distance as a fixed value or use an input parameter to provide the distance value at runtime.
            - This predicate checks if two objects are within a certain distance of each other.  
            - Table A ⇒ Contains customer locations (as POINT).
            - Table B ⇒ Contains store locations (as POINT).
            - If you want to find which customers are within **5 kilometers** of a store:
            - .
                ```
                SELECT a.customer_id, b.store_name
FROM customer_locations a
JOIN store_locations b
ON ST_Within_Distance(a.location, b.location, 5000); -- 5000 meters = 5 km
                ```
            - This will return all customers whose location is within 5 km of a store location  
        - Select Execute Join if ⇒ Predicate Evaluates to true
        - Choose **OK**.
    - Full Outer Join >>>
        - A Full Outer Join combines the behaviours of the Left and Right Outer Joins.
        - Rows from both tables that match on joined columns
        - Rows from the left table with no match in the right table
        - Rows from the right table with no match in the left table
        - A Full Outer Join is supported by calculation views only, in the standard Join and Star Join nodes.
        - However, in a Star Join node, a full outer join can only be defined on one DIMENSION calculation view. This view must appear last in the  *Star Join*  node.  
    - Text Join >>>
        - a Text Join behaves like a Left Outer Join, with cardinality 1:1 but, in addition, you specify a language column,  
        - ![](https://remnote-user-data.s3.amazonaws.com/WhG710xduZGDTS1zf9J9Tc5zRczh7N-k_aC6L_u3dn5bzP6FSGjdGFslcMJjVHwFrwUILLtTdBXFP6vE99uG_zJkM3_UrTLbOLeKH_lBxf3CMRRpKcBtthIfMgvu0izD.png)
        - During join execution, the language of the end user querying the calculation view is used to retrieve descriptions from the text table (here,  *MAKT* ) in the corresponding language, based on the language column.  
        - Then in semantics ⇒ [Label Column Field](SAP%20HANA/Nodes/Semantics%20node/Label%20Column%20Field.md)
    - Temporal Join >>>
        - Frequently, master data stores historical values. For example, in the employee table there might be two records for one employee. One record represents the employees job position in the past, and the second record represents the employees job position today. Each record contains dates to represent the validity of the record. So how does a calculation view know which record to request?
        - It is possible to add a ? condition to a join in order to find matching records from two tables based on a date. The records are only matched if a date column of one table is within a time interval defined by two columns of the other table.
        - ![](https://remnote-user-data.s3.amazonaws.com/cJ9Kd3oSX4L28m4uiX-kRd1h6wtsXyyS3-OJR2mT4Kar0c7Z_uLapLm66DgnZVy5EyK8c78PAv4oDgIeeLlRkqWTyH7Q0WDPrflPHkwVUuxIOo_VHOqsPnwznkDRO25D.png)
        - In this example, the status of the customers can change over time, and this information is captured in a dedicated table (Customer Status). If you need to analyse the sales orders and include the status of each customer when they issued the order, you create an Inner Join on the  *ContactID*  column and add a temporal condition as follows:
        - Temporal column ⇒ Date (Sales Orders)
        - From Column ⇒ DateFrom (Customer Status)
        - To Column ⇒ DateTo (Customer Status)
        - [Temporal Condition](https://help.sap.com/docs/SAP_HANA_PLATFORM/fc5ace7a367c434190a8047881f92ed8/fa8abcc4cc094d6faef77c11cd516868.html) ⇒ Include Both ⇒ determining whether the boundaries are inclusive or exclusive.  
        - Only supported in the  *Star Join*  of calculation views of the type cube with star join. The join type must be defined as  *Inner* .
        - Only columns already mapped to the output of the  *Star Join*  node can be defined as  *Temporal Columns*  in the temporal properties of the join.
        - **Temporal conditions can be defined on columns of the following data types:** 
            - timestamp
            - date
            - integer
    - Join Cardinality >>>
        - The cardinality of a join defines how the data from two tables joined together are related, in terms of matching rows.
        - For example, if you join the Sales Order table (left table) with the Customer table (right table), you can define an n:1 cardinality. This cardinality means that several sales orders can be related to the same customer, but the opposite is not possible (you cannot have a sales orders that relates to several customers).
        - We recommend that you specify the cardinality only when you are sure of the content of the tables. If not, just leave the cardinality blank.
        - That is, 1..1, 1..n, n..1, and n..m.
        - Always set join cardinalities so the optimizer can decide if joins can be omitted at runtime based on the query that is being sent to the calculation view.
        - If tables are used as the data sources, and not calculation views, then you can use the  *Propose Cardinality*  option.  
    - Non-Equi Join >>>
        - The join condition is not represented by an = (equal) operator. but instead is based on other comparison operators such as Greater than.
        - Operator ⇒ The condition of Join is other than Equal eg. Less than, Greater than, Less than equal to, Not Equal
        - Defining a Non-Equi Join condition is possible for the following types of joins: Inner, Left Outer, Right Outer, Full Outer
        - ![](https://remnote-user-data.s3.amazonaws.com/XZTX9P7syQ8ksKFWG5hxElU31N_SNMZRuTlOb7HngfQF0iW4RXTmX5fJD9YWUt3HK9w4rKjtPgxCiq4w5CgtQUnqBr-OVM4JI7V_1MdFCisdmxNk29Cj8Ss9pTGZcEDt.png)
        - ProductsToBeDelivered.id Equal SUBTASKS.id
        - ProductsToBeDelivered.dueOn Less Than SUBTASKS.plannedDate
    - Dynamic Join >>>
        - In some scenarios, you want to allow data analysis at different levels of granularity with the same calculation view.
        - ![](https://remnote-user-data.s3.amazonaws.com/Umj9bk6gtmf3O0QJgZAiHMoeKQdDOpgdVAsGTwWlOH71txlYPJlGriWzG4wgH29W64Zyh7TJVjdvaUtjKPBG4ohosMY-LwvPQZrMXXhmU1SVr5tupZFXNXjJeNL-3fuw.png)
        - In this case, assuming that you model your calculation view with a Regular Join on  *Country*  and  *Region* , you will get correct results if you analyze the data by country, but the results will be inconsistent if you analyze the data by region.  
        - With this Join, only the join columns requested in the query are brought into context and play a part in the join execution. As a consequence, the same calculation view can be used for both purposes, that is, to analyze data by country or by region.  
        - A Join can be defined only with multi-column joins. 
        - With this Join, if none of the joined columns are requested by the client query, you get a query runtime error.  
        - If we consider the behavior of the join from an aggregation perspective:
            - In a Regular (static) Join, the aggregation is executed after the join.
            - In a Dynamic Join, when a joined column is not requested by the client query, an aggregation is triggered to remove this column. Then, the join is executed based only on the requested columns.
    - Adding a Filter to a Join Node >>>
        - When you define a filter (a filter expression) in a join node, you have a possibility to optimize the runtime execution of the calculation view. With filter mapping, when a filter is defined on a column from one source, you can ask the SQL optimizer to also apply this filter to a column of another other source.
        - From SAP HANA Cloud QRC 4/2023, it is also possible to define filter mapping in non-equi joins.
        - The improved performance results from the early execution of filters on both sources, before the join is executed.  
        - ![](https://remnote-user-data.s3.amazonaws.com/2IJ6IkyS2S-YIjm7VRUokM1Yhj-3_RcKHw7iEsdRGFYd-aCit6kyRN0eb-Ahs0Mv_2oG4uHyz5SVhfGDNo_zWrqFnIhkaPtmkEJohAZDQAL38tg00ckX6HvdqtUs3zR8.png)
        - A  *Direction*  must also be specified, so that the optimizer knows from which side (left or right) an existing filter must be mapped to the other side. The default direction is  *Left and Right* , that is, bi-directional  
        - Left ⇒ Right : If Projection_1.product = 'Apple', only records with Projection_2.product_name = 'Apple' will be considered in the join.
        - Right ⇒ Left: If Projection_2.product_name = 'Orange', only records with Projection_1.product = 'Orange' will be considered in the join.
        - Left and Right : If `Projection_1.product = 'Apple'` and `Projection_2.product_name = 'Apple'`, the join will work.  If one side doesn’t match, the row gets excluded.
    - Joining Multiple Data Source in a Join Node >>>
        - Support in cube with star join
        - In the Join definition select the canvas
        - Select the central table
        - Select the Multi Join Order
            - Inside Out ⇒  The join that is near to the central table is executed first
            - Outside In ⇒ The join that is far from the central table is executed first
            - ![](https://remnote-user-data.s3.amazonaws.com/pyjWQT34xS5vgSGxMBQK8K0AL_6DlACNfriulD3jVCGSgTCu6klrURKxFChdRUKMHHXNhYSqbnJ4ywdyuhqd8U6ubzTv9adGNLinN4TVUIIjtn1YNoXN26aMpM60IETE.png)
            - ![](https://remnote-user-data.s3.amazonaws.com/hpOVXKVk3OWOBrFGu3W6rSEzX4PXNYrsIxv223_BIqorBpIn8KZc8y1FAg-DzOLJG1X7-MEvNrtgPd3DgQBwHB5GCPW7i_Rf8OYtFiWkmhN7F4Z3piHOBPWnA-43je8u.png)
            - In the figure, Multi-Join Scenarios, the multi-join order property only applies in scenarios 1 and 2, and affects joins J1 and J2. The precedence between joins J1 and J3 (in scenario 2) or J1 and J2 (in scenario 3) is not controlled by the multi-join order setting.  
    - 
- Views
    - Calculation views >>>
        - Not materialized
        - SQL expressions can be used
        - Calculation views can adapt its Behavior to the list of columns that are projected for example the granularity of the rank node can be determined dynamically depending on whether the query results by country or by country and customer
        - Calculation views are read only, and cannot change data in the SAP HANA Cloud database.
        - **Supported Tables types in Calculation view** 
        - [Row Tables](SAP%20HANA/Views/Row%20Tables.md)
        - [Column Tables](SAP%20HANA/Views/Column%20Tables.md)
        - [Virtual Tables](SAP%20HANA/Views/Virtual%20Tables.md)
        - [Calculation views](SAP%20HANA/Views/Calculation%20views.md) 
        - [SQL Views](SAP%20HANA/Views/SQL%20Views.md)
        - [Table functions](SAP%20HANA/Views/Table%20functions.md)
        - Calculation Engine >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/DsAqY8s_WwZKheHQJVd0WK_mbadotP-u9mnNJvl9Fa2vJMZDvLxKJ6kWdNs3Dxzc8dRZAZwJee08S_VQpSaoThmx8Ffppi14uijz-EjA95FhGyf5TpUTXvrqIvmERs4N.png)
            - The calculation engine pre-optimizes runtime model after applying pruning's such as [Dynamic Join](SAP%20HANA/Joins/Dynamic%20Join.md),[Pruning configuration table](SAP%20HANA/Nodes/Union%20node/Pruning%20configuration%20table.md), [Column Pruning](SAP%20HANA/Optimization%20Theory/Column%20Pruning.md) before they are worked on by the SQL engine.  
            - A calculation view is instantiated at run-time when a query is executed. 
            - After this, the SQL optimizer applies further optimizes and determines the query execution plan - for example, it determines the optimal sequence of operations, and also those steps that could be run in parallel.  
    - Dimension View >>>
        - Projection node is default second node
        - Dimensions are re-useable objects. When they have been created, they can are shared by developers and used to develop star schemas.  
        - **Processing Engine:** Join Engine
        - Dimension calculation views are only built from attributes. They do not include measures. Every column from the data source is treated as an attribute. This means that any numerical column in the source record, such as price or salary, is treated as an attribute and not a measure. 
        - When you query a dimension calculation view, a complete list of every single individual value in the source is produced in the output. Even the numerical values such as quantities are listed individually and not aggregated. If you want to sum values then you need a cube calculation view where it is possible to define measures.
        - If you would like to produce only a  *distinct*  list of attribute values, you can specify aggregation on any column. Then each unique value appears only once in the result.  
    - Cube View >>>
        - Aggregation View is default node
        - When you would like to create a calculation view that includes measures, you use a calculation view of the following type: cube.  
        - **Processing Engine:** OLAP Engine  
        - By default, all the measures in this type of calculation view will always be aggregated by the attributes requested by the query. Consequently, even though the calculation view may be able to provide many attributes, the measures are always automatically aggregated only by the attributes that were requested by the query.  
        - For example, your cube calculation view provides the measure: revenue, and the attributes: country and city. The query requests only country, and so revenue is summed by country and not by city. If the next query requests the measure: revenue, and the attributes: country and city, then the revenue would be summed by city and also country. This means that you will have two levels of aggregation.
        - This type of calculation view is optimized for ad-hoc analysis, where unpredictable  [Slice-and-Dice](SAP%20HANA/Concepts/Slice-and-Dice.md) is required over the measures by any combination of attributes within the model.
    - Time Dimension View >>>
        - You use views to automatically generate various date-related attributes  like year, month, day, week, days in a week from a base date  *dd-mm-yy*  in customer table. All that is needed is the base date in the source record. From that we can derive all possible time attributes automatically.  
        - **Granularity** 
        - Year ⇒ _SYS _BI:M_TIME_DIMENSION_YEAR
        - Month ⇒ _SYS _BI:M_TIME_DIMENSION_MONTH
        - Week ⇒ _SYS _BI:M_TIME_DIMENSION_WEEK
        - Second, minute, hour, day ⇒ _SYS _BI:M_TIME_DIMENSION
        - The tables M_TIME_DIMENSION is precreated and then you join
        - Create New Time Dimension HANA DB Artifact >>>
            - Data category ⇒ DIMENSION
            - Dimension type ⇒ TIME
            - Calendar ⇒ [Gregorian Calendar](SAP%20HANA/Views/Time%20Dimension%20View/Gregorian%20Calendar.md)
            - granularity ⇒ Week/Date
            - time dimension artifact ⇒ schema:m_time_dimension_week/_SYS_BI:M_TIME_DIMENSION
        - Gregorian Calendar >>>
            - is the standard international calendar used for civil purposes.
            - It consists of **12 months** and **365 days (or 366 days in a leap year)**.  
            - It starts on **January 1st** and ends on **December 31st**.  
            - SYS.update_time_dimension >>>
                - You need to ensure T005T and T005U are available and filled
                - This procedure allows you to generate time data using SQL statements
                - For example, you can choose to generate records between 2020 - 2024. 
                - You can regenerate the data at any time to keep the table up-to-date. The generated table is used as the data source to the time-based dimension calculation view
                - granularity ⇒ 'WEEK'
                - START_YEAR ⇒ 1999
                - END_YEAR ⇒ 2024
                - FIRST_DAY_OK_WEEK ⇒ 1
                - TARGET_SCHEMA_NAME ⇒ '' // You can give the schema name if you don't give schema name then default schema is _SYS _BI
                - TARGTE_TABLE_NAME ⇒ '' //This will create table in the target schema based on granularity if not specified then default table m_time_dimension_week in target schema or _SYS _BI
                - RECORD_COUNT ⇒ ?
        - Fiscal Calendar >>>
            - Also called a financial year or accounting year) is used by governments,  
            - the fiscal year **does not always start on January 1st**. It varies by country and organization.  
            - SYS.update_fiscal_calendar >>>
                - You need to ensure T009 and T009B are available and filled
                - Create your own table having structure _SYS _BI.M_FISCAL_CALENDAR 
        - When you create a time-based dimension calculation view, a table is also generated that is automatically filled with records that represent the data attributes for a range of dates that you specify. For example, you can choose to generate records between 2020 - 2024. You can regenerate the data at any time to keep the table up-to-date. The generated time table is used as the data source to the time-based dimension calculation view. You then consume the time-based dimension calculation view in your cube calculation view.  
        - ![](https://remnote-user-data.s3.amazonaws.com/xdwSx2anJ_AzmzrMbrNJT5OlnCw1WNkRgfqtLa0drbG6r1m4v2ExvvwRQOmNQou4pZxGanV0Gu9j39BXALq13nw02YAQAPGyMHo29yGGIHpUlYEv5xgk4KUfPUDzSNIo.png)
    - Hierarchies >>>
        - A hierarchy is a structured representation of an organization, a list of products, the time dimension, and so on, using levels.
        - It is often used to provide the easy navigation of a large data set in a drill-down. 
        - Is available only in Symantics node
        - ![](https://remnote-user-data.s3.amazonaws.com/Q38kfsLKuH6885VxwuAHrBQIEDijYT8OWF14OTDVZDZWtIc4AMtt4XWfvZ8DFDjz18ieverGTFf4Idn_-OPwGDPtUA82jS7lGoOOljVkHTQpEmo3qlC2iKsJAqcJorsV.png)
        - Level Hierarchy >>>
            - A level hierarchy requires each level to be stored in a separate column. Rows represent leaf nodes.
            - Heterogeneous fields (possibly with different data types) are combined in a hierarchy.
            - A level hierarchy is defined by choosing columns from a source data set and organizing them in a sequence that provides a drill-down path. Each level is based on a separate column in the source data.  
            - In the Semantics node, select the Hierarchies tab and click the '+' button in the Hierarchy pane.  
            - Add the columns to the hierarchy in the correct level order from top to bottom, with the lowest granularity at the lowest level of the hierarchy.
            - Additionally, you can define an ascending or descending sort direction per level.
            - ![](https://remnote-user-data.s3.amazonaws.com/IeZf2OrC9OI1jcGtiwCGV_vDIM-Q6wYIogS7mGwsUnnfdke-69EvSRJ-NvNEaRCdT5oGN-BOAVh2LvkF2ORNAAi6g78WuweZbps778CfxNVV3orqyET7aWlVFhLpcDAx.png)
            - **Node styles are used to define the output format of a node ID.** 
            - Using a fiscal hierarchy example, the following table demonstrates the different node styles:  
            - Level Style  | Output | Example
            - Level Name | Level and node name | MONTH.JAN or COUNTRY.GERMANY
            - Name Only |Node name only | JAN or Hamburg
            - Name Path | Node name and its ancestors | FISCAL_2015.QUARTER_1.JAN or EUROPE.GERMANY.Hamburg
            - **Level Types** 
            - A level type specifies the semantics for the level attributes. For example, the level type TIMEMONTHS indicates that the attributes are months such as January, February, or March.
            - The level type REGULAR indicates that the level does not require any special formatting.
            -  *Order By ⇒*  dropdown list, an attribute can be selected for ordering the hierarchy members in the order specified in the  *Sort Direction*  column.  
        - Parent and Child >>>
            - A parent-child hierarchy requires columns of the same data type for parents and children. Rows represent each node.
            - Parent-child hierarchy columns usually contain IDs or key fields instead of plain text.
            - You can define multiple parent-child pairs to support the compound node IDs - for example:
            - CostCenter and ParentCostCenter
            - ControllingArea and ParentControllingArea
            - Here’s an example of a compound parent-child definition to uniquely identify cost centers:
            - CostCenter: CC1001, CC1002, CC1003
            - ParentCostCenter: CC1000, CC1000, CC1001
            - ControllingArea: CA01, CA01, CA02
            - ParentControllingArea: CA00, CA00, CA01
            - In this example:
            - CostCenter CC1001 is a child of ParentCostCenter CC1000 within ControllingArea CA01.
            - CostCenter CC1002 is also a child of ParentCostCenter CC1000 within ControllingArea CA01.
            - CostCenter CC1003 is a child of ParentCostCenter CC1001 within ControllingArea CA02.
            - Advanced Properties of a Parent-Child Hierarchy >>>
                - ![](https://remnote-user-data.s3.amazonaws.com/37H9XezLFktUz0KKLQfxavRzZi_4H_v5S2b0Kq1r4HqIFCFkmy0fF0glDZVPF37LNeKrNuHbuzflszjBbao8rgLK818ymn6smXmQRFkMHeB72at8C9kT140ygHSwyjsD.png)
                - **Aggregate All Nodes** 
                - defines whether the values of intermediate nodes of the hierarchy should be aggregated to the total value of the hierarchy’s root node. If you are sure that there is no data posted on aggregate nodes, you should set the option to False. The engine then executes the hierarchy faster.
                - Root Node: Company
                    - Intermediate Node: Department A
                        - Leaf Node: Cost Center 1
                        - Leaf Node: Cost Center 2
                    - Intermediate Node: Department B
                        - Leaf Node: Cost Center 3
                        - Leaf Node: Cost Center 4
                - Cost Center 1: $10,000
                - Cost Center 2: $15,000
                - Cost Center 3: $20,000
                - Cost Center 4: $25,000
                - Aggregation:
                - Department A: $10,000 (Cost Center 1) + $15,000 (Cost Center 2) = $25,000
                - Department B: $20,000 (Cost Center 3) + $25,000 (Cost Center 4) = $45,000
                - Company: $25,000 (Department A) + $45,000 (Department B) = $70,000
                - **Default Member** 
                - The Default Member value ⇒ helps you to identify the default member of the hierarchy. 
                - This default member acts as a fallback option when a hierarchy level is not explicitly defined in a query, ensuring consistent and predictable query results.
                - If you do not provide any value, all members of the hierarchy are default members.
                - Orphan Nodes >>>
                    - An orphan node in a hierarchy is a member that has no parent member.
                    - Level hierarchies offer four different ways to handle orphan nodes. They are as follows:
                    - Root Nodes
                    - Any orphan node will be defined as a hierarchy root node.
                    - Error
                    - When encountering an orphan node, the view will throw an error.
                    - Ignore
                    - Orphan nodes will be ignored.
                    - Step Parent
                    - Orphan nodes are assigned to a step parent you specify.
                    - If you choose to assign an orphan node to a step parent, the following rules apply:
                    - The step parent node must be already defined in the hierarchy at the ROOT level.
                    - The step parent ID must be entered according to the node style defined in the hierarchy
                - Root Node Visibility >>>
                    - The value helps modeler know if it needs to add an additional root node to the hierarchy.  
                    - This ensures that the hierarchy has a well-defined starting point
                    - Consider an organizational structure stored in a parent-child hierarchy, where each employee reports to a manager, and the hierarchy culminates at the CEO.
                    - **Add Root Node:** By setting the Root Node Visibility to "Add Root Node" and specifying "Company" as the root node value, the hierarchy would display "Company" at the top, under which the CEO and subsequent reporting lines are organized.
                    - **Ignore Root Node:** If the Root Node Visibility is set to "Ignore Root Node," the hierarchy would begin directly with the CEO, followed by the respective managerial levels, omitting the overarching "Company" node.
                - Time-Dependent Hierarchies >>>
                    - If elements in your hierarchy are changing elements (time dependent elements) such as human resources applications with their organizations, or material management systems with BOMs where information is reliant on time., you can enable the parent-child hierarchy as a time dependent hierarchy. In other words, if you are creating hierarchies that are relevant for specific time period, then enable time dependency for such hierarchies. This helps you display different versions on the hierarchy at runtime.  
                    - Your source data needs to contain definition columns consisting of a  *Valid From*  and a  *Valid To*  column.  
                    - When a hierarchy needs to show elements from an interval, you have to define two input parameters; a  *From*   *Parameter* , and a  *To Parameter*   
                    - If the hierarchy needs to show elements valid on a specific date, you need one input parameter defined as the  *Key Date* .  
                    - ![](https://remnote-user-data.s3.amazonaws.com/4nxnhxsKORX5KOdMJ10fSAmsZHkA-erZdt314B9hpWuV0waOxlF1BQMxCYhcX8xFJQ9fVJt0FfatUCgRoMSlegbLtH6pM_PHaBoaUrNj0KUF55D_IwbFJCjTmsZCN6EV.png)
                    - Consider a company's organizational chart where an employee transfers from the "Marketing" department to the "Sales" department on January 1, 2025. 
                    - The employee's record would have two entries: one with a **Valid To** date of December 31, 2024, under "Marketing," and another with a **Valid From** date of January 1, 2025, under "Sales."  
                    - By setting the query's key date to December 2024, the employee appears under "Marketing." Setting it to February 2025 shows the employee under "Sales."  
        - Type of Generated Hierarchy >>>
            - When you define a hierarchy in an SAP HANA Cloud calculation view, whether it is a level or parent-child hierarchy, the hierarchy is materialized upon build/deployment by various tables and/or views in the HDI Container schema (column views or classic SQL views)
            - These generated objects provide detailed hierarchy node relationship data to enable processing of the hierarchy when the front-end tool is not able to generate the hierarchy relationships itself.
            - A key setting allows you to influence the way SAP HANA translates the defined hierarchy into technical tables and views in the database. This is the Hierarchy Type setting, which you can define in the Semantics of the Calculation View, in the View Properties  ⇒ General tab.
            - Auto (default value)
            - This setting is useful if you exchange views between SAP HANA Cloud and On-Premise, because upon build, SAP HANA generates the following:
            - In SAP HANA Cloud: classic SQL views to materialize the SQL hierarchy
            - In SAP HANA On-Premise: MDX hierarchies (only compatible with SAP HANA on-Premise), including the metadata defining the hierarchy, as well as column views materializing the MDX hierarchy. No SQL Hierarchy view is generated.
            - SQL Hierarchy Views
            - Upon build, classic SQL views are generated to materialize the SQL hierarchy.
            - No Hierarchy Views
            - Upon build, the metadata of the hierarchies is generated (_SYS_BI.BIMC* tables) but the hierarchies themselves (detailed list of members, and so on) are NOT generated.
            - This setting is should be used when the consuming front-end tool itself can generate the set of hierarchy members and their relationships, based on the hierarchy metadata defined in the BIMC tables.
            - Assume one level hierarchy, PROD_LEV_HIER, has been modeled within the Calculation View HC300::CVC_SALES_HIER.
            - Then the SQL Hierarchy view is called HC300::CVC_SALES_HIER/PROD_LEV_HIER/sqlh/PROD_LEV_HIER.
        - [Query Hierarchies using SQL](SAP%20HANA/SQLSCRIPT/Query%20Hierarchies%20using%20SQL.md)
    - Projection node >>>
        -  To select only the required columns from a data source.
        -  To define calculated columns.
        -  To define Input parameters and Variables that request values at run-time, such as user-prompts.
        -  To apply a filter on the data source.
    -  Cube with Star Join Calculation View >>>
        - An extension to the cube calculation view is the cube with star join.  
        - **Processing Engine:** OLAP Engine  
        - In addition to the capabilities of the cube type of calculation view, a cube with star join calculation view allows you to connect dimension calculation views so that you significantly expand the capabilities for analysis by providing additional attributes. For example, if you create a sales cube calculation view, which provides only limited attributes such as a product number, you could then join the product dimension calculation view to provide many more product-related attributes such as product description, supplier, color, and price. You could then aggregate the sales revenues by product color, supplier, and so on.  
        - The type of joins between the fact and dimension tables within the star schema can be defined in the  *Star Join*  node  
        - ![](https://remnote-user-data.s3.amazonaws.com/Yslp05FEvFpQkeEfshSPX59-FAv1adfDYRgIH5GRuBrd_fczNfc_vg_BI_iWsjuoNPLvCmEDAuqLBev_K49xOMB8LbN72loPv_2WRM7M55pK2Uir_RlZFVAh3TpKAcBK.png)
        - 
    - Client-Dependent Views >>>
        - Almost all tables in SAP applications are client-dependent. When you select data from an SAP system database, it usually only makes sense to request data for one client number.  
        - calculation view can use the client column property to enable automatic client filtering when you work on data from SAP applications. You do not need to define an explicit filter on the client column each time you create a calculation view.  
        - ![](https://remnote-user-data.s3.amazonaws.com/yK1X0OJemdsphoN6WdQv1hqkAkA-aWOsWe5mURmNUzAwsA50S8Pn4dAZ-LhCguxQ5FM5zKHCPcwDV_-LvKOWrJcHfxfEnAvSZKb8ZI8J2t4uRZ0hZXbTKnH2vYEHTS1e.png)
        - **Default Client** 
        - Cross-Client: All the data is retrieved regardless of the client number.
        - Fixed Client: You specify an SAP Client number (for example, 200) and the data sources are automatically filtered to include only the rows with this client number.
        - Session Client: The source tables are filtered dynamically based on a client value that is specified for each user in the USERS table of SAP HANA Cloud.
        - As the technical user has no default client assigned, if you specify a Column Client property for a calculation view and set the Default Client to Session Client, the data preview in the Developer perspective will not retrieve any data.
        - To by-pass the technical user and apply client filtering based on a classic database user, you can do one of the following with this user:
        - Use the data preview feature Data Preview with Other Database User
        - Client Columns
        - For databases coming from the remote system
        - Select the table in the mapping and expand the properties tab at the bottom
        - You can find a client column select field click on the value help and select the client column
    - SQL Views >>>
        - created using the SQL language
    - Column Tables >>>
        - SAP HANA Cloud supports optimized tables. 
        - These tables are optimized to provide you with a very fast read-performance  
    - Row Tables >>>
        - SAP HANA Cloud supports traditional tables
        - It makes no difference to a modeler whether the table is row or column and all modelling functions are available with both types
    - Virtual Tables >>>
        - Is a table that is part of the SAP HANA Cloud database and is mapped to a remote table outside of SAP HANA Cloud.  
        - you reach external data sources (database tables, flat files, spreadsheets)
    - Table functions >>>
        - Functions can be used to define complex data sources using [SQLScript](SAP%20HANA/SQLSCRIPT/SQLScript.md) are used to return a tabular data  
        - All output parameters of the table function are offered as input columns to the calculation view node. If your table function requires input parameters you can first define input parameters in the calculation view and then use the [Parameter Mapping](SAP%20HANA/Data%20Model%20Concepts/Parameter%20Mapping.md) function to map them.  
        - [Table Function Node](SAP%20HANA/Nodes/Table%20Function%20Node.md)
        - .
            ```
            FUNCTION "com.sap.hana.example::TABLE_FUNC_SCALE" (VAL CHAR)
RETURNS TABLE (A INT, B INT)
LANGUAGE SQLSCRIPT
AS
BEGIN
    RETURN SELECT A, :VAL * B AS B FROM MYTABLE;
END
            ```
    - Scalar Function >>>
        - Think of them as dynamic tables that are created at runtime.
        - Reusable block of data processing logic using the powerful SQLScript language to generate a result as either a single value (scalar), or a complete data set (table ).
        - These are always read-only. This means it is not possible to use any data definition language such as create table or alter table. It is not possible to use data manipulation statements such as update or insert into. These block never change the database.
        - One of the common uses of a scalar function in a calculation view, is to derive values for input parameters of calculation view
        - Can call other functions, which encourages a modular design approach to maximize reuse.
        - .
            ```
            FUNCTION "com.sap.hana.example::SCALAR_FUNC_ADD_MUL" (x DOUBLE, y DOUBLE)
RETURNS RESULT_ADD DOUBLE, RESULT_MUL DOUBLE
LANGUAGE SQLSCRIPT
AS
BEGIN
    RESULT_ADD := :x + :y;
    RESULT_MUL := :x * :y;
END
            ```
    - 
- Nodes
    - Intersect Node >>>
        - The intersect node is used to select the rows from one data source that also appear in another data source. You choose the columns on which the intersection check is made.  
        - works on entire data sets 
        - We need to map to the same columns in output table eg. projection_A has REGION and CATEGORY. Projection_B has REGION and CATEGORY. We have only one REGION and CATEOGRY in output table projecting from two views
        - The difference between INNER JOIN and INTERSECT in SQL lies in their purpose and behaviour when handling sets of data:
        - **Data Relationship**: `INNER JOIN` explicitly connects tables based on a relationship (e.g., foreign key), while `INTERSECT` works on entire result sets and does not require table relationships.
        - **Output Scope**: `INNER JOIN` outputs columns from both tables, whereas `INTERSECT` only provides the intersecting rows matching all selected columns.
        - .
            ```
            ```
SELECT ID, Name FROM TableA
INTERSECT
SELECT ID, Name FROM TableB;
```
            ```
        - This query fetches rows (ID and Name) that are common to both `TableA` and `TableB`.
        - ![](https://remnote-user-data.s3.amazonaws.com/ISRwvHFVVwSq1hgyiy7ED2grDo6FnRznynORS1XcGvvdP7z42xirK-oHF9FJD8BVzLos1_mdw3K-HSsN_6nklod4Jf18aOxb0PhpyMhMOHaExqWAsgKITKOB-gVHCVqZ.png)
        - Minus node >>>
            - The minus node is used to select rows that appear in one data source but not another. Again, you choose the columns on which the minus check is made.  
            - For the  *Minus*  node, the data sets are considered based on their order in the list of data sources for the node. Therefore, the output contains items from the FIRST data source that are NOT in the SECOND data source. In SAP HANA Cloud, it is possible to change the order of the data sources: just right-click on the  *Minus*  node and choose  *Switch Order* .  
            - There are two data sets - data set A and data set B 
            - Dataset A contain all the regions
            - Dataset B contain only US region
            - If I add these two data source in minus node then the output is the data set containing all regions except US region
    - Semantics node >>>
        - This node is used to define enrichments to the final results set, such as adding currency information to measures, or apply masking rules to cover up sensitive parts of a data  
        - it is already present and will always be the top node  
        - Display separately the view-global information, such as view properties (including caching and snapshots queries),  
        - In this node, you assign semantics to each column in order to define its behaviour and meaning. This is important information used by consuming clients so that they are able to display and process the columns appropriately.  
        - One of the most important and mandatory settings for each column is its column type. You can choose between attribute or measure  
        - Private tab contains the output column you created from the previous node
        - Shared tab will show all the outputs from other dimension views
        - Hiding a column - This can be used if a column is only used in a calculation, or is an internal value that should not be shown, for example, hiding the unhelpful  *product id*  when we have assigned a description column that should be shown in its place.
        -  *Semantics type*  >>>
            - you can also optionally assign * to each column * 
            - For example, if you define a column as a date semantic type, the front-end application can format the value with separators rather that a simple string. The key point is that it is the responsibility of the front-end application or consuming client to make use of this additional information provided by the semantic type.  
        - Sort Direction Property >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/PnWjbKirytF8XzrFoyqc9YWlJzoqfXuzwppiGbWbAXdgauxmr8UWQObwTAiAAnJczO-7jaPJgaqWBL1reg586TzDTVGpoeBrLB54k991cSG6en7FxNnopsw6asTHFjsg.png)
            - Suppose a calculation view (a type of database view) is designed to sort data by two columns, say `SO_ID` first and then `PRODUCT_ID`.
            - If a client (like a front-end tool) sends a query that only specifies sorting by `PRODUCT_ID`, the database might not maintain the original sorting order defined in the calculation view (`SO_ID` then `PRODUCT_ID`).
            - **Result Set Order**: As a result, the data might not be sorted as expected. To ensure the data is sorted by both `SO_ID` and `PRODUCT_ID`, the client query must explicitly include both columns in the `ORDER BY` clause.
            - In essence, to achieve the desired sorting order, the client query needs to match the sorting columns defined in the calculation view. Does that help clarify things?
            - the client query will take priority. If the client query specifies sorting only by `PRODUCT_ID`  
            - ![](https://remnote-user-data.s3.amazonaws.com/9kUGGthi8i4WTDipwRiFTY-PYMRPaNFlOo0JJvdxaDnEoaucs0cZOrHkiiaihiijGlihvSvJMwsqZZBpO58PKoEl7aPWKi_lp-r9_He1egcdJIVUXUrxPHJE_rGxkFyr.png)
        - Label Column Field >>>
            - Assigning a description column to another column - for example, assigning the product id column to a product description column so that a user sees a value which is more meaningful.
            - navigate to the "Semantics" node within the view editor, select the column you want to describe, then in the properties panel, use the "Label Column" field to choose the column containing the description text  
            - ![](https://remnote-user-data.s3.amazonaws.com/Hyy9htAbtwS9899NIrf5iYlquPs8YliLgb-quoyzEefG92JKPwWGNT7-dJ1qMGYc_WDulZ1ouR2V5W2mJ3ukOxXIPeZNuRMRjY0buGlv9hu-gjv3D6zRlxcVeFTZ3dmw.png)
        - Null Values >>>
            - Columns, both attributes and measures can contain undefined values or null values. You can handle such cases by defining default values that replaces the null values in reporting tools.
            - For example, you can replace the column values that would usually appear with the null value representation of '?' with a default value 'Null', or 'Empty' or with any other user defined value that you prefer.
            - Select the Semantics node
            - Choose the Columns tab
            - Select a measure or attribute
            - Select the 'Null Handling' checkbox
            - Optionally, in the Default Value text field, provide a default value
            - If you have enabled null handling for columns and if you have not provided any default value, then the tool considers the integer 0 as the default value for columns. However, for columns of data type NVARCHAR, if you have not defined a default value after enabling null handling, the tool displays an empty string, (which means blank), as the default value.
        - Private and Shared Columns >>>
            - the  *Columns*  tab of the Semantics node separates columns into two categories  
            - **Private** 
            - Private columns are columns that are defined inside the calculation view itself. These can be measures or attributes. You have full control over these columns.
            - **Shared** 
            - Shared columns are columns that are defined "externally", in one or more dimension calculation views that are referenced by your cube with star join calculation view. On these columns, you have logically less control, because they are potentially "shared" with another cube with a star join calculation view. Still, you can hide some of these columns to only keep the ones that you need.
            - Regarding the shared columns, their  *Name*  and  *Label*  properties cannot be changed, compared with a private column, but you can define an Alias Name and an Alias Label. Moreover, providing Alias Names is mandatory if column names from the underlying dimension calculation views conflict with each other or conflict with the private column names.  
        - [Properties of Views - General](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/working-with-common-features-of-calculation-views_db0c1ca5-73de-43c3-943b-31af594e6dde) >>>
            - Data Category  ⇒ For calculation views, this determines whether the view supports multidimensional reporting.
            - Type ⇒  *Standard*  or  *Time*   
            - Default Client ⇒ Defines how to filter data by SAP CLIENT (aka MANDT).
            - Apply Privileges ⇒ Specifies whether analytic privileges must apply when executing a view (SAP Business Application Studio for SAP HANA modeling supports SQL analytic privileges only).
            - Enable Hierarchies for SQL access ⇒ In a calculation view of type CUBE with star join, determines whether the hierarchies defined in shared dimension views can be queried via SQL.
            - End-User View ⇒ Specify whether to offer this calculation view as a source to reporting tools.
        - [Properties of Views - Advanced](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/working-with-common-features-of-calculation-views_db0c1ca5-73de-43c3-943b-31af594e6dde) >>>
            - Propagate instantiation to SQL Views ⇒ If the calculation view is referenced by an SQL view or a Core Data Services (CDS) view, it determines whether the calculation view must be instantiated. For example, it prunes columns that are not selected by the previous queries) or executed exactly as it is defined.
            - Analytic View Compatibility Mode  ⇒ If this setting is activated, the join engine ignores joins with cardinality n..m defined in the star join node when no column at all is queried from one of the two joined tables.
            - Ignore Multiple Outputs For Filter ⇒ Optimization setting to push down filters even if a node is used in more than one other node.
            - Pruning Configuration Table ⇒ Identifies which table contains the settings to prune Union nodes.  
            - Execute in ⇒ Determines whether the model must be executed by the SQL engine or column engine.
            - Execution Hints ⇒ This property is used to specify how the SAP HANA engines must handle the calculation view at runtime.
        - Aggregation Attributes >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/2JIAMKypgqY13HC8hWiD8Umc_Oj8WMGlajl0VRL03F0cE6iU8fOC6jn-SXwuRwJ5dLIJNlgA1m0oYkcQ5wZnpqT2VMuO9ZdidSkdDVUaSyKLgl-ZhaTk8tdFjoMa618c.png)
            - For each measure of the calculation view, you can define one or several attributes that should be kept when aggregating data.  
            - The information entered in the Semantics is meant to be used by reporting tools  
            - The setting defined in the Semantics has no influence on the way aggregations are executed by the calculation engine. Besides, it does not prevent the execution of a query that might return inconsistent data because an aggregation attribute needed for a given column has not been included.  
        - Keep Flag >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/eBMUCmr9_dOnbZggKN1EY7DxSE9P9wpoIOwxmF9VuYbN-r-8ZsefrlLK4y-PYy6u-guGHRWrHq84vw2n27oRH72TsOeoeMpgpv2j0R7A7BK5iBrs8td5sh23uD1ghuEj.png)
            - Setting the  *Keep Flag*  property to true for the  *Order ID*  column forces the calculation to be triggered at the relevant level of granularity, even if this level is not the grouping level defined by the client query.  
            - The  *Keep Flag*  option can also be defined on the shared columns in the  *Star Join*  node of a CUBE with Star Join calculation view   
            - for any calculations column ⇒ multiplication should happen first and then summation or subtraction
            - To include a lower level of granularity
            - In the aggregation node you need to enable the keep flag property for Primary ID like order_id, employee_id
            - By doing this you get accurate results ⇒ For each order_id we multiply the price and quantity and then sum take place
        - Transparent Filter >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/Boep4LVGn-m3nUXMPIjeHpM4Inu8gvxxsgQfhhJYb5W-XDNiZ82NtdO4u07BAsR10ksiUCdwAzXKg0MJNvuSQ9e_6IWf-RdnVUm65Hj4ShvlhoeQ7TydPya7Msg_3rzN.png)
            - In this scenario, for example, calculating the number of stores that sold mice to John or Susan triggers an intermediate calculation of the StoreCount sum by product and by customer, instead of calculating the StoreCount by product.  
            - Setting the  *Transparent Filter*  property to true for all models and nodes that reference the  *Customer*  column will stop the  *Customer*  column from being unnecessarily used in the  *Group By*  clause.  
            - **This property is required in the following situations:** 
            - When using stacked views where the lower views have distinct count measures ⇒ By setting the **Transparent Filter** property to true, you ensure that the distinct count measures are aggregated correctly, even when filters are applied in the upper view.
            - When queries executed on the upper calculation view contain filters on columns that are not projected
            - If a filter defined in a lower node of a calculation view should be transparent for another view that consumes it, you must select the  *Transparent Filter*  property of all nodes from that node up to the top calculation view node.  
    - Rank Node >>>
        - The purpose of the Rank node is to enable the selection, within a data set, of the top or bottom 1, 2, ... n values for a defined measure, and to output these measures together with the corresponding attributes and, if needed, other measures.
        - For example, with a  *Rank*  node, you can easily build a subset of data that gives you the five products that generated the biggest revenue (considering the measure  *GROSS_AMOUNT* ) in each country. The country, in this example, defines a Logical Partition of the source data set.  
        -  *Rank*  node can partition the source data, but it does NOT perform any aggregation on the source data.  
        - When the source data for the  *Rank*  node is a table, you must make sure that the data structure suits your modeling needs. If not, you might need to first aggregate data by adding an  *Aggregation*  node used as a data source by the  *Rank*  node.
        - When the source data for the  *Rank*  node is a CUBE calculation view, the aggregation defined in that calculation view is implicitly triggered based on the columns requested by the rank node, but a column that is mapped to the output of the  *Rank*  node but not consumed by the upper node will not be requested to the data source.
        - As is the case with other types of nodes, some columns are passed to the upper nodes even when they are not requested. For example, this is the case when the calculation view has a sort order defined in the  *Semantics*  node (all the columns used for sorting are passed to consuming views). This is also the case when special settings such as [Keep Flag](SAP%20HANA/Nodes/Semantics%20node/Keep%20Flag.md) are used.  
        - ![](https://remnote-user-data.s3.amazonaws.com/JqIbkMs6nndiZ8a3XvICP79jqI3pdhiPHihqrqFB15aF-0JQ9GScJVQ-C6zJIl7fQfB2nIgU9W23dD3B30RI7frmeyfrfApQnttae23hUsg9Vsx_Uukr5AZjK5pfBmwb.png)
        - Aggregate Function >>>
            - Rank, row, dense rank, 
            - sum ⇒ The  *Sum*  aggregation function generates a cumulative sum of the sorted column up to the target value.  
            - ![](https://remnote-user-data.s3.amazonaws.com/r-PBf7Augbav_NP4Hx-fQZWha2Kyu4x0RJE-IrN_u1SAXRtJNMyTFbaH6cZ0ovdySE0l0KpnctK8Necm21U549lu02TSfLYemX6lQ1dp8IWPBLOOnqwJZPO2uDweQqLx.png)
        - Result Set Direction ⇒ Top
        - Result Set Type ⇒ Absolute/Percentage
        - Target Value >>>
            - Fixed or Input Parameter or All Values⇒ Top 5
            - Define the number of rows to return  
            - ![](https://remnote-user-data.s3.amazonaws.com/Qa5eDbENiz2ECsHOj2cDo8gXrfYkPWKO_wVVJlTs6ZiVZQ_87GraVZhlBWkoYUc2zweQJUQSEu4G1X2udaO5wwdrEl3qL6nBMCYDoESUV7BrdWGaUOMtQMK2oUycTOK7.png)
            - The setting must be a rank value - for example, extract the data up to the rank 2), except for the  *Sum*  aggregation function where you set a cumulative value (for example, extract the data up to a cumulative Sales amount of 200).  
            - SAP HANA Cloud QRC 2/2024 introduces an additional capability: you can now set the Target Value setting to All Values. The rank node then sorts the value without eliminating any of them from the result set.
        - Offset >>>
            - It is possible to exclude a number of elements from the top of the sorted data set by defining an offset.
            - ![](https://remnote-user-data.s3.amazonaws.com/SPLgq63ugF21pJ9kNFW-qCRCsPBfwnI-_uK493NlOqRIx28poZiryBtTdtrisRTiwSLJ36K-CvtVkN5NLYTyy7AxQsW4UmAld8HKrNewoxPzn865kbBDu0dalcul_T8U.png)
        - Generate a rank column ⇒ A new column can be generated which provides the blank position
        - Partition Column >>>
            - Partition the source data set by one or several columns, before executing the rank computation  
            - Each row in the result will be related to an individual value or **combination of values of the columns **that are not included in the logical partition definition so in this case with our settings the result will display the top five products for each country based on the gross amount each row will be for one product
            -  *Dynamic Partition Element* 
                - If you choose, the columns listed in the Partition will be ignored if they are not requested by an upper node or top query. To follow the same example, you could return the top five total sales for each Country only, for each year only, just by excluding the column you do not need from your top query, and without redesigning your calculation view. 
        - Sort Column >>>
            - Within each partition the data is sorted by one or several columns
            - the aggregation functions Row, Rank, and Dense Rank will process the sort columns in the order they are listed
            - This helps manage identical values in the first sort column by using the subsequent sort columns to break ties.  
            - The  *Sum*  aggregation function only uses the first Sort Column.  
        - 
    - Aggregation Node >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/PpB1COn-6SGxlie-KDX_9xBSCVgqPPaUNt3kyIGDYvRKXZerMR-IfOzuVGmMmgg0OkMzwr2TKfewQqwnqeJNT7ELXsqIOnoFJHadOva2clFr9ku2u3_64KrVQGHU-_oG.png)
        - The purpose of an Aggregation node is to apply aggregate functions to measures based on one or several attributes. The node generates a result set that is grouped by the selected attribute columns and computes the selected measure with the specified function, such as SUM, MIN, AVG, and so on.
        - By default, the granularity of the aggregation is defined by the attribute columns that are mapped to the output of the aggregation node. In the example, you will get one aggregate row for each country/order pair showing two measures: the total amount of each order, and the biggest line item of each order.
        - If you want to get the total amount and biggest order amount at the country level, removing the Order column from the output in semantics is not enough because you will still get the biggest line item detail in each country.    
        - To correctly calculate both the **total amount** and the **largest order amount** at the country level in SAP HANA Cloud, you need to use **two aggregation nodes** in sequence. Here's why:
        - Example Data: #
        - | Country | OrderID | Amount |
        - |---------|---------|--------|
        - | US      | 100     | 50     |
        - | US      | 100     | 30     |←Line items for Order 100
        - | US      | 200     | 100    |
        - | UK      | 300     | 20     |
        - | UK      | 300     | 40     |←Line items for Order 300
        -  **Incorrect Approach (Single Aggregation Node)**
        - If you aggregate at the country level directly only by removing order from semantic output:
        - .
            ```
            ```sql
AGGREGATE BY Country
MEASURES:
    SUM(Amount) AS TotalAmount,
    MAX(Amount) AS LargestOrder
```
            ```
        - **Result**:
        - | Country | TotalAmount | LargestOrder |
        - |---------|-------------|--------------|
        - | US      | 180         | 100          |←MAX picks the largest line item (100), not the largest order total.
        - | UK      | 60          | 40           |←MAX picks the largest line item (40), not the largest order total.
        - **Issue**: The "largest order" is incorrectly calculated because it ignores the total of multi-line orders (e.g., Order 100 in the US has a total of `50 + 30 = 80`, but `MAX(Amount)` picks `100` from Order 200).
        -  **Correct Approach (Two Aggregation Nodes)**
        - Step 1: Aggregate by Country and OrderID #
        - First, calculate the **total amount per order**:
        - .
            ```
            ```sql
AGGREGATE BY Country, OrderID
MEASURES:
    SUM(Amount) AS TotalPerOrder
```
            ```
        - **Intermediate Result**:
        - | Country | OrderID | TotalPerOrder |
        - |---------|---------|---------------|
        - | US      | 100     | 80            |←Total for Order 100
        - | US      | 200     | 100           |
        - | UK      | 300     | 60            |←Total for Order 300
        - Step 2: Aggregate by Country #
        - Now calculate the **total amount** and **largest order** at the country level:
        - .
            ```
            ```sql
AGGREGATE BY Country
MEASURES:
    SUM(TotalPerOrder) AS TotalAmount,
    MAX(TotalPerOrder) AS LargestOrder
``` 
            ```
        - **Final Result**:
        - | Country | TotalAmount | LargestOrder |
        - |---------|-------------|--------------|
        - | US      | 180         | 100          |←Correctly picks the largest order total (100 for Order 200)
        - | UK      | 60          | 60           |←Correctly picks the largest order total (60 for Order 300)
        -  **Why Two Nodes Are Necessary**
        - 1. **First Node**: Groups data by `Country` and `OrderID` to calculate the total for each order (`TotalPerOrder`). This ensures multi-line orders are summed before comparing sizes.
        - 2. **Second Node**: Aggregates the results from the first node to compute the final `TotalAmount` (sum of all orders) and `LargestOrder` (max of `TotalPerOrder`).
        - Without the first node, `MAX(Amount)` would operate on individual line items, not the full order totals. The two-step process guarantees accuracy for both metrics.
        - > If you exclude an attribute from an upper node (in the same calculation view or another one that consumes it), or in a query executed on top of this view, by default these attributes are ignored. In addition, the calculation view aggregates the result set on the remaining attributes.  
        - In an  *Aggregation*  node, a calculated column is always computed AFTER the aggregate functions. If you need to calculate a column BEFORE aggregating the data from this column, you have to define the calculated column in another node, for example a  *Projection*  node, executed BEFORE the aggregation node in the calculation tree.  
        - Calculated columns >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/vtK2BoRBjAVPb1APVYhK6s7usZjzhHnQX01kiacvErZCAwrY6rp8G8zBdQPaXTC4kjF_MVLuXmf7h5tSsZJaHKfuMLyFvN0DDKL7d8fPbf_rX5YPDCNADFrCgKJeitHi.png)
    - Star Join node >>>
        - which is used to model a star schema, also defines one or several joins between a main data sources (fact "table") and the dimension calculation views.  
    - Union node
        - Standard union >>>
            - A union node can be implemented in a dimension, cube and cube with star join calculation views.
            - the SAP HANA Union node in calculation views is designed to work similarly to a `UNION ALL` operation.  
            - ![](https://remnote-user-data.s3.amazonaws.com/tiQewhA358N627shLdvJkTObUpmUHGnn5asNxyHFXyiQZIQpTGG0wmFCTUrxnvQorQ1T69un8SrqySikE4g-pY47nKuMgA9GPj8RwBheS12EsiaW3a-e-ZsoaHLiTJhi.png)
            - The source column does not have to have the same name as the target column. For example, a source column month could be mapped to a target column period. Although column names can differ between the source and target (like **month** to **period**), the mapped columns must have the same or compatible data types to ensure proper unioning.  
            - does not have to provide a mapping for each source column. In other words, there could be source columns that are left behind and play no part in the union. This scenario is useful when you want to combine measures from multiple sources into one column. As the data sources can provide an attribute that describes the type of measure (for example, Plan or Actual),
        - Union with constant >>>
            - Is where you provide a fixed value that is used to fill a target column where the data source cannot provide a mapped column. For example, you have a data source that provides the actual amount and you also have a second data source that provides the planned amount. You decide to avoid combining these measures into one column as they would lose meaning, because there is no attribute to describe the meaning. In this case, you would map the measure to a separate target column and provide a constant value ‘0’ to the target column for the missing measure on each side.  
            - Select the union node ⇒ go to mapping ⇒ Create Two output columns ⇒ gross_value_EMEA and gross_value_AMERICA
            - Right click the new columns and select manage mapping
            - Provide constant value let say 0
            - Now you can see gross_value_EMEA is 0 for America region similarly gross_value_AMERICA is 0 for EMEA region
            - ![](https://remnote-user-data.s3.amazonaws.com/mMDKbWggE83Vc1f46qjX_vRA6P3VXfxvwImBpZEeiO6ErehSl8SpgUDJNcsqXqoGmdEwBLlC8rZ-TxoPjUd6yWagrK7nAqZslUkTcCSaHZBxGVKAjQoWoCBmAT4cuiAQ.png)
        - Empty Union Behavior >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/MkbkN8TWjbaDuYuqdfIDpeD5Jz6yQY4W0OaIrxCAHUelSSMox3Rm5PDIgd3ki7FUc0M2EM5ZSgvOke2qYjpkvwjNE19NEyV3HNaSszrB4ekSPCjRBsEC-cf1OqCiDluL.png)
            - To illustrate this feature, imagine you have a data source, A, that contains the columns  *Product*  and  *Product Group*  and another data source, B, that contains only the column  *Product* . If a query requests only the column  *Product Group* , how does data source B respond when it does not have this column?
            - The answer depends on the setting in the property,  *Empty Union Behavior* , which is set for each data source. The default behavior, as you might expect, is to provide  *No Row*  for data source B.
            - In that case, you should proceed as follows:  
            - Add a constant column to the union output.
            - Provide a constant value for each data source with a suitable value that help you identify each source.
            - Change the property  *Empty Union Behavior*  to  *Row with Constants* .
            - Define a constant value for the Product Group column for Data Source B ⇒ [Union with constant](SAP%20HANA/Nodes/Union%20node/Union%20with%20constant.md)
            - The results will then contain multiple rows from data source A, and also a single row with the constant value that you defined for Product Group column from data source B and nulls will fill the empty columns that it could not provide.  
        - Union All or Union Distinct >>>
            - A Union node generates a list of all values from the input data sources, even if values are repeated. For example, if two data sources both include the same customer, then the customer will appear twice in the output data set.
            - This is the equivalent of UNION ALL in SQL and is desirable in many cases.
            - If repeating values are not required, you should include an Aggregation node on top of the Union node to aggregate the attributes. We normally associate aggregation Behavior with measures, such as SUM, AVE, and so on, but aggregation can also be performed on attributes. The effect of aggregating attributes is to simply produce a distinct list of values.
            - This is the equivalent of UNION DISTINCT (or just UNION) in SQL.
        - Constant Pruning/Explicit pruning >>>
            - For each data source in a union node, you can define an additional column and fill it with a constant value to explicitly tag the meaning of the data source
            - If a query filter - where clause - does not match the constant value for a specific source of a union, then the source is ignored. This is called explicit pruning. Explicit pruning helps performance by avoiding access to data sources that are not needed by the query.
            - ![](https://remnote-user-data.s3.amazonaws.com/fb510AMn6lD2HNRFhKvGE4WYK9U4yxe10dBH_LCDOI8JBVYp1BwE9-3RWzRkkig-jEVIe4gjnqIlrLdJf3VFNkCFB0VopCVTlN2WgH7Q7utB4oFvcgw-1bfftTpu5MRp.png)
            - the union node has a new column defined in the union node. To assign the constant value, we use the  *Manage Mapping*  pane and enter a constant value for the new column per data source. In our case, the new column is named  *temperature* . In our case, either the value  *old*  or  *new*  is assigned. This is a simple way to prune data sources in unions where the rules are simple and rarely change.  
            - Although a union node usually has just one constant column, you can create and map multiple constant columns and use AND/OR filter conditions in your query. However, a key point is that if you do not explicitly refer to the constant column in the sending query, then the data source is always included in the union and is not pruned.  
        - Pruning configuration table >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/e6-XgSHCmusHoEq7tmY7vay8il0mKk67zwpYKGk6xWQo7UGTorJrredODb3Qxg5EtD9JAqT8EOq2ZvCcxI3GQwe12QzFQi7JkTZb4U-G8B6Zxc4YtbgoDDA_oDDFPa9f.png)
            - configuration table sits outside the calculation view and allows you to describe in greater detail the rules that determine when each source of a union is valid.
            - approach does not need to provide constant values in the union node for each data source inside the calculation view. With the table, you can define multiple single values per column that are treated with ‘OR’ logic. You can also use the greater than and less than operators. You can as well define values across different columns in a rule. Values across columns are treated with ‘AND’ logic.
            - eg COUNTRY = 'US' OR COUNTRY = 'UK' AND STATE = 'NY'
            - **Go to Semantic ⇒ view properties ⇒ advanced ⇒ Create Pruning configuration table** 
            - Table name
            - View name ⇒ Union_prune
            - Input ⇒ projection_1
            - Column ⇒ COUNTRY
            - Operators ⇒ equal
            - Low Value ⇒ any value DE or US
            - If you execute the SQL with where COUNTRY = 'DE' , Then union is pruned
            - The possible operators are =, <, >, <=, >= and BETWEEN.
            - If several pruning conditions exist for the same column of a view, they are combined with OR.
            - On different columns, the resulting conditions are combined with AND.
            - For example, you could define that a source to a union should only be accessed if the query is requesting the column YEAR = 2008 OR YEAR = 2010 OR YEAR between 2012 – 2014 AND column STATUS = ‘A’ OR STATUS = ‘X’.
            - This means that if the requested data is 2008 and status ‘C’, then the source is ignored. If the request is for 2013 and status ‘A’, then the source is valid.
        - Column-based Pruning >>>
            - Column-based pruning is another approach to eliminating data sources within unions that are not required by queries. However, unlike the other approaches, which are based on checking column values, column-based pruning looks at whether specific columns have been requested by a query and if those columns are defined as important to a result set.
            - ![](https://remnote-user-data.s3.amazonaws.com/P3m3uFZp3-NDv2ASAEgG0yTQ7cNSxm81F2JhOGwzGFC3aVH9Ch4T9WFWd-7Q3E0_qOSGLPTwnW2g0kgW0tKQ44obqLvxMm1mV3sTMIg_CZXNOHp3rzFK8SjBJ1mU0NnS.png)
            - In our graphic, we see that both data sources could provide the  *SALES*  column, but only one data source  *salesOrder2019*  provides the column to the output.
            - We also see that the  *SALES*  column has been defined as a  *Focus Column* , which means that if a query requests this column and the data source cannot provide it then we are not interested in that data source, even if other columns (YEAR and so on) could be provided.  
            - It is possible to define multiple  *Focus Columns*  and they can be attributes or measures or a mix of both.  
            - ![](https://remnote-user-data.s3.amazonaws.com/-JZdAmV2OsKvj3ZLX5Ol06umkbpEsMp-sG9_iQNMCf63qhqWFeQgnj-qZfkP5J0G5OhNMk3lgNJ5bP7qQRHlS2eQExlNQpiwlsRXCSwTMc6NKZ7w1eDeCngUQzY14w59.png)
            - Before setting focus columns
            - ![](https://remnote-user-data.s3.amazonaws.com/UZo2aTY0CuLRSfhXMCospHEpq2dWu4T3PWn_vhxQ0j1F3zZD9eJMTFNRrV7fe44PAorXlNfvsZeVkZQ5I8YQn4u8MG-Ffdmm7PpSK46dq6lxzjXMF1Gv8PHGaDBppfah.png)
            - After setting focus column we don't see the 2020 records because even though the data source for 2020 provided Year and product data we completely neglected the data source for 2020
    - Table Function Node >>>
        - This node is specially dedicated to the handling of the different types of mapping of the input parameters of a table function.  
        - ![](https://remnote-user-data.s3.amazonaws.com/2e0psfT5OBNUVPqGNbREYOhsMOh8frlVoGeNk2qUTnXprsz8CjA7g8kYaK_3FFRBfJBFWFJd2llFdS6-ISXQMaWyhjkRtmSD-pzeCztjLefsMwxpV_SpV_39b2RUxHZv.png)
        - There are three types of input mapping available in the  *Table Function*  node. They are as follows:
        - **Data Source Column** 
        - First, add a data source to the Table Function node. Then, map one of the data source columns to one or more table function input parameters.
        - **Input Parameter** 
        - Choose an input parameter from the calculation view and map it to one or more table function input parameters. This could be useful when you want a user to manually enter a value at runtime.
        - **Constant** 
        - Manually define a single, fixed value, and map it to one or more table function input parameters. This is really only useful when the input value rarely/never changes.
    - Window function nodes
- Optimization Theory
    - Monitoring unfolding >>>
        - Use Analyze ⇒ Explain plan in SQL console
    - Transparent Filter >>>
        - Again used in aggregation node to get accurate results
        - Store A has product hat and sales 2000
        - Store B has product hat and sales 1000
        - If we request for number of unique product from store A and B
        - We get result 2 which is incorrect. The result should be 1
        - Select the Store Column in mapping section and enable Transparent Filter. 
        - The result is one which is correct.
    - CHECK_ANALYTICAL_MODEL >>>
        - tips to improve the optimization of object
        - **Procedure with parameter ** 
        - 'list'
        - schema name
        - object name
        - out
        - call CHECK_ANALYTICAL_MODEL('list','schema','object',?)
    - Optimize Join
        - Optimize Join Columns Option >>>
            - In SAP HANA is a configuration setting used in calculation views to improve performance when joins are executed on specific columns. When this option is enabled for a join column, it provides the SAP HANA engine with additional metadata about the column, such as its usage frequency or distribution. This helps the engine optimize query execution plans.  
            - **Steps:** 
            - Open Calculation View Editor  
            - Select Join Node  
            - Select the join column from one of the tables in the join.
            - Right-click the column and look for the option `Optimize Join Column` or a similar setting in the column properties.
            - By default the joint field like employee_ID, Adress_ID is included in the aggregation even if not requested by the query
            - When the Optimize Join Columns option is active, pruning of join columns between two data sources, A and B, occurs when all four following conditions are met:
            - The cardinality on B side (the side of the join partner from which no column is requested) is ..1
            - The join column from A is NOT requested by the query.
            - Only columns from one join partner, A, are requested.
            - The join type is Referential, Outer or Text
            - No measures at all are requested or only measure with count distinct aggregation
        - Greedy Join Pruning >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/pDTYvb3nmAX5b62QCwkzR3F6PFCt1omjDQAQ88jnaiR68rjUM6YyX_jwlJrBU_uVwbmqaP5f64f5wxvFejFb5Wwy27l8uLL-qUe-55dd3rGYt-aZbHvzzi_qWyQJZXsc.png)
            - If you want to prune the joints at any cost irrespective of the joint type and cardinality But force Pruning by left, right and both
            - It allows the SQL optimizer to prune a joined source if it is not queried at all (no column requested), regardless of the cardinalities and join type settings.  
            - When greedy pruning is enabled, it does not prevent other join pruning mechanisms. Therefore, even if a condition is not met for greedy join pruning (for example, the  *Greedy Pruning*  is  *Left*  but you request columns from the left table of the join), join pruning could still happen because the cardinalities, join type and so on, allow it.  
            - The results might be affected you need to thoroughly test it
            - Select the join link
            - Select the join type ⇒ inner
            - Select cardinality 
            - Enable Greedy pruning ⇒ both sides
            - **Another way which takes high priority** 
            - Select the semantics node
            - Select view properties
            - Select advanced tab
            - Select Execution hint
            - Greedy Pruning both sides
            - ![](https://remnote-user-data.s3.amazonaws.com/tjqR6HhvZG0knfLT0gcatFW_M9CKHPsOeIM_7dG-v6gaGYVrhFSKxJ2pACLdQ_lLd22vMaKRHCp2Wh2VzJx8u_OnwxRwwkkuUGI_I-wdnAENE9ZMkbsR7x2AvDCftN0g.png)
            - [Greedy Pruning at Different Levels](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/optimizing-joins_c2a47077-c573-4ca3-95e2-be564528354d)

    - Parallelization >>>
        - SAP Hana Cloud optimizes performance through automatic parallelization
        - **Forced Parallelization** 
        - Use "Partition local Execution" flag in calculation views select the table node in mapping tab
        - Data Source must be a table and not a view
        - Only one parallelization block per query
        - Start with table defined node - Set the flag for Partition local Execution
        - Perform aggregation and calculation in the middle node
        - End with Union node, Set the flag for Partition local Execution, Indicating termination of parallelism, Note Union node should have only one data source
    - Performance analysis >>>
        - Button in Graphical calculation view editor
        - In each node you will get performance analysis tab
        - Identify settings leading to poor performance during calculation view development
        - React to design time warnings and information to optimise performance
        - Access detailed information on performance analysis tab for all nodes except Symantec node
        - Information Provided
            - Table types (row or column)
            - Joint types (inner or outer) and join cardinality
            - Table partitioning details
            - Clear warnings for very large table
            - missing or misaligned cardinalities
            - Consider partitioning and applying filters for large tables to process only necessary data
            - 
    - Plan and SQL Analyzer >>>
        - Provides brief info about operators and related information
        - Analyse performance of SQL queries, including those generated by calculation views
        - gain insights into query execution type, step duration, bottlenecks, parallelization, and processing engines
        - Provide views to analyse SQL queries. Drill down into query execution, timeline and table usage
        - Requires installation of SAP Hana Performance Tools Extension in sap business application studio
        - Select the .hdbcalculationview file and open SQL editor
        - Select Hana SQL analyzer tab in the left pane
        - Click on Analyze and select **Generate SQL Analyzer file** 
        - The generated file is available in the main Hana instance
        - SAP Hana cloud central ⇒ Database explorer ⇒ DBADMIN
        - Expand Database Diagnostic Files ⇒ Expand host ⇒ other ⇒ download the generated file
        - Import that in BAS ⇒ open in SQL analyzer
    - Snapshots >>>
        - Store calculation view result for history or performance reason
        - Take snapshots of data at different point of time
        - Semantics ⇒ View properties ⇒ Snapshots
        - New query name ⇒ create snapshot after deployment (will create snapshot table) ⇒ keep snapshot during redeployment 
        - Deploy the snapshot view individually to generate the snapshot table
        - Deploy the calculation view to generate the snapshot table
        - You can also find the procedures generated for the snapshot table (Insert, Truncate, drop, delete)
        - execute snapshot manually or scheduled for regular updates
        - Query snapshot table for historical data while using calculation view for real time data
    - Best Practices for Modeling >>>
        - Always maintain accurate cardinality
        - Try to reduce the number of join columns
        - Avoid joining on calculated columns
        - Avoid type conversions at runtime
        - Avoid mixing serious CDS views with calculation views, because both are performed on the different engine
        - Break down large models into individual calculation views so that they are easier to understand and also allow you to define reusable components, thus avoiding redundancy and duplicated maintenance as a consequence.
        - For performance issues, we generally recommend that you create dedicated calculation views or tables to generate the list of help values for variables and input parameters.
        - Setting up and maintaining replication is possible by executing SQL statements in the SQL Console. But this approach means that the objects that are created in the database will be owned by your user ID and this can lead to problems when others need to take over the objects. Also, transportation of the database objects isn’t possible.
        - Best practice is to develop the objects using source files and then build/deploy to generate the database artifacts in an HDI container.
    - Column Pruning >>>
        - Do not map columns that are not needed
        - Avoid adding too many columns which can lead to large granular data sets when only aggregated data is needed
    - Switch Calculation View Expressions to SQL >>>
        - For SAP HANA on-premise, it is possible to write expression using either plain SQL or the native HANA language called column engine language. SAP recommends that you only use SQL and not column engine language to ensure best performance (Column Engine language limits query unfolding).  
        - When you build calculation view expressions on SAP HANA Cloud, you can only choose SQL expression language. Column engine language is not available in SAP HANA Cloud.  
        - Calculation views that were imported to SAP HANA Cloud from SAP HANA on-premise and include column engine language expressions will still run, but you should change the expression language to SQL from the column engine, to achieve better performance. When you select SQL language, the language selector is then grayed out so that you cannot return to Column Engine language.
        - You should be able to recreate column engine expressions to plain SQL. For example, the concatenate operator + is replaced with the plain SQL operator || or you can use the CASE keyword instead of If...Then.
        - ![](https://remnote-user-data.s3.amazonaws.com/8riKPlsybmjVNIY2eD2-rolmP6dp1PPYUryuArOY9Q67GC6AWE59JZXfIG08PZs4ZiljPJSjrVKFplK29xyhoOCyfKbYcOH8BCoGhP90pUNpU-ZQpcGlm8YkmEYslVer.png)
    - Optimal Aggregations >>>
        - Always reduce the data set by introducing aggregations early in the modelling stack.
        - If you consume a calculation view that produces attributes, do not change them to measures and vice versa.
        - Choose a cube with star join to build dimensional OLAP models. Do not try to create OLPA models using standard join nodes which creates a relational model. These may not perform as well.
    - Performance Analysis >>>
        - You switch your calculation view to Performance Analysis mode by pressing the button that resembles a speedometer in the toolbar. When you do this, the Performance Analysis tab appears for all nodes except the semantics node. Choose this tab to view detailed information about your calculation view, in particular the settings and values that can have a big impact on performance.
        - Examples of the information presented on the Performance Analysis mode tab include the following:
        - The type of tables used (row or column)
        - Join types used (inner, outer, and so on)
        - Join cardinality of data sources selected (n:m and so on)
        - Whether tables are partitioned, and also how the partitions are defined, and how many records each partition contains
        - The size of tables with clear warnings highlighting the very large tables
        - Missing cardinalities or cardinalities that do not align with the current data set
        - Joins based on calculated columns
        - Restricted columns based on calculated columns
        - This information enables you to make good decisions that supports high performance. For example, if you observe that a table you are consuming is extremely large, you might want to think about adding some partitions and then apply filters so that you process only the partitions that contain data you need.
    - Controlling Parallelization in a Data Flow >>>
        - SAP HANA Cloud always attempts to automatically apply parallelization to queries in order to optimize performance
        - Within a calculation view, it is possible to force parallelization of data processing by setting a flag Partition Local Execution to mark the start and also the end of the section of a data flow where parallelization is required. The reason you do this is to improve the performance of a calculation view by generating multiple processing threads at specific positions in the data flow where performance bottlenecks can occur.
        - The parallelization block begins with a calculation view node that is able to define a table as a data source. It is not possible to use any other type of data source such as a function or a view.  
        - In the Properties of the chosen start node, a flag Partition Local Execution is set to signal that multiple threads should be generated from this point onwards. It is possible to define a source column as a partitioning value. This means that a partition is created for each distinct value of the selected column. For example, if the column chosen was COUNTRY, then a processing thread is created for each country. Of course, it makes sense to look for partitioning columns where the data can be evenly distributed. The partitioning column is optional. If it is not selected, then the partitioning defined for the table is used.
        - To end the parallelization block, you use a union node. However, unlike a regular union node that would always have at least two data sources, a union used to terminate the parallel processing block is fed only from one data source. The union node combines the multiple generated threads but the multiple inputs are not visible in the graphical editor and so the union node appears to have only one source from the node below.
        - In the Properties of the union node, a flag Partition Local Execution is set to signal the ending of the parallelization block.
        - Only one parallelization block can be defined per query. This means that you cannot stop and the start another block either in the same calculation view or across calculation views in the complete stack. 
        - You cannot nest parallelization blocks, for example, start a parallelization block then start another one inside the original parallelization block.  
        - It is possible to create multiple start nodes within a single parallelization block by choosing different partitioning columns for each start node. If you create multiple start nodes, then all threads that were generated are combined in a single ending union node.
        - To check the partitioning of the data, you can define a calculated column within the parallelization block with the simple column engine expression partitionid(). In the resulting data set, you will then see a partition number generated for each processing thread.  
    - Table Partitioning >>>
        - it is also possible to subdivide the rows of column tables into separate blocks of data.
        - Partitioning only applies to column store tables and not row store tables. This is why it is recommended that you define tables as a column store that will be used for querying large data sets. Tables as a column store that will be used for querying large data sets.
        - Generating partitions is usually the responsibility of the SAP HANA Cloud administrator. There are many decisions to be made relating to partitions including the type of partition. For example, hash, round robin, or range. Partitions can also include sub-partitions. The administrator uses monitoring tools to observe the performance of partitions and makes adjustments. What the modeler will need to do is to provide the information relating to the use of the data - for example, how queries will access the data so that the partitions can be defined optimally.
    - Caching View >>>
        - Reduce system load and speedup query execution by caching complex calculation view results
        - Decreased CPU and memory consumption
        - Applied to highly processed and reduced data example aggregations and filtering
        - Caching shouldn't be applied to raw data but to data that has been highly processed and reduced through aggregations and filtering. In practice, this means applying caching to the top-most nodes of a calculation view.
        - Cache can only be used for calculation views that don't check for analytic privileges. This means analytic privileges should be defined on the top-most view only, in a calculation view stack. The optimal design would be to define the cache setting at the highest calculation view possible in the stack, but not at the very top where analytic privileges are checked. This means that the user privileges are always checked against the cached data, if the cache is useable.
        - Firstly, calculation view caching can be defined at the column level. This means queries that use only a sub-set of the cached columns can be served by the cache. It's recommended to cache only the columns that are frequently requested in order to reduce memory consumption and speed up cache refresh.
        - If you don't specify any columns in the cache settings, then all columns are cached.
        - The cache size can be further reduced by defining filters in the cache settings. Queries that use either exactly the cache filters, or a subset of the filters, are served by the cache. Cache is only used if the query filter matches exactly the filter that is defined for the cache, or reduces the data further.
        - It's currently not possible to specify that cache should be invalidated if new data is loaded to the underlying tables. This feature is expected to come later. For now, we just have a time-based expiry (in minutes).
        - **Semantics ⇒ Static cache** 
        - Firstly, the basic setting Enable Cache must be set in the calculation view to be cached.
        - Then, the query must be able to be fully-unfolded. This means that the entire query must be fully translatable into a plain SQL statement and not require the calculation of interim results. You can use the Explain Plan feature of the SQL Analyzer to check if unfolding can occur.
        - There must be no calculations that depend on a specific level of calculated granularity as the cache is stored at the level of granularity requested by the first query. This may not align to the aggregation level required by subsequent queries and could cause incorrect results.
        - Hint is added to your SQL Query:
        - SELECT ...WITH HINT (RESULT_CACHE)
        - **A database hint is used in the top-most view, which consumes to-be-cached view** 
        - View A: A complex calculation view that processes raw data.
        - View B: Another calculation view that aggregates and filters data from View A.
        - View C: The top-most view that uses the results from View B.
        - Enable caching for View B to store its results.
        - Use a database hint in View C to utilize the cached results of View B.
        - **Configuration parameter is set:** 
        - indexserver.ini -> [result_cache] -> enabled
    - Calculation View Snapshots >>>
        - To store historical values. Or you could need performance but not on real-time data, just on a latest snapshot of the system taken in the morning, for example.
        - First, you need to deploy your calculation view to generate the snapshot table.
        - Procedures are also generated to truncate the snapshot table or to trigger insertion of data.
        - You can execute the snapshot manually or schedule it to get new data regularly.
        - You can then query the snapshot table to get historical data.
        - You can always continue using the calculation view as usual to get real-time data.
        - **Steps**
        - Open your calculation view
        - Go to the view properties tab and select the snapshot tab
        - Add a new query
        - Define the SELECT statement for the query
        - Give me a name to your query
        - Decide if you want to provision the table or deployment
        - Decided which mode you want the procedures to be created in
        - Decide if you want an interface view
        - **INSERT** 
        - The INSERT procedure can be used at any time to execute the query and store the results into the snapshot table.
        - If you call the procedure to insert data twice, without truncating data, you'll have duplicates. So first call TRUNCATE Procedure and then execute INSERT
        - If you don't want to delete data between two deployments, you can use the Keep Snapshot During Re-deployment option.
        - ![](https://remnote-user-data.s3.amazonaws.com/ZErhzV1jcQgHyEBAJkj__DQj3dYHFV6Q_WXD-B6HagSm457-eQDll-smDYJpoY_5GWoYAALtIhuFAEE9ZuncRYPrBQ4ZvCg3_zE_8R7IWSoA3h51TMOQSteEuGtvJo-y.png)
        - Interface View >>>
            - Additional calculation view is generated which is a union of snapshot and original calculation view
            - Additional Column called 'SOURCE' is generated in the Interface View which has constant values 'BASE' and 'SNAPSHOT'
            - Additional parameter called 'I_SOURCE' is added to the interface view which is a static list of filter values
            - Additional Filter expression is added to filter the source
    - MDS Cubes for a Calculation View >>>
        - store pre-processed data in a format that can serve all these complex drill-down queries and allows to quickly answer these most frequently required drilldown situations
        - ![](https://remnote-user-data.s3.amazonaws.com/9iA31hKBKsZplyDDkNbS3uaCqSWNEaR0ck33V2sW7wDN-m8hYg-Wxyzz2WxfKtK-GVcC-oMy5zuV7iKVM5p1jdBpYlGMYrktMUgQgefRU_0XfCcATq4xTE7BI6-5Im_E.png)
        - You can define multiple MDS Cubes for the same calculation view. Deployment of the calculation view builds the view and generates all MDS cubes that are defined for it, initially without data.  
        - the data is not stored as a flat datastructure, but in a multidimensional, star-join-like structure that is optimized for tailored data access.
        - When the source data changes after populating the MDS cube, the loaded data is not automatically updated.
        - Analytic privilege settings of a calculation view are applied automatically also to all MDS Cubes that apply to within the calculation view  
        - An API is available to manage and maintain MDS Cubes. This API implements an extended CRUD (Create, Read, Update, Delete) service, which can be used by both applications using MDS Cube and directly by the customer.  
        - .
            ```
            CALL MANAGE_MDS_CUBE('
{
   "Cube": {
      "Command": "Reload",
      "Target": {
         "DataSource": {
            "SchemaName": "LIQUID_HORIZON",
            "ObjectName": "model.mdscube::LIQUID_HORIZON_LTD/mdscube/LIQUID_HORIZON_MDS_CUBE"
         }
      }
}',?);
            ```
        - For monitoring, check the _SYS_EPM.MDS_METADATA table  
        - **Limitations** 
        - MDS Cubes cannot contain calculated measures. Instead, you can generate the calculation in SAC.
        - MDS Cubes support variables, but no input parameters
        - Joins with two or more join condition fields are not supported.
        - VARBINARY columns can not be used.
        - **Authorizations** 
        - To load or delete an MDS cube, you need the EXECUTE privilege for procedure MANAGE_MDS_CUBE. You can grant it as shown in the code snippet:  
        - .
            ```
            GRANT EXECUTE ON MANAGE_MDS_CUBE TO <DB_USER>;
            ```
        - Additionally, you need privilege CREATE ANY for the HDI schema in which the Calculation view of the MDS Cube resides. Moreover, you must have SELECT privileges for the data source of the MDS Cube.  
    - Query Unfolding and folding >>>
        - Query unfolding is when SAP HANA ignores the pre-designed structure of the calculation view and then runs the query directly on those raw tables instead of using the calculation view’s built-in logic.  
        - A benefit of unfolding to SQL is that only the SQL engine is needed and no other SAP HANA engine is called, such as the Calculation Engine or other SAP HANA Cloud engines such as Spatial or Graph
        - ![](https://remnote-user-data.s3.amazonaws.com/zwPgaxQaznqQH2ag_Jq_xjsxOFJtzp_Sk3hJW-3fg2lD7FpL5XIbTwcZ6taBrO6SpeKVEFvKS6kcMtqjerI5cReUFVJmiRcMDo9_xIEllf-hGg5GHFYaS2LQ9c0tr7B7.png)
        - If a  *column view*  is used as a data source instead of a table, then you know this is not an unfolded query as the result has been materialized as an interim view and not read directly from a table. A completely unfolded query only accesses tables.  
        - The calculation view is designed to show a summary, like “how many activities each student does.” But suppose you run a query asking for something very specific, like “all students with a particular activity on a specific date,” and the calculation view doesn’t have that info pre-organized. SAP HANA might **unfold** the query. It goes straight to the raw **Students** and **Enrichment** tables, joins them (e.g., matching STUDENTS_ID = STU_ID), and filters the data step by step.  
        - Query folding is when SAP HANA uses its pre-designed, optimized structure to get the results. It doesn’t break things down to the raw tables—it trusts the “recipe” already built into the view.  
        - Using the same calculation view with **Students** and **Enrichment** tables, let’s say you want to know “how many activities each student does.” The calculation view might already have this summary pre-calculated or optimized. When you run this query, SAP HANA **folds** it—meaning it stays within the calculation view (e.g., one called exampleNoKAnonymity) and quickly gives you the answer without touching the raw tables.  
    - 
- Security
    - CREATE USER AND ROLE WITH DBADMIN >>>
        - DBADMIN
            - Admin user used for creating another user, roles and other super admin activities
            - One user cannot assign privileges to the same user even admin cannot assign own privilages
        - One way of creating a new user is going to the SAP Hana Central ⇒ select the Hana instance ⇒ open sap Hana cockpit ⇒ Select security and user management tab ⇒ user management
        - .
            ```
            //creating a new user
CREATE USER FASHION_ADMIN PASSWORD <> NO FORCE_FIRST_PASSWORD_CHANGE SET USERGROUP DEFAULT;
//creating a role
CREATE ROLE "<>"
// the user having this role can grant access to schema EFASHION to other users
GRANT SELECT, SELECT METADATA ON SCHEMA EFASHION TO "rolename" WITH GRANT OPTION
GRANT "rolename" to FASHION_ADMIN with ADMIN OPTION 
 
            ```
    - Make calculation view a secured view >>>
        - Select semantics node ⇒ view properties ⇒ general ⇒ [Property] Apply privileges ⇒ SQL analytical privileges
        - Deploy the enabled calculation view
    - Create analytical privilege (.hdbanalyticalprivilege) >>>
        - In SAP BAS, Create a new SAP Hana DB artifact
        - Click on Add Secured Models
        - Choose the calculation view
        - Click on add in ASSOCIATED ATTRIBUTE RESTRICTIONS
        - Select Attribute, Which is Field of calculation view
        - Click on Add Restriction
        - Give Fixed Value = US
    - Create Role (.hdbrole) >>>
        - Create a new SAP Hana Database Artifact
        - Click object privilege tab
        - Select Object name ⇒ calculation view
        - Select Object type ⇒ VIEW
        - Select Privileges ⇒ SELECT
        - Select privilege with grant option if you want that user to grant privilege to other users
        - Click analytical privilege tab
        - Select privilege name ⇒ Analytical privilege name
        - Click on define schema reference
        - Deploy the entire project
        - Object Privileges ⇒ Control the access to the database objects defined in your container such as tables views procedures
        - Schema Privileges ⇒ Control the access to the entire schema usually granted by the technical user configured in the user provided service ( for external schema access )
        - System Privileges ⇒ Privileges related to the database operations such as user management and backups
    - User Groups >>>
        - Allow related users to be managed together supporting efficient user management
        - Each user group can have one or more dedicated group administrators
        - Group administrators can be assigned specific tasks for managing users within the respective user groups
        - User groups can have groups specific configurations such as password policy settings or client connection restrictions
        - Users with a system privilege CREATE USERGROUP can create user groups
        - Initially the database administrator (DBADMIN) has the privilege and can enable other users to create user groups by granting them the CREATE USERGROUP privilege
        - User group administrators can designate dedicated administrators for specific user groups by granting the object privilege USERGROUP OPERATOR on the user group
        - Group administrators can add new users to a user group using the CREATE USER statement or the cloud cockpit
        - To move a user from one group to another a user authorized for both user groups can use the ALTER USER statement to set the user's user group to the new user group automatically removing the user from the original user group you can also use cloud cockpit
    - Authorizations
        - LDAP (Lightweight Directory Access protocol) >>>
            - Serves as the external authentication provider responsible for handling user authentication
            - Users can authenticate themselves in sap Hana directly from ODBC or JDBC database clients using a password stored in an LDAP directory server
            - This authentication method is applicable when the local sap Hana authentication for users has been disabled
        - [Security Assertion Markup Language (SAML)]()
        - JWT >>>
            - A JSON web token can be utilized for user authentication when accessing sap Hana directly from ODBC or JDBC database clients 
            - SAP Hana database validates the JWT to verify its authenticity and extracts user identity details
            - They extracted user identity is then mapped to the identity of an internal database user for access authorization
            - External authentication provider must still have a known database user in sap Hana, And the external identity is linked to an internal database user for authentication purpose
            - SSO capable
        - X.509 >>>
            - User accessing sap Hana directly from ODBC or JDBC clients can be authenticated using client certificates signed by a trusted certification authority
            - During authentication, the client presents a digital certificate, which contains the user's identity and is signed by a trusted CA
            - SAP Hana verifies the certificate authenticity by validating the CA signature
            - Upon successful validation, the users identity is extracted from the certificate and mapped to the identity of an internal database user for authorization purpose
            - SSO Capable
    - Privileges
        - System privileges >>>
            - In SAP Hana central ⇒ General system settings. They are primarily used for administrative purpose such as
            - Creating schemas
            - Creating and changing users and roles
            - Monitoring and tracing system activities
            - Essential for performing administrative tasks and managing the Sap Hana database
        - Object privileges >>>
            - Depending on the object type different actions can be authorized such as select, create any, alter, and drop
            - You can select Tables and Views as object
            - Schema privileges are object privileges granting access to and modification of schemas and contained objects
            - Source privileges are object privileges used to control access to and modification of remote data sources connected through sap hana smart data access
            - Object, schema, source privileges are essential for controlling access to specific data and database objects ensuring data security and integrity in SAP Hanna
        - Analytical Privilege >>>
            - Analytic privileges are used to enable data access in calculation views by filtering the data based on the values of one or more attributes.
            - They are based on certain values or combination of values and are evaluated during query processing
            - Enabling fine-grained data access permissions based on user roles or attributes
            - Hierarchy Node in an Attribute Restriction >>>
                - If a DIMENSION calculation view contains a parent-child hierarchy and the hierarchy is enabled for SQL access, it's also possible to define the restriction on a hierarchy node.
                - Add the calculation view  
                - Create an  *Attribute*  restriction type.
                - From the section Hierarchical Privilege select one of the available hierarchies..
                - Choose a hierarchy node value.
            - If two analytic privileges (or more) are defined to apply to the same view, SAP HANA combines the corresponding conditions with a logical OR.
            - **Steps**
            -  Create a source file with the extension  *.hdbanalyticprivilege* .
            -  Assign the calculation views that you want to secure with this analytic privilege.
            -  Choose the type of restrictions that you want to use and define the restrictions.
            - ![](https://remnote-user-data.s3.amazonaws.com/f8_St12-fJqRNET2Vcii6Ro62xnBkCQctvo7L0boCUSeE4MYsT4yGD3Q8iQc5eXtcB3cndV3ciY_fAWbn5VGgQ8bJnZ3u_GRvLVRum_b7nG8-WbOj8goAl4Qh0bU7sRX.png)
            - Attribute : With the restriction editor, select one or several attributes from the secured views. For each of them, define restrictions.
            - SQL Expression  : ("REGION"=’EMEA’ AND "YEAR"=’2015’)  
            - Dynamic Restriction Type >>>
                - Use a procedure to derive a dynamic SQL expression to restrict the data set. This expression must be similar to a WHERE clause in a select statement.
                - For example, a procedure could return ("COUNTRY"='US') for  *User1*  and ("COUNTRY"='UK' OR "COUNTRY"='FR') for  *User2* .  
                - When a user executes a calculation view that has an analytic privilege referencing a procedure, SAP HANA automatically runs that procedure in the context of the logged-in user built-in functions CURRENT_USER  
                - .
                    ```
                    lv_user := CURRENT_USER;
                    ```
                - Procedure must be read-only
                - Security mode must be  *DEFINER* 
                - No input parameters
                - Only one scalar output parameter of type VARCHAR(256) or NVARCHAR(256)  
            -  Set the secured calculation views to check analytic privileges.
            - ![](https://remnote-user-data.s3.amazonaws.com/5kF0HL2rYOAQm7pv7p8WZ6G27QE40JlHY9RMHLcibYJNXRWzxjbR4AeIhtf8uw7v__H4emfbZ9CoMMWVYV-IWEQqeNOJgFwnTDePpRD37xnoLRr7IeFiC_-IC-eZTGJB.png)
            -  Deploy the analytic privilege.
            - create a design-time role - [Create Role (.hdbrole)](SAP%20HANA/Security/Create%20Role%20(.hdbrole).md) and grant the new analytic privilege to this role.  
            -  Assign the role to a user 
            - .
                ```
                grant "SIMPLE_PROJECT_HDI_DB_1"."HC::ROLE_US_MANAGERS" TO username
                ```
        - Nested Calculation Views – Data Access Security Principles >>>
            - The key rules that govern the access to data are as follows:
            - Object privileges
            - There's no need to grant SELECT privileges on the underlying views or tables. The end user only needs to be granted SELECT privileges on the top column view of the view hierarchy.
            - Analytic Privileges
            - The analytic privileges logic is applied through all the view hierarchy.
            - Whenever the view hierarchy contains at least one view that is checked for analytic privileges but for which the end user has no analytic privilege, no data is retrieved (not authorized).
            - > Note that the end user always needs an explicit SELECT privilege on a calculation view to be able to query its data. That is, granting an Analytic Privilege to this user doesn't also grant an implicit SELECT authorization on the views that this Analytic Privilege secures.
    - SQL Security
        - DEFINER >>>
            - The procedure executes with the privileges of the user who created the procedure.
            - **Use Case**: Useful for enforcing consistent permissions, as the invoker's permissions do not affect execution.
            - **Default Behavior**: If not specified, SQL procedures default to DEFINER mode.
            - Scenario 1: **DEFINER Mode**
            - .
                ```
                ```
-- Create a procedure in DEFINER mode
CREATE PROCEDURE FetchEmployees()
    SQL SECURITY DEFINER
AS
BEGIN
    SELECT * FROM Employees; -- Requires SELECT permission
END;
```
                ```
            - If `user_definer` has `SELECT` permission on the `Employees` table, any user can invoke this procedure without having `SELECT` permission themselves.
        - INVOKER >>>
            - The procedure executes with the privileges of the user who invokes (runs) the procedure.
            - **Use Case**: Useful for granting flexibility, especially when different users have varying permissions on underlying tables or resources.
            - .
                ```
                ```
CREATE PROCEDURE procedure_name
    SQL SECURITY {DEFINER | INVOKER}
AS
BEGIN
    -- Procedure logic
END;
```
                ```
            - Scenario 2: **INVOKER Mode**
            - .
                ```
                ```
-- Create a procedure in INVOKER mode
CREATE PROCEDURE FetchEmployees()
    SQL SECURITY INVOKER
AS
BEGIN
    SELECT * FROM Employees;
END;
```
                ```
            - The permissions of the invoking user (`user_invoker`) are used to execute the procedure. If `user_invoker` lacks `SELECT` permission on the `Employees` table, the procedure will fail.
    - The .hdbroleconfig File >>>
        - The  *.hdbrole*  file can't contain references to real schema names, but only logical references to schemas that are resolved in another type of design-time file: the  *.hdbroleconfig*  file.  
        - You can create the .hdbroleconfig file manually and then specify this file when you create your .hdbrole file. Or you can generate the .hdbroleconfig file automatically from within the .hdbrole editor and then optionally adjust the generated file if necessary.
        - ![](https://remnote-user-data.s3.amazonaws.com/XRRds7Z_U53Kk2Ay9wrha5FgZJXHUkBEt_c--jvgrvbce4UtkUsJxETPeLHWO4K9vbHMfVXjBoopJjF4X5-Mj24eS52QRjg6GKP_ZI6yc2gTEfrIxCZZApfpMEhIhHAU.png)
        - ![](https://remnote-user-data.s3.amazonaws.com/tpNMmdKggTxq5DNM5UhGr6_OgTjgEOO3ZYAsnea00zJq2Jb5kb1Mu6yv0PJTxnfk_nUz6a3KLNQhcRzJxrCDYj7_dR0HvQFQjlgLLDEaePDTnEgGge3rsTstOVAx06zh.png)
    - Defining a Mask Expression >>>
        - A mask expression is defined in a calculation view as follows:
        - In the Semantics node, choose the Columns tab.
        - Select a column, and choose the Data Masking icon in the toolbar
        - Define the masking expression using SQL
        - Only columns of certain data types can be masked in a calculation view: VARCHAR, NVARCHAR. CHAR, SHORTTEXT
        - For example, you can mask the middle part of a credit card number stored in column credit_card with the following mask expression:  
        - .
            ```
            LEFT(credit_card,4) || '-XXXX-XXXX-' || RIGHT(credit_card,4)
            ```
        - **Nested calculation views with mask mode**
        - when two calculation views are nested, A column asking define in an underlying calculation view with mask mode default does not affect the calculation view that consumes it as a consequence you must make sure that the calculation views that you expose to the end users contain the relevant mass definition
        - **Calculated Columns Use Unmasked Data** 
        - When the expression of a calculated column refers to the column that has a column mask assigned the calculation uses unmasked data regardless of whether the user has the unmasked privilege for the view or not
        - **Authorizing Access to a Masked Column** 
        - At the schema level:  
        - .
            ```
            {
"role":
  {
    "name":"db::UNMASK_ENTIRE_SCHEMA",
    "schema_privileges":[{
      "privileges":["UNMASKED"]}]
  }
}
            ```
        - At the object level:
        - .
            ```
            {
"role":
  {
    "name":"db::UNMASK_EMPLOYEES_PAYMENT",
    "object_privileges":[{
      "type":"VIEW",
      "name":"db::CVD_EMPLOYEES_PAYMENT",
      "privileges":["UNMASKED"]}]
  }
}
            ```
- Project Structure
    - Project Structure >>>
        - Space
        - Workspace
        - Projects/db/srv
        - HDI Containers >>>
            - Containers play a key role in database development and provide an efficient method of isolating the database runtime artifacts and encouraging modularization
            - User and roles are granted privileges on containers so that they can access the resources they contain.
            - HDI containers are simply database schemas, but they include additional features such as a dedicated owner of the container plus metadata to manage their database objects. Think of a container as a smart schema.
            - HDI takes care of dependency management and determines the order of deployment; it also provides support for upgrading existing runtime artifacts when their corresponding design-time artifacts are modified.
            - the entire lifecycle (creation, modification, and deletion) of database objects is performed exclusively by the HDI
            - A container is create automatically when you deploy your database module for the first time.
            - When you bind an HDB module to an HDI container service, this binding creates the corresponding container schema, if it does not exist. After that, design-time objects can be deployed to the HDI container. 
            - HDI Container Configuration File >>>
                - under the src folder with the suffix: .hdiconfig. 
                - This configuration file that is used to bind the design-time files of your project to the corresponding installed build plug-in.
                - you want to add a new feature to a calculation view that just became available with the newer version of SAP HANA) you will not see the new feature in the source editor if the .hdiconfig file hasn't been adjusted to use the later feature version.
        - **Design time files** 
        - Tables HDB artifacts
        - Calculation views
        - Roles
        - Functions
        - Procedures
        - Analytical privileges
        - **Synonyms**
        - Direct references to objects outside your container aren't allowed. You must use synonyms.
        - ![](https://remnote-user-data.s3.amazonaws.com/WntxcHPQciveoX1OsQpVWg54gs6gf0drTAKWSODb8CD-l99TsNgw3dtiWaHq45--FtayZ5Yk3mmsrHYgbol-lczIuyMee-8BYyk-9XEsMRskoAzJdhnTOFCDUK9xbQ1b.png)
    - Create Deployment Package >>>
        - .hdiconfig ⇒ set of plugins which convert design time to runtime artifacts
        - mta.yaml ⇒ build MTA project
        - mbt build
        - cf deploy .mtar
    - Import Data >>>
        - Right click on the DB connection
        - Click on import Data
        - Select Import type ⇒ Import Data
        - Select Import from
        - Select Import target ⇒ Create a new table ⇒ Select Schema ⇒ new table name
        - Table Mapping ⇒ Rename columns name
        - Error Handling
    - SAP HANA Database Project >>>
        - Project name ⇒ any
        - Module Name ⇒ db
        - SAP HANA Database version ⇒ SAP Hana Cloud
        - Bind the database module to run time environment service instance ⇒ yes
        - Create a new HDI service instance ⇒ yes
        - Give name of HDI service instance ⇒ any
        - And use the default database instance of the selected cloud foundry space ⇒ Yes
    - Additional SAP Extensions >>>
        - **SAP HANA Calculation View Editor** 
        - Allows to edit and manage SAP Hana calculation views
        - **SAP HANA Tools** 
        - Allows you to develop native SAP Hana applications
    - Data preview with other database user >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/1_BSUYdH-WKbC01DBqT9Oe-BfEIkuOc301JtBWlKyxGZaK9-8YzouBES8D8xH8ta9LSwVF_cnhkAM1h0beXbSfe0w6jseGQTJ-rYcFOGua-yy91_DG9N5jhnNNMEMSiS.png)
    - .HDBTABLEDATA >>>
        - .
            ```
            {
    "format_version":1,
    "imports":{
        "target_table": "MERGE", //table name
        "source_data":{
            "data_type": "CSV",
            "file_name": "sales.csv"
            "has_header": true,
            "dialect": "HANA",
            "type_config": {
                "delimiter":","
}
},
"import_settings":{
    "import_columns":[]
}
}
}
            ```
    - Data preview hierarchies >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/5jDxQCyFmilH4NK0I7rD35Nb_Qoif3xE2VL9nf1R4LUVGEtVHAma0Law4BLR3klxPqEHkH2r7564Wb2h7zMKxJ5d4fspC00bWTfWAKHrt1kG-JPjfjC0FbQoBj5Hn_u4.png)
    - Default Schema Name of the HDB Module Container >>>
        - <project_name>_<hdi_service_name_in_MTA>_<numeric_increment>  
        - To identify the name of the generated database schema, open an SQL console connected to the container and execute the following SQL query:
        - .
            ```
            select CURRENT_SCHEMA from DUMMY;
            ```
        - Specifying a Schema Name for the HDI Container
        - .
            ```
            resources:
 - name: hdi-cont
   parameters:
     config:
       schema: <YOUR_SCHEMA_NAME>
   properties:
      hdi-container-name: ${service-name}
   type: com.sap.xs.hdi-container
   
   //Then, the schema generated during the first build of the HDB module is named as follows:
       
   <YOUR_SCHEMA_NAME>_<numeric_increment>
            ```
    - BAS Technical User >>>
        - Cockpit ⇒ HDI Instance ⇒ Service Key
        - ![](https://remnote-user-data.s3.amazonaws.com/emjoHT8UJ4Ca7b_ygrI5rmazVCnVfd5513cXKUcv1NfWbpg8TLs8i0-tSy6bhbHzY2zySjcRXhNVwpMSa6g8criRn9WssCp5UFsuHnL0C2N6FrRI2t7R7LO29kKN1iz5.png)
    - Web IDE for SAP HANA >>>
        - Web IDE is installed locally on your server
        - Support development of application for SAP Hana on premise system
        - Web IDE provides code editors, graphical modeling tools, debuggers, run-time performance tools, code libraries and an SQL console.
        - Web IDE is fully integrated with Git for source code version control.
    - Propagate Recursively >>>
        - From SAP HANA Cloud QRC 3/2023 onwards, it is possible to use the option  *in Consuming Views*  of the  *Rename and Adjust References*  tool, for a single column at a time. This column name is then renamed recursively, up to the last calculation view of any calculation view stack.  
    - Extract Semantics >>>
        - Apply the semantics from an underlying node or data source to the Semantics node of the calculation view.
        - For example, assume you are modelling a complex calculation view with multiple underlying data sources, and these data sources have their own semantic definitions for their columns. You can extract and copy the semantic definitions of columns from their underlying data sources to define the semantics of the calculation view. Extracting and copying the semantic definitions this way helps you save the effort of manually defining the semantics of the calculation view.  
        - For example, a Variable defined in an underlying Calculation View cannot be mapped to a Variable defined in the current Calculation View. However, it is possible to access these variables from the  *Extract Semantics*  feature and copy them to the current view. To do that, you right-click the data source in the calculation scenario and choose  *Extract Semantics* . Then choose the  *Variables*  tab and select the ones you want to copy to the semantics of your Calculation View.  
        - Similarly for parameter: Semantic node ⇒ Parameters ⇒ Extract Semantics from dimension views
        - In the **Columns** tab, choose Extract Semantics.
        - In the **Extract Semantics** dialog, the output columns from the underlying data sources is displayed.
        - Select the columns.
        - By default, the column properties, Label, Label Column, Aggregation Type, and Semantic Type are enabled. This means that the values of these column properties are extracted from the underlying views to the semantic definition of the calculation view.
        - Select and extract the hierarchies
        - Extract execution hints.
        - To override the existing semantic definition of the calculation view with the extracted semantics, select **Overwrite semantics already defined**.
    - Propagate to semantics >>>
        - If you change any label or anything in the bottom node 
        - You can right click on that changed output column and select propagate to semantics
    - Assign Semantics >>>
        - Go to [Semantics node](SAP%20HANA/Nodes/Semantics%20node.md)
        - Select any property and click on assign semantics
        - You can select the semantic type
        - For dimension ⇒ you can select date and others
        - For measure ⇒ you can select Amount with Currency code or Amount with Unit of measure
        - Based on the semantic type selected the consumer client can know whether it is Date, Currency and Unit of measure
        - Currency Conversion >>>
            - calculation views can convert currencies during runtime
            - To convert GROSS_AMOUNT_EUR to GROSS_AMOUNT_USD ⇒ Create a new column GROSS_AMOUNT_USD which is duplicate of GROSS_AMOUNT_EUR
            - Select GROSS_AMOUNT_USD and click on assign semantics ⇒ Select Amount with Currency
            - After assigning the semantic type,  *Amount with Currency Code* , you then must enable conversion and define the main parameters used for conversion.  
            - ![](https://remnote-user-data.s3.amazonaws.com/NGqepCli18nwHaRA1Qo5HjkUZO0BVt430MFcVmBg2T1hq85kLzKUgYPEdyJAL5yWovzd8lOD4ndzkouxKZvdHHXk-xbWqVeWqo4Gum0JdK7_heGEsjwUcOnPvDzxl2pS.png)
            - Select Defination tab
            - Client is mandatory
            - Source Currency 
            - Conversion Date
            - Target Currency 
            - **Exchange Type** 
            - Determine how exchange rates are applied to convert currency values. Commonly used exchange types include:
            -  **Average Rate**: Uses the average exchange rate over a specific period.
            -  **Closing Rate**: Uses the exchange rate at the close of a specific period, like month-end or year-end.
            -  **Spot Rate**: Refers to the current market rate at the time of the transaction.
            -  **Historical Rate**: Refers to the exchange rate from the date of the original transaction.
            - Exchange Rate ⇒ (optional) A column from the source data that contains the exchange rate to be used  
            - Generate  ⇒ If selected, this option creates a column that indicates for each converted amount the (target) currency in which it is expressed ⇒ This new column will show “USD” for each converted amount, indicating that the amounts are now expressed in USD.
            - [Standard SAP Currency Conversion Tables](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/implementing-currency-conversion_b9e42abd-1708-467d-a6d7-8a61912202f7) >>>
                - Standard tables exist in most SAP systems (in particular, SAP Business Suite and SAP S/4HANA), and SAP HANA Cloud can use them to compute data conversion.  
                - To enable currency conversion in SAP HANA Cloud, these tables must be available in the SAP HANA Cloud database. The tables are not provided by SAP HANA Cloud by default and you must provide them and update them yourself, either by creating a synonym to tables from an external schema, or using any of the available data provisioning tools.  
                - **TCURC** ⇒ Currency codes
                - **TCURR** ⇒ T006/T006A ⇒ Currency Exchange rates
                - TCURV ⇒  Currency Exchange rate types for currency translation
                - TCURF ⇒ Currency Conversion factors
                - TCURN ⇒ Currency Quotations
                - TCURX ⇒ Currency Decimal places
                - TCURT ⇒ Currency code names
                - TCURW ⇒ Usage of Exchange Rate Types
            - **Using an Input Parameter for the Currency** 
            - If you want to define the currency at runtime, when the view is executed, you can create an input parameter.
            - VARCHAR (5) is the way that the currency code is defined in the TCUR* tables, so to be consistent, we recommend that you define the input parameter with the same data type.
            - Then in the Assign Semantics pop up
            - Select Target Currency ⇒ Input Parameter ⇒ Input Parameter name
- Migration
    - 1. **SAP BTP Database Tools**
        - HANA Cloud Export/Import
            - Use `hdbsql` or SAP HANA Database Explorer to export tables to `.csv` or `.hdbtable` files and import them into the target subaccount.
            - For large datasets, use **backup/restore** or **schema replication** in SAP HANA Cloud.
    - **Cloud Integration (CPI)**:
        - Use HTTP/SOAP/OData adapters to call APIs in the source/target systems for data transfer.
    - 
- Data Provisioning
    - 
    - Cross-Container Access as User Provided Service >>>
        - To enable access to external schema you need a user-provided service
        - You simply choose the name of the service and a user id and password that has privileges to all external objects that you wish to access through the service
        - These privileges in turn will be granted by this service user, to the container's technical user and application users using a .hdbgrants file.
        - After you create the service, open the mta.yaml file, and notice how the user-provided service has been automatically added as a resource assigned to your HDB module.
        - **Steps** 
        - Create a new schema and import data in SAP Hana DB main instance (SYSTEM Instance)
        - Create a new user and role [CREATE USER AND ROLE WITH DBADMIN](SAP%20HANA/Security/CREATE%20USER%20AND%20ROLE%20WITH%20DBADMIN.md)
        - Grant container-specific access using `GRANT SELECT` on the external schema or container.
        - **Create a Cross service container as User provided service from BAS to the database** 
        - In BAS ⇒ SAP Hana Project tab
        - Select the hana project
        - Expand database connection
        - Add new database connection
        - Connection Type ⇒ Create User provided service instance
        - Enter service instance name ⇒ EFASHION_UPS
        - Connect to database ⇒ Use deployment target container database
        - use name ⇒ user of schema with admin privilege
        - password
        - Schema name ⇒ EFASHION
        - .hdbgrants file >>>
            - is used to define the set of authorizations that will be given to two specific users, the  *Object Owner*  and the  *Application User* .  
            - The user-provided service's user may have a lot of privileges on external objects, Technically, it would be possible to grant all the database privileges of that user to the object owner and application user, Instead, we should grant only the privileges that are needed, in other words, a subset of the service user's privileges.  
            - We define the privileges required by the object owner and application user using a special file with the suffix  *.hdbgrants*   
            - .
                ```
                {
    "cross_container_user_provided_service_name":{
        "object_owner":{
            "global_roles":{
                roles: ["bindrole"]
}
}
 "application_user":{
            "global_roles":{
                roles: ["bindrole"]
}
}
}
}
}
                ```
            - .
                ```
                {
    "cross_container_user_provided_service_name":{
        "object_owner":{
            "global_object_privileges":{
                "name": "CL_RS_OP_SDA",
                "type": "REMOTE SOURCE",
                "privileges": ["CREATE VIRTUAL TABLE"]
}
}
 "application_user":{
            "global_object_privileges":{
                 "name": "CL_RS_OP_SDA",
                "type": "REMOTE SOURCE",
                "privileges": ["CREATE VIRTUAL TABLE"]
}
}
}
}
}
                ```
            - **Object Owner:** 
            - When a database artifact is deployed to a database container, it is owned by the technical user of the container, NOT the developer who created the source file. Only the object owner can alter and drop the object. During deployment, the object owner must have privileges to access any external objects that have been referenced in any calculation view.
            - **Application User:  ** 
            - The application user is used to bind the HDI container to a consuming application. In the context of calculation view modelling, it is typically the user  *<container_schema_name>...RT*  who performs a number of modelling operations, including data preview on top of the HDI container connection  
            - if a certain privilege or role has been granted to the object owner and/or application user when you last built the .hdbgrants file, this will not be automatically reverted if you remove the privilege/role from the .hdbgrants file and build the file again.
            - Instead, you need to create a .hdbrevokes file, with the same structure, listing (only) the privileges and/or roles that you want to revoke. You must also remove these privileges and/or roles from the .hdbgrants. This will be effective after you build the .hdbrevokes file.
        - Synonyms >>>
            - The final step in the setup of external schema access is to create synonyms that point to the target objects of the external schema. The synonyms declaration is done in a .hdbsynonym file. This file type can be edited either with the text editor, as in the example below, or a dedicated synonym editor.
            - .
                ```
                {
  "HA300::SALES_DATA": {
    "target": {
      "object": "SALES_DATA",
      "schema": "TRAINING"
    }
  },
  "HA300::SALES_DATA_NEW": {
    "target": {
      "object": "SALES_DATA_NEW",
      "schema": "TRAINING"
    }
  },
  "HA300::CUSTOMER": {
    "target": {
      "object": "CUSTOMER",
      "schema": "TRAINING"
    }
  }
}
                ```
            - Name of synonym: You will refer to this name whenever you need to access the target object from the HDI container
            - Object: The actual object name in the target schema, such as a table name.
            - Schema: Where the target object is found.
        - Create a calculation view and add table in projection from cross container user provided service and create synonym
        - > There's no need for a user-provided service if the external container service is running in the same space as the one your project is assigned to.

You can add the external HDI container service to your project (which automatically adds it to the mta.yaml file).

You must create roles inside the external container that contain the relevant privileges to all objects that could be accessed by the service.  The .hdbgrants file refer to the dedicated roles created inside the external container
    - Connect on-premise Hana Database to SAP Hana Cloud >>>
        - Connect subaccount to [Cloud Connector - Principal Propagation]()
        - Choose cloud to on-premise in the side tab
        - Select add
        - Input Backend type ⇒ SAP Hana
        - Input protocol ⇒ TCP
        - port ⇒ 39015
        - virtual host and port can be same
        - Make Sure Cloud Connector property is allowed in SAP Hana Cloud Instance. Go to SAP Hana Cloud Central ⇒ edit the Hana instance ⇒ select Connections tab ⇒ Enable Cloud Connector
    - Access Tables of Other Schemas in SQL Console >>>
        - If you want to access tables in another schema, the owner of that schema (or a user with `GRANT` option) must grant you the required privileges.
        - Grant SELECT Privileges on the Target Schema  
        - `GRANT SELECT ON SCHEMA <target_schema_name> TO <your_user_name>;`
        - Use Fully Qualified Table Names  
        - `SELECT * FROM <schema_name>.<table_name>;`
        - When querying tables from another schema, always specify the schema name before the table name.
    -  System Database >>>
        - The primary and mandatory database created during the installation of an SAP HANA system. It handles system-wide management and administration tasks.
        - **System Management**:
        - It manages the overall SAP HANA instance, including starting and stopping tenant databases.
        - **User and Role Management**:
        - The system database is used to manage users and roles that can administer the entire SAP HANA system.
        - **Monitoring and Logging**:
        - The system database collects and stores logs, traces, and system performance data.
    -  Tenant Database >>>
        - A **Tenant Database** is an independently running database within the SAP HANA system. It stores application-specific data and runs separately from other tenant databases.
        - **Isolation**:
        - Each tenant database is isolated from other tenant databases in terms of:
        - Data storage
        - Users and roles
        - Backup and recovery
        - Network access (separate ports)
        - **Independent Administration**:
        - You can manage tenant databases independently (e.g., you can stop, start, back up, or restore them without affecting others).
        - **Scalability**:
        - Multiple tenant databases can be created within a single SAP HANA system, making it suitable for multi-tenant applications or hosting services where different clients or applications require their own database.
    - Mount Remote DB in Database Explorer >>>
        - Select Add database.
        - Select database type ⇒ SAP HANA Database (Multitenant)
        - Input host name ⇒ hxehost
        - Input instance number ⇒ 90
        - Select system database
        - If you check Tenant Database, then input name ⇒ HXE, which is your tenant database name
        - Username input is SYSTEM
        - Password input is the same password that we used while installing the SAP Hana database
    - Components for remote database >>>
        - ![](https://remnote-user-data.s3.amazonaws.com/2CavY1g9smXaXPy2L5iQmqUKsoH8DWnUFbmY67ZC-N7EexkyEEiRl8U1xhJQAaY3Xwe49ewJyLUnUBXDe8S7XN2YZsV9kF7oVuN_XFmJOOrYO5F8itZFPAe18znSEk42.png)
        - Connecting an SAP HANA Cloud target to an SAP HANA On-Premise source with SAP HANA smart data access requires an instance of the SAP Cloud Connector, acting as a reverse proxy to enter the company network where SAP HANA On-Premise runs.
        - SAP HANA Smart Data Access (SDA) >>>
            - SDA provides **virtual access** to remote databases. Instead of physically replicating data, it creates virtual tables in SAP HANA Cloud that point to tables in the remote database.
            - When you query the virtual table in SAP HANA Cloud, the data is fetched **on demand** from the remote database.
            - supports access and integration of data from multiple remote systems in real-time
            - Smart data access technology is included with SAP HANA on-premise and SAP HANA Cloud. 
            - Relies on built-in adapters that run on the SAP HANA database. There are mainly two types of adapters: ODBC and REST API adapters. The REST API adapters are only available in SAP HANA Cloud for Amazon Athena and Google BigQuery.
        - SAP HANA Smart Data Integration (SDI) >>>
            - Is relevant for SAP HANA on-premise and also SAP HANA Cloud. 
            - The HanaAdapters run on data provisioning agent. You specify either a data provisioning agent or group in case several agents are part of a high-availability agent group.  The agent is initially connected to the SAP Hana cloud
            - OData adapters run on the data provisioning server (inside the database). The location **dpserver **is automatically assigned and can’t be changed.
            - Allow you to fetch data from a remote database in near real-time or periodically, depending on the replication method and configuration you use.
            - Data Provisioning Server >>>
                - This service is running in the SAP HANA instance. It needs to be enabled to support any SDI connectivity.
            - Data Provisioning Agent >>>
                - This component is an application running on-premise, in your company network. This agent connects to the Data Provisioning Server and exposes various adapters so that they can be used by remote sources.
        - Smart data quality >>>
            - Is a set of functions provided by several components to cleanse and enrich data before it’s persisted in the SAP HANA database. 
            - Is relevant for SAP HANA on-premise but not for SAP HANA Cloud. For SAP HANA Cloud, a subset of SAP HANA smart data quality capabilities is provided by a subscription-based microservice called Data Quality Management Microservices (DQMm).
    - Remote Subscriptions >>>
        - Open Schema
        - Go to remote subscriptions ⇒ Right Click and select ⇒ Show Remote subscriptions
        - CHECK_REMOTE_SOURCE >>>
            - The execution of this procedure is either successful (check passed), or if it fails you get some details about the root cause in the SQL console.
            - .
                ```
                CALL CHECK_REMOTE_SOURCE('<remote_source_name>');
                ```
    - Datalake >>>
        - You can create tables, views, access the data reside on HANA Cloud data lake using Database Explorer option as mentioned.
        - All the data where high latency is allowed can be stored in sap Hana data lake
        - Sap Hana Cloud instance can connect with SAP datalake through virtualization process or replication process
        - Date lake on sap Hana instance can be accessed under remote connections folder
        - Add Datalake to sap Hana Cloud main DB instance >>>
            - Data lake is needed to store cold records which is not used for Analytics but still we need them
            - Go to sap Hana cloud tool central
            - Select the hana db instance
            - go to actions and click on add Datalake 
    -  CREATE REMOTE SOURCE >>>
        - it does not depend on any schema, and a given remote source can serve virtual objects defined in more than one schema.  
        - .
            ```
            CREATE REMOTE SOURCE <name> ADAPTER "hanaodbc" CONFIGURATION 'ServerNode=hanahost:39015;use_haas_socks_proxy=true;scc_location_id=L1' WITH CREDENTIAL TYPE 'PASSWORD' USING 'user=SYSTEM;password=Bismi@786'
            ```
        - hanahost:39015 is the virtual host of cloud connector
        - **Name** 
        - The name of the remote source must be unique in the target database.
        - **Adapter** 
        - The adapter defines the type of source database or source system you want to access with the remote source
        - **Location** 
        - The location of a remote source refers to where the adapter is located.
        - ![](https://remnote-user-data.s3.amazonaws.com/EdaiOeh7T0Rr4sCD1EhYAsXBNGJXxikYO37RWAkxci3fNp_3hYn7LHTSq1khcsRvK3s2pgBpKXUZnUAD0D8FLVnQq400SsZhpVIByq8uKKsAUvtaU51E1K-Dta664if2.png)
        - For SAP HANA smart data access, the adapters always run on the **indexserver **of the target SAP HANA instance.
        - ![](https://remnote-user-data.s3.amazonaws.com/cNJ8X484Z5cFAS-hv1cBXOq3M4X3NMtN7h-Jn3Yhj-yktyCaPUKIeSo6zfBL-s0Ew63Y3KE4-qx_l_3Pk4Z7nZo7Hw-XXgU_pzwihJJhHlzT4JbNWuF6poi2YG3oc4e3.png)
        - **Configuration** 
        - To identify the source system. For example, host and port, reference to a web service URL, or reference to a Data Source Name or DSN. This includes any additional setting specific to the remote system.
        - To identify the SAP Cloud Connector instance to be used, when connecting to an SAP on-premise system through the SAP Cloud Connector
        - To provide additional configuration for the remote source Behavior, such as the Data Manipulation Language (DML) mode (read/write or read-only), or some performance-related settings
        - To pass some security-related information, such as certificates
        - **Credentials** 
        - For example, this can be the name and password of a technical user, the mention of secondary credentials (mapping of technical users from the remote system to local users), or a certificate-based authentication method (X.509).
        - Preparation Steps >>>
            - ![](https://remnote-user-data.s3.amazonaws.com/mfIUwXkhb1Ry-YWnZk_X0wDWXw8z9gzmZQ6CfAyvT_zCsPoA8yOUWqGf-NMVdyty0F1MO9mx842wfzOavTq2uRnXgR9y6Fm6ZlPoqAd0NKusX21bIEUOxiyNUFa3BWn6.png)
    - Matching Adapter Name >>>
        - .
            ```
            SELECT * FROM "SYS"."ADAPTERS";

CALL CHECK_REMOTE_SOURCE('<remote_source_name>'); 
            ```
        - The result shows all the system adapters (SDA), plus all the adapters that are currently registered on any of the data provisioning agents connected to your local SAP HANA database.  
    - Virtual Table >>>
        - Select Remote Source and filter the table, Select the table and click Create Virtual table
        - ![](https://remnote-user-data.s3.amazonaws.com/DIwLPXF1Cb0UkO9mm1L0IctxVjXxPynNUeJ5b_0EXQS0BPNgzga_4SX3r2zOhcPZkCOkF7EVlSI1zo-KF-Y5XknJzm1h6Qn0fOKgd-keXSTMz1JO1ksV125KWzgxkj79.png)
        - .
            ```
            CREATE VIRTUAL TABLE <schema>.<virtual_table_name>
AT <remote_source>.<database>.<schema>.<object_name>;

CREATE VIRTUAL TABLE "CL_DATA_ENGINEER_FED"."VT_SNWD_PO"
AT "CL_RS_OP_SDA"."NULL"."EPM_MODEL"."SNWD_PO";

CREATE VIRTUAL TABLE "CL_DATA_ENGINEER_FED"."VT_SNWD_PO_I"
AT "CL_RS_OP_SDA"."NULL"."EPM_MODEL"."SNWD_PO_I"; 
            ```
        - The Database in the SQL query is always NULL when the remote system is SAP Hana on-premise Database
        - As a general rule, when you create a virtual table pointing to a remote database system, the remote user derived from the remote source definition (or an existing secondary credential associated to your user) only needs the CATALOG READ privilege (SAP HANA) or an equivalent, but NOT necessarily a SELECT privilege on the remote table or view (or its containing schema).  
        - The local user who created the remote source is automatically granted the privilege to create virtual tables on this remote source.
        - Any other use requires the CREATE VIRTUAL TABLE privilege on the remote source.
        - In any case, the local user needs CREATE ANY privileges on the schema where the virtual table will be stored.
        - A dedicated design-type artifact, .hdbvirtualtable, is used for that purpose.
    - Maintaining Privileges on a Remote Source >>>
        - The remote source privileges are object privileges (they refer to a specific remote source object) 
        - Administration privileges:
        - ALTER
        - DROP
        - Privileges to operate the remote source:
        - CREATE VIRTUAL TABLE
        - LINKED DATABASE
        - CREATE REMOTE SUBSCRIPTION
        - PROCESS REMOTE SUBSCRIPTION EXCEPTION
        - Another privilege is CREDENTIAL ADMIN, which is required to administer secondary credentials on a remote source that you did NOT create.
        - All these privileges are classically administered in the SAP HANA Cockpit or with GRANT and REVOKE statements in an SQL console.
    - Linked Database >>>
        - With SAP HANA smart data access connections based on ODBC adapters, there's a way to consume remote tables and views that doesn’t require the creation of virtual tables
        - feature is available between two SAP HANA systems, either cloud or on-premise
        - When you access a remote table or view with the linked database feature, SAP HANA creates a hidden virtual table located in the schema  *_SYS_LDB* .
        - This hidden virtual table is used to store the metadata of the remote object and isn’t meant to be queried.
        - To use the linked database feature, a specific privilege LINKED DATABASE on the remote source must be granted to the consumer of remote objects.
        - .
            ```
            SELECT * FROM "remote_source"."remote_schema"."remote_object"
            ```
        - To refresh the metadata for all linked objects
        - .
            ```
            ALTER REMOTE SOURCE <remote_source> REFRESH LINKED OBJECTS;
            ```
        - To delete the internally generated objects associated with linked database access
        - .
            ```
            ALTER REMOTE SOURCE <remote_source_name>
DROP LINKED OBJECTS [CASCADE | RESTRICT];
            ```
        - With the CASCADE option, the statement deletes all linked objects as well as any database object that depend on them.
        - With the RESTRICT option, the statement deletes all linked objects ONLY if no database object at all depend on them. If there's even a single dependency on a single linked object, no linked object at all is deleted.
    - Linked Database – Optimized Mode >>>
        - In scenarios where both the source and target systems are SAP HANA databases (and connected via an SAP HANA smart data access remote source), it's possible to use an optimized version of the linked database
        - Compared with the standard linked database feature, this one does NOT create hidden virtual tables, but caches locally the metadata of the remote objects you select data from. So there's no housekeeping needed (no hidden virtual tables) and no need to refresh statistics for remote objects: They're fetched automatically when needed.  
        - There are a few limitations to SAP HANA features with the linked database – optimized mode. Please refer to SAP Note 2605574.
        - .
            ```
            CREATE REMOTE SOURCE MY_HANA1
ADAPTER "hanaodbc"
CONFIGURATION 'Driver=libodbcHDB.so;ServerNode=myserver:30115;linkeddatabase_mode=optimized'
WITH CREDENTIAL TYPE 'PASSWORD' USING 'user=user1;password=Test1234';
            ```
    - Replication Services >>>
        - When you need to migrate an on-premise SAP HANA database to an SAP HANA Cloud database and want to select a subset of tables and views, you should consider this services.
        - Is used to replicate from SAP HANA on-premise to SAP HANA Cloud, and not the other way round. 
        - Doesn’t support non-SAP data sources or targets.
        - Is available in SAP HANA Cloud Central and allows you to replicate users (including their privileges), tables, SQL views, and calculation views from SAP HANA on-premise to the SAP HANA Cloud.
        - ![](https://remnote-user-data.s3.amazonaws.com/JwNhSytL0MQPNY7tpX7huoPssQNWn2vn4gfjyT9Xk-O7Wl7Taskj4kl5IwRT5-u2wFxtNXEyDW8pAmVrMu8Nt84es9OOHW7ShBJPlTBQ7iguHH1-DBHgzJTt7i-BDY16.png)
        - Open SAP Hana Cloud Central
        - Select create replication task in the data application card of the overview tab
        - Select the remote connection
        - Select the users to be replicated if needed
        - Select the source objects to be replicated
        - Change Data Capture (CDC) >>>
            - New data from the remote database is captured in near real-time using techniques. It listens for changes (insert, update, delete) and immediately applies them to the target table in SAP HANA Cloud.
        - **Batch replication**: Data is fetched periodically based on a schedule you define (e.g., every hour, daily).
    - Replica Tables >>>
        - At first, creating a report on top of a virtual table might seem a good option because this approach supports a quick implementation and easy maintenance.
        - But in some situations, replicating the remote data to the local SAP HANA system might offer better query performance than accessing the data in a remote table. So, you might consider abandoning the virtualization approach and reimplementing a replication approach.
        - But the problem with switching the data provisioning approach is that you'd need toadjust all applications to point to the new target table where the replicated data is loaded. This would generate transport and testing tasks that you might not want to be burdened with.
        - A replica table sits alongside a virtual table and has the same structure. The replica table captures and stores the data in real-time.
        - The key feature of implementing a replica table is a toggle that allows you to easily switch between the virtual table and the replica table. This means that you can switch to the replica table if performance begins to degrade and switch back when performance improves.
        - You can easily identify problematic remote queries and cases where it might be beneficial to toggle using two monitoring views:
        - M_EXPENSIVE_STATEMENTS >>>
            - When the execution time of a SQL statement that's run on a virtual table exceeds a certain threshold, it’s included in the M_EXPENSIVE_STATEMENTS monitoring view.  
        - M_REMOTE_STATEMENTS
        - Each virtual table can only have one replica table.
        - A replica table is updated in real time when the source table is updated
        - Replica tables are automatically generated and stored in an internal schema, managed by an internal user. They're hidden behind the scenes.
        - To add a replica to a virtual table, execute the following statement  
        - .
            ```
            ALTER VIRTUAL TABLE <virtual_table_name> 
ADD SHARED [SNAPSHOT] REPLICA;

//To directly access the virtual table again

SELECT * FROM <virtual_table_name> 
WITH HINT(NO_VIRTUAL_TABLE_REPLICA);

//use a SET statement before you execute the query.

SET 'VIRTUAL_TABLE_REPLICA' = 'FALSE'; 
SELECT * FROM <virtual_table_name>;

//Temporarily disable the replica using an ALTER statement.

ALTER VIRTUAL TABLE <virtual_table_name> DISABLE REPLICA;

//Delete the replica using a DROP command.

ALTER VIRTUAL TABLE <virtual_table_name> 
DROP REPLICA;
            ```
    - SAP Hana Flowgraph >>>
        - You create a flowgraph by creating a design time file in your project, with the extension  *.hdbflowgraph*   
        - You simply select node types and drag them to the canvas. You then configure each node with specific settings to define what should happen when the data travels through the node.  
        - It's possible to read from different data sources and to write to different data targets within the same flowgraph.
        - The sources can be virtual tables, local tables or views. You can combine data and split it up for distribution.  
        - you need to deploy the flowgraph. During deployment, the definitions are checked and executable run-time objects are generated in the container (database schema) of your module. 
        - After successful deployment, you can execute the flowgraph manually from the graphical editor, or you can schedule it.
        - While both calculation views and flowgraphs in SAP HANA can be used for data transformation, flowgraphs are typically preferred when you need more complex data manipulation, especially for large-scale ETL (Extract, Transform, Load) operations involving data cleansing, complex joins, data quality checks, and data persistence to external sources
        - What can you do if you don't yet have an existing table with suitable data types?
        - You can define the target as a template table. If you choose a template table, a new table is automatically proposed based on the output structure of the predecessor node.
        - Settings >>>
            - You can generate as a procedure or a batch type
            - If you have select batch type ⇒ In database explorer expand schema ⇒ Select Tasks ⇒ Click on execute
            - ![](https://remnote-user-data.s3.amazonaws.com/k4X0pi5zgheZy0Li1LNR9mLnYvvAiUY0xVC-yyHB95YVpBmNo1xz_pjfMmqF93nOc4agFDwknqg3W_6bzCeoBigltyDiuRiihw1T9R08Eqt-suxKhFYir_RTn177SoNy.png)
            - Setting | Purpose | Created run-time obj
            -  *Batch task*  | Process data as a batch or initial load |
                - A procedure
                - A task for batch load or initial load
            -  *Real-time task*  | Process data in real time
                - A procedure
                - A task for batch load or initial load
                - A task for processing updates in the sources in real-time
            -  *Transactional Task*  | Process data in real time without initial load 
                - A procedure
                - A task for processing updates in the sources in real-time
            -  *Procedure*  | Schedule or integrate the transformation in another procedure or flowgraph
                - Only a procedure
            - When you load to a data target that already contains data, you need to specify how the new and existing records are handled. This also applies to empty tables or template tables after the first execution. The following options exist:
            - Truncate: Delete all existing records and fill the records into the empty table.
            - Insert: Add new rows in addition to existing ones. For this option, define a sequence as key generator that finds the next unused integer as a row number.
            - Update: Overwrite existing records with additional or more current information. This option requires that you define a sequence and use a table with a primary key or define the key fields of the template target.
            - Upsert: Insert the new or update the changed records. This option requires that you define a sequence and you use a table with a primary key or define the key fields of the template target.
        - Splitting Data Sets >>>
            - You can implement different storage strategies, such as load to in-memory storage for recent records, and load to disk storage for older records.
            - You can store data at different levels of aggregation. For example, you could aggregate older data and load this to a table and leave the newer data at the line level and store this in a separate table.
            - Different transformation logic may be required. For example, a bonus calculation that depends on the job role, such as senior manager or sales executive.
            - The  *Case*  node receives data from a single inbound port and distributes the rows unchanged to multiple outbound ports.
            - For every outbound port, a Boolean expression is defined to determine which inbound rows will be transferred to it.
            - One outbound port is marked as "Default" to receive all the inbound rows that haven't been dispatched to any of the other ports.
        - **History Preservation**
        - Allows for maintaining older versions of rows when a change occurs by generating new rows in a target.
        - When a record changes in a source system, you usually want to update the data target with the change. You can choose to overwrite the existing record with the newer version, but you could also decide to keep the existing record, mark it as 'old', and load the new record alongside. This way you keep the old records as well as provide the new record to the business.  
        - Lookup >>>
            - is almost a join. You define matching criteria and add the corresponding values from another column
            - The lookup table should not change dynamically during data load of the main records. Therefore, the lookup transformation can be processed in real-time.
            - You can specify lookup table column and sort value pairs to invoke a sort, which selects a single lookup table row when multiple rows are returned.
            - You've learned how to fetch an attribute such as a phone number and add it to a sales record with the corresponding person. But if this person has multiple phone numbers a join would create a copy of the transaction data record with this person for each phone number. Suppose, you need only the first phone number, a  *Lookup*  node is what you need  
            - Configure default values in the form of constants to be output when no Lookup table rows are returned.
            - ![](https://remnote-user-data.s3.amazonaws.com/BQUe_gZ1Y2Bq2TzhtMU_WcceXkBsHGQ0H0rzuLFhJaDFD8qo9veQkVUIJgt7wVnIjFMQ-KdpPAmUs0S12YqRVYqShPEnvYn5QdS06UWAriShnddT1vPNPybRQFBtUrdr.png)
        - **Map Operation**
        - Sorts input data and maps output data.
        - Pivot >>1.
            - ![](https://remnote-user-data.s3.amazonaws.com/7HtIEwlCdZ6n3yyNRKx0rfiAGY7O5KdCFPOgfGnvOM4nOhnAUiMz6MgoAx6RhlEDBbByGtkDwFcjNXXz7KQfoV7gMcYY-wvZ21w6Q_6tCZggSTwP1-gyHTYqdGyfjCPp.png)
            - Unpivot ⇒ Creates a new row for each value in a column identified as a pivot column.
            - ![](https://remnote-user-data.s3.amazonaws.com/V5awLYPmkK9pUiZBZZOw5uUeu7-irmHIHcC8jt99HnGHrHMeLJ5U-amqkKwCBqAWBLlCYuC466AsiAvATAf3A0JFqw0ueTkBBV5XcSNuHIAa-vRtM3fRuShwarz0LUee.png)
            - In your flowgraph, add a  *Pivot Node*  and connect its predecessor to the new node.
            1. In the  *Axis Attributes*  section, select  *Click to select axis* . Select the column that you want to pivot on, and then choose  *OK* . A set of pivoted columns is generated for each axis value. Create an axis value for each unique value in the axis column. At runtime, a new column is created for each pivoted attribute and each unique axis value in the  *Axis Attribute*  section.
            2. Select  *Add Values*  to create one or more columns to hold the pivoted data. Type the column name in the  *Value*  column, and enter a prefix in the  *Prefix*  column. The new columns with their prefixes are displayed in the list of output columns after you specify the data in the columns. For example, if you distribute by month, you can generate prefixes  *Jan*  for the axis value (month)  *1*  and  *Feb*  for the axis value (month)  *2* .
            3. Under  *Data Columns* , choose the  *+*  icon for each data column whose values you want pivoted from rows into columns. For example, you can select  *Costs*  and  *Revenue*  columns that you wanted included. Notice that the  *Output Columns*  section generates  *Jan_Costs* ,  *Jan_Revenue* , *Feb_Costs*  , and  *Feb_Revenue*  as new columns. (The underscore is added automatically to separate the prefix name from the pivoted column name.)
            4. Under  *Output Columns* , select  *Pass Through*  to select the columns that you want to output without pivoting, typically attributes, for example, Country. The pass through columns appear in the target table without modification.
            5. Set the  *Duplicate Strategy*  at the bottom to choose the behavior when a duplicate is encountered.
                - Select  *Abort*  when you want to cancel the transform process.
                - Select  *First Row*  when you want to store the value in the first row.
        - Table Comparison ⇒ Compares two tables and produces the difference between them as a dataset with rows flagged as INSERT, UPDATE, or DELETE.
        - Data Quality Management microservice Cleanse (DQMm Cleanse) >>>
            - Record de-duplication, address cleansing, generating missing data, and enriching records with geographic information. 
            - For SAP HANA Cloud, the key data quality features are available via a subscription-based micorservice  
            - SAP HANA on-premise provides several nodes that support data quality. These are:
            - The node processes input data and compares it with reference data stored in country-specific address directories. The directories can be downloaded and deployed as part of SAP HANA Smart Data Integration or Smart data qualitiy installation for SAP HANA on-premise.
            - Match - find and deal with records that might be duplicates
            - Cleanse - tidy-up fields and generate additional information
            - Geocode - generate geographic data for a record
            - The Geocode node generates latitude and longitude coordinates for an address, and generates addresses from latitude and longitude coordinates.  
            - The node interprets the input data and compares them with reference information coming from local geographical information directories.
            - [Modeling Guide for SAP HANA Smart Data Integration and SAP HANA Smart Data Quality](https://help.sap.com/docs/HANA_SMART_DATA_INTEGRATION/cc7ebd3f344a4cdda20966a7617f52d8/bc040a4563de413f89cb2017988b7a13.html) 
        - Variable >>>
            - When you create variables, you can use them in nodes that accept them such as the Projection node and Aggregation node.
            - Choose the type of Variable ⇒ expression, as it is used in Filter expression
            - .
                ```
                "Filter1_Input"."COUNTRY" = $$P_COUNTRY$$
                ```
        -  *Procedure*  node >>>
            - To implement a complex sequence of SQL statements with just one node
            - To reuse an existing flowgraph
            - The node allows you to call database procedures (that is, in SQLScript) within a flowgraph.
            - The procedure node has an inbound or outbound port for every IN or OUT table parameter of the procedure
            - If a procedure has scalar input parameters, the values for these parameters come from variables defined in the flowgraph. These variables are created automatically. When the flowgraph is executed, you provide a value for each variable so that it can be passed to the input scalar parameters.
            - The procedure node is not available for real time processing.  
        - Projection node >>>
            - Filter expression
            - Remove fields (columns)
            - Rename fields (columns)
            - Add new fields (columns) using SQL expressions
        - **Nested Flowgraphs** 
        - If you have an existing basic flowgraph that should be called by one or more new top-level flowgraphs, you can deploy it as a procedure. Then, this procedure can be implemented in the new flowgraph.
        - How to debug a Flowgraph >>>
            - Switch on the debug panel
            - Select each relevant node and turn on preview option
            - Deploy the flow graph
            - Execute the flow graph and check the content of the interesting nodes
        - Scheduling a Flowgraph >>>
            - Make sure that the flowgraph has been deployed as a procedure.
            - In your project, create a source file with the extension .hdbschedulerjob, for example UPDATE_JOB.hdbschedulerjob.
            - The file should include the SQL command CREATE SCHEDULER JOB, but write it without the leading CREATE. You may already know this concept from writing the CREATE TABLE SQL command without the leading CREATE in a table definition (.hdbtable) file.
            - Depending on the flowgraph design, you need to provide various parameters in the .hdbschedulerjob file. For example, suppose you have created and deployed a flowgraph and its corresponding procedure with the name People_Fullname2 with a parameter P_COUNTRY. Its parameter value should be 'USA'. The job should run on Monday through Friday at 1:00 am during all of 2024 and 2025.
            - .
                ```
                SCHEDULER JOB UPDATE_JOB 
CRON '2024,2025 * * mon,tue,wed,thu,fri 1 00 00' 
ENABLE PROCEDURE "People_fullname2" 
PARAMETERS P_COUNTRY = 'USA'
                ```
            - After CRON, a cron expression (a string of format '<years> <months> <dates> <weekdays> <hours> <minutes> <seconds>') is expected. This expression defines the recurrence.
            - To delete the job, delete the file and redeploy the src folder.
        - Real-Time Processing >>>
            - Real-time means that records are immediately processed row by row. With batch processing, the data is selected in packages
            - **Valid for real-time** 
            - Aggregation
            - Case
            - Cleanse
            - Data Mask
            - Geocode
            - History Preserving
            - Lookup
            - Map Operation
            - Table Comparison
            - Union
            - **Not valid for real-time** 
            - Date Generation
            - Join
            - Match
            - Pivot
            - Procedure
            - Projection
            - Row Generation
            - Unpivot
            - **References** 
            - SAP note 2495696 – How to schedule execution of flowgraphs or replication tasks - SAP HANA Smart Data Integration
            - [How to schedule an SDI design time object monitor](https://help.sap.com/viewer/d60a5abb34d246cdb4ab7a4f6b9e3c93/latest/en-US/701be86d4ad3470485ee4ecb25258ba2.html) 
            - [How to Schedule a .hdbjobschedulerjob](https://help.sap.com/docs/hana-cloud-database/sap-hana-cloud-sap-hana-database-deployment-infrastructure-hdi-reference/scheduler-jobs-hdbschedulerjob) 
            - [Possibility of Real-Time Processing](https://help.sap.com/docs/HANA_SMART_DATA_INTEGRATION/cc7ebd3f344a4cdda20966a7617f52d8/1eb8869e70ef4ceb847063da3762ef21.html) 
    -  Replication in SAP Hana Cloud Database >>>
        - To replicate data from various data sources in SAP Hana Cloud, it’s necessary to set up access to that table. This is done by creating a remote source and a virtual table, as described in the previous unit.
        - In addition, you need to create the target table to store the replicated data. The target table can either have the same structure as the source table, be a subset of the source table with fewer columns, or have variations in the column definitions, for example, to reduce long strings where not all characters are needed.
        - Once you've created the virtual table and the target table, you can then define the remote subscription . The target table is then subscribing to the changes made to the data accessed by the virtual table.
        - **Log-based table replication** 
        - Uses the database redo log to fetch changes on the source table and reproduce them. It's non-intrusive, and transactional integrity is assured because only committed transactions are replicated.
        - **Trigger-based table replication** 
        - Triggers are created in the source database to monitor the source table and capture all modified (updated or deleted) and new rows. The captured data is stored in a shadow table. A queue table is also created to record all modifications in the correct sequence. This technology is independent of the source database version and may offer more functions than log-based replication, such as replication of Large Objects (LOBs).
        - **File replication** 
        - This is the technology implemented by the FileAdapter and is used to replicate new rows in a file. Only append is supported.
        - > Duplicate Data is sitting on both on-premise and SAP Hana cloud
        - If you have time sensitive queries then you can use replication scenario because virtualization layer will take some time
        - .
            ```
            Create schema reptest;

CREATE COLUMN TABLE "REPTEST"."R_OUTLET" LIKE "Virtual"."V_OUTLET" TARGET TABLE "REPTEST"."R_OUTLET";

//Virtual is case sensitive
 
CREATE REMOTE SUBSCRIPTION replicate ON "Virtual"."V_OUTLET" TARGET TABLE "REPTEST"."R_OUTLET"

//to activate replication
ALTER REMOTE SUBSCRIPTION replicate DISTRIBUTE 

//to Deactivate replication
 ALTER REMOTE SUBSCRIPTION replicate RESET
            ```
        - [SYNTAX](SAP%20HANA/SQLSCRIPT/SYNTAX.md)
        - [Remote Subscriptions](SAP%20HANA/Data%20Provisioning/Remote%20Subscriptions.md) - Verify the replication activation
        - Authorizations >>>
            - For the user specified in the remote source definition, the user must have CREATE ANY privilege on the source schema.
            - For the user implementing the replication in the target SAP HANA database, CREATE VIRTUAL TABLE and CREATE REMOTE SUBSCRIPTION on the remote source, CREATE TABLE on the target schema.
        - Implementation Steps >>>
            - Create a remote source.
            - Create a virtual table based on the remote source.
            - Create the target table
            - .
                ```
                CREATE COLUMN TABLE "REPTEST"."R_OUTLET" LIKE "Virtual"."V_OUTLET" TARGET TABLE "REPTEST"."R_OUTLET";
                ```
            - Define the remote subscription using the virtual table as the provider and the target table as the receiver.
            - .
                ```
                CREATE REMOTE SUBSCRIPTION <subscription_name> 
ON <virtual_table_name> 
TARGET <target_table_name>;
                ```
            - Queue the remote subscription, which involves creating source triggers, shadow tables, and queue tables for trigger-based replication (only applicable to SDI remote sources) ⇒ You will see many tables starting with HADP_SHADOW_OP_SOURCE* in the **Source SAP Hana On Premise System** 
            - .
                ```
                ALTER REMOTE SUBSCRIPTION <subscription_name> QUEUE;
                ```
            - If using SDI remote sources, copy the initial source data into the target.
            - .
                ```
                ‌INSERT INTO <target_table_name> 
(SELECT * FROM <virtual_table_name>);
                ```
            - Distribute the data, initiating real-time change data capture.
            - .
                ```
                ALTER REMOTE SUBSCRIPTION <subscription_name> DISTRIBUTE;
                ```
        - Replication Tasks >>>
            - To manage replications, you create a .hdbreptask file to define the metadata of the replication. The metadata describes the:
            - Remote source
            - Name of the Remote Source object used to access the source data.
            - Remote source object
            - Name of the table, view, or file you want to access.
            - Target table
            - Name of the table where the replicated data will be stored
            - Replication behavior >>>
                - You could choose to replicate all data only once. This is usually referred to as an initial load. This is useful if you wanted a snapshot of your data. You could also choose real-time replication without initial load if you only need future data. Then, you could also want to replicate changes in the structure of the table.
                -  ***No data transfer*** ** ** 
                - If you just want a copy of the source data structure, but without the source data. This is equivalent to a CREATE TABLE <...(new table)> like <...(existing table)>statement.
                -  ***Initial load only*** ** ** 
                - If you just want a copy of the current data but no replication of future data. This is called a snapshot.
                -  ***Initial + real-time*** ** ** 
                - Most probably, you want current data and future changes.
                -  ***Initial + real-time with structure*** ** ** 
                - The same as the previous option, but it will also replicate the changes in the source table structure. Only works for tables.
                -  ***Real time*** ** ** 
                - You could only need future data but no current data.
                -  ***Real-time only with structure*** ** ** 
                - Same as the previous option but with replication of the table structure.
            - Once built/deployed, the replication task generates the different objects needed in the SAP HANA database. These include:
            - A virtual table
            - A target table
            - A start procedure (.START_REPLICATION) to set up the replication
            - A second procedure (.RS_SP) to execute individual subscription commands
            - You need to call the main start procedure to create the remote subscription, triggers, and so on. The Replication Task only executes an initial load.
            - The second procedure can be called for maintenance or to execute single commands such as Reset, Drop, or any other subscription steps.
            - If your target is an on-premise database, you can create the User-Provided Service in XSA Cockpit:
            - ![](https://remnote-user-data.s3.amazonaws.com/jXewDdIzMmwBUM4tu9NJ5HdjpzRPezW3jQmdZ78aG2ydtW4AgEeAocwFDi5rppL36y8odwngkIWB3WNV5Wrh8WK1O4ixjMM6MLrm0JoJaJUXxdfl54kQMwu4XszOJcZm.png)
            - Be careful to add the "hana" tag 
            - The .hdbgrants file must include the required authorizations for both object owner and application user as shown in the following example:
            - .
                ```
                {
  "ServiceName_1":{
    "object_owner":{
      "global_object_privileges" : [
        {
          "name" : "OP_RS_OP_SDI",
          "type" : "REMOTE SOURCE",
          "privileges" : [ "CREATE VIRTUAL TABLE", "CREATE REMOTE SUBSCRIPTION" ],
          "privileges_with_grant_option" : [ "CREATE VIRTUAL PROCEDURE" ]
        }
      ]
    },
    "application_user":{
      "global_object_privileges" : [
        {
          "name" : "OP_RS_OP_SDI",
          "type" : "REMOTE SOURCE",
          "privileges" : [ "CREATE VIRTUAL TABLE", "PROCESS REMOTE SUBSCRIPTION EXCEPTION" ],
          "privileges_with_grant_option" : [ "CREATE VIRTUAL PROCEDURE" ]
        }
      ]
    }
  }
}
                ```
    - Connect to third-party databases >>>
        - Data Provisioning Server (DP Server) >>>
            - This is a built-in service or component within **SAP HANA**.
            - It works closely with the Data Provisioning Agent to handle data flows.
            - The Data Provisioning Server is often part of the “Smart Data Integration” or “Data Provisioning” component in HANA.
            - If you didn’t install it initially, you can run the HANA installer (or HANA lifecycle manager) again and select the relevant SDI/DP server component.
            - In **HANA Studio** or **HANA Cockpit**, check if the DP** server** service or plugin is running.
            - You can also check your installed plugins under **HANA Administration ⇒ Overview** ⇒ **Services/Plugins**.
            - ![](https://remnote-user-data.s3.amazonaws.com/oXaHtDjc6Dg1Ht8jOeGISx3Fct1NrgxLItDkPnw0JIyZWoa6XeehzGeCQeXmtPhYY97OCSQl7VhFtz8-1W29g1JXq--dyyTBEyTNbLc1scIEBLMnZTL1FHadi-0JPjR7.png)
        - Data Provisioning Agent (DP Agent) >>>
            - This is a separate piece of software you install **outside** of HANA, usually on a server that can connect to your source systems (e.g., Oracle, SQL Server).
            - It hosts various **adapters** (Java or C++) that know how to speak to different databases.
            - **Download the DP Agent software** from the SAP Software Downloads site (matching your HANA version).
            - **Install the DP Agent** on a server that can connect to the Oracle database.
            - **Configure the Agent** using the included **Data Provisioning Agent Configuration Tool**:
            - You’ll **register** the Agent with your HANA system (provide HANA hostname, ports, credentials).
            - SAP Hana Cloud  ⇒ You may need Cloud Connector
        - Adapters >>>
            - These are like “translators” that understand how to talk to each specific database (Oracle, MS SQL, etc.).
            - The **Oracle adapter** is typically a Java adapter that you configure in the DP Agent to connect to Oracle databases.
            - Install or enable the **Oracle adapter** (this might be the `com.sap.hana.dp.adapter.oracle` adapter or a similar name depending on your version).
            - Provide any necessary Oracle JDBC drivers if prompted (e.g., `ojdbc8.jar`).
        -  Create and Test the Remote Source in HANA >>>
            - 1. In **HANA Studio** or **Web IDE**, go to **Provisioning**→**Remote Sources**.
            - 2. Click **New Remote Source** and select the **Oracle adapter** (the one registered via the DP Agent).
            - 3. Provide **connection details** (hostname, port, username, password) for the Oracle database.
            - 4. **Test Connection** to ensure everything is set up correctly.
    - Create an ABAP Cloud Remote Source >>>
        - Import Certificates for SSL Connections to Remote Sources >>>
            - Connections to an SAP HANA database in SAP HANA Cloud, to an SAP HANA Cloud, data lake Relational Engine, or to an ABAP system require an SSL certificate
            - Download the DigiCert SSL certificates:
            - .
                ```
                //DigiCert Global Root CA:
curl -O [https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)

//DigiCert Global Root G2:
curl -O [https://dl.cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

DigiCert TLS RSA4096 Root G5: 
curl -O https://cacerts.digicert.com/DigiCertTLSRSA4096RootG5.crt.pem 
                ```
            - Create a certificate collection
            - .
                ```
                CREATE PSE <pse_name>;

CREATE PSE YU1_080_KEY_COLL; 
                ```
            - Create a certificate and insert the content of the downloaded certificate.
            - If you require more than one SSL certificate, create a separate certificate for each one.
            - .
                ```
                CREATE CERTIFICATE <certificate_name> FROM '
-----BEGIN CERTIFICATE-----
<content in certificate> 
-----END CERTIFICATE-----';

CREATE CERTIFICATE DigiCertTLSRSA4096RootG5  FROM '
-----BEGIN CERTIFICATE-----
<content in certificate> 
-----END CERTIFICATE-----';

CREATE CERTIFICATE DigiCertGlobalRootG2 FROM '
-----BEGIN CERTIFICATE-----
<content in certificate> 
-----END CERTIFICATE-----';

CREATE CERTIFICATE DigiCertGlobalRootCA FROM '
-----BEGIN CERTIFICATE-----
<content in certificate> 
-----END CERTIFICATE-----'; 
                ```
            - Add the certificate(s) created above to the certificate collection:
            - .
                ```
                ALTER PSE <pse_name> ADD CERTIFICATE <certificate_name>;

ALTER PSE YU1_080_KEY_COLL ADD CERTIFICATE DigiCertTLSRSA4096RootG5;

ALTER PSE YU1_080_KEY_COLL ADD CERTIFICATE DigiCertGlobalRootG2;

ALTER PSE YU1_080_KEY_COLL ADD CERTIFICATE DigiCertGlobalRootCA;
                ```
            - Set the purpose of the newly created PSE to REMOTE SOURCE:
            - .
                ```
                SET PSE <pse_name> PURPOSE REMOTE SOURCE;

SET PSE YU1_080_KEY_COLL PURPOSE REMOTE SOURCE FOR REMOTE SOURCE YU1_080; 
                ```
            - This makes the usage of the certificates in the PSE store available to all remote sources.
        - You have the CREATE REMOTE SOURCE system privilege ⇒ Use the DBADMIN credentials
        - ![](https://remnote-user-data.s3.amazonaws.com/-MkhMU7T9Z6wMf4yEEeqaQ-5e5i3ggV452YJkp5mvkchYjPVARQdPOOc1DOG2NDMFZPJDd7SKhijyastZkx4OaPzPWmzC-CYJJEY-7gBh9NCrUXI4nasfbfYybeBOYyk.png)
        - An ABAP cloud remote source lets you access ABAP CDS view entities and parameterized ABAP CDS view entities in the remote ABAP system. It can be used with virtual tables or the linked database feature.
        - Only the [SAP Technical User](SAP%20HANA/Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/SAP%20Technical%20User.md) credential type is supported.
        - CREATE VIRTUAL TABLE with WITH REMOTE Option: The WITH REMOTE option in the CREATE VIRTUAL TABLE statement allows for creating virtual tables that can perform write operations on the remote source. However, since ABAP remote sources are read-only, using this option isn't supported.
        - **Join Relocation:** Join relocation refers to processing joins directly on the remote source to optimize performance. Since ABAP remote sources are read-only, SAP HANA cannot push down join operations to the ABAP system. Instead, it retrieves the necessary data and performs the join operation within SAP HANA.
        -  *Example:*  Suppose you have two tables: `Customers` in SAP HANA and `Orders` in the ABAP system. If you want to join these tables to find out which customers placed which orders, SAP HANA will fetch the relevant data from `Orders` and then perform the join with `Customers` locally.
        - Exposing Virtual Tables and Using CDS Associations: You can expose virtual tables as part of your service definitions in SAP CAP. By doing so, you can create OData services that allow read access to data from these virtual tables. Associations can be defined to enable navigation properties in the OData service.
        - Example: In your CAP project, you might have a service that exposes both Customers and Orders, with an association between them. This setup allows consumers of your OData service to retrieve a customer and navigate to their orders seamlessly.
        - By default, the ODBC driver for ABAP runs on the indexserver
        - For troubleshooting purposes, traces for the ODBC driver for ABAP can be configured and managed within the SAP HANA trace framework.
        - SQL based
        - Data Federation (live data) with virtual tables
        - > Can only use CDS view entities
        - SAP Technical User
            - Search for and open the Maintain Communication Users app in SAP S/4HANA.
            - Click on New.
            - Enter the Username, Description, and Password for the new user.
            - Click on Create.
        - > Create a service binding of type SQL 
            - Mandatory to define SQL Schema
            - ![](https://remnote-user-data.s3.amazonaws.com/oSiUpvWNR6cBaWtPEbDjp3IZeGcnuA4p85TVuSyOIs-AVw0TuG5uKbJo3YwPWVnX1FB5YGzNsKN7KiHAdmCHGsZJz4qf1VV-Pxt4X7A6SVQY8tiIJSqd7KuDURxv-IfL.png)
        - Create a new Communication Scenario for CDS views
            - SAP_COM_0531 
                - SAP Datasphere - ABAP CDS Extraction - OData Integration  
                - Save the Cloud Data Integration Service URL/Service Interface, which looks like this example below:
                - <Host<Service Path> where in ‘Service path’ is /sap/opu/odata4/sap/cdi/default/sap/cdi/0001/
                - Host: my402042-api.s4hana.cloud.sap
                - Service path: /sap/opu/odata4/sap/cdi/default/sap/cdi/0001/?sap-client=080
                - ![](https://remnote-user-data.s3.amazonaws.com/PPJdbz3FpiyqmMrXkiTAphNhODYVtWNYbQamApcKU3aVN5SEyzOD1ni9p6YiOEIhLk6CtApMwKz4nR9tVKGgozSPh2Ngt0BL4wQ-WuSLhHjUp6rumShsBq4NdYFwc7VW.png)
            - S_PRIVILEGED_SQL1
                - In eclipse Right-click your package and choose New > Other ABAP Repository Object > Communication Scenario
                - Name:Z_COM_SCENARIO_SQL_####
                - Description: Communication Scenario for SQL test
                - Select a transport request and click Finish.
                - your communication scenario will be opened. go to the Inbound tab.
                - Since you want to use user/password authentication in your Windows Excel test case, select Basic as Supported Authentication Methods.
                - In section Inbound Services, choose the Add… button and enter S_PRIVILEGED_SQL1 as Inbound Service ID and click Finish.
                - The S_PRIVILEGED_SQL1 inbound service is a pre-configured service for the privileged access to CDS view entities, that is, no DCL is applied. (DCL stands for Data Control Language. It provides an access control mechanism to restrict the results returned by the CDS view from the database according to conditions)
                - ![](https://remnote-user-data.s3.amazonaws.com/vxqzFoTYUrXTLfqGkYrvM-EwlkJnDcPhFf8N3FWFqLOIHAumIiZ83XAny4-D5F-5BSqClWAoi1d3w5vVTkNn9HYxqqJnU_L5gm3oCMlnMH18ElBYsBZYOFhTG2OthT3Z.png)
                - Now you need to add additional authorizations to enable access to your service binding. Go to the tab **Authorizations**. Below **Authorization Objects**, press the **Insert** button.
                - Enter S_SQL_VIEW in Authorization Object field and click OK.
                - ![](https://remnote-user-data.s3.amazonaws.com/SZvSFjCQIurCQC28wLB6__jQ295tSXNa0kri0lQwDPQ4sy2lBAXW0UEYIDziEc6OOeMHkeY-Zt87f0Px4W2BbsJDIO_U4fenCHX3MPSXY32COF0nV5EaTmAGD5G7tNla.png)
                - Select the added authorization object and fill out the authorizations in the details:
                    - `SQL_SCHEMA`: Your schema name
                    - `SQL_VIEW`: `*`
                    - `SQL_VIEWOP`: `SELECT`
                - `SQL_SCHEMA` must contain the name of the service binding that you want to grant access to.
                - The value * for `SQL_VIEW` means that you allow access to all views in the service definition that is attached to the service binding ZORDERS.
                - Since currently only read only access is allowed, `SQL_VIEWOP=SELECT` is mandatory.
                - Save your entries and press the **Publish Locally** button to publish it in the current development system.  
        - Create a Communication System in SAP S/4HANA
            - Search for and open the Communication System app in SAP S/4HANA.
            - Click on New.
            - Enter the System ID, System Name, Host Name (can be same as System ID) and add Inbound and Outbound Users. The authentication method for Inbound/Outbound is Username and Password.
            - Click on Save.
        - Create a Communication Arrangement
            - Search for and open the Communication Arrangement app in SAP S/4HANA.
            - Click on New.
            - Select [SAP_COM_0531 ](SAP%20HANA/Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/Create%20a%20new%20Communication%20Scenario%20for%20CDS%20views/SAP_COM_0531.md) as Communication Scenario
            - Enter a name for your Arrangement Name. Then click on Create.
            - ![](https://remnote-user-data.s3.amazonaws.com/bDUtGHSfKjm24gVW4NYiDREjwdObsGYSYPuAGckc0ZWHvhavrMVo7inusWYCyoP9KDeIUiRgqq9ch6Du6hh6x8oqbwsshqGrYN4NjtsT2CDbFnkBENjsbQ1B64XBkFBe.png)
            - Add your Communication System that you created the previous step.
            - Add your Communication User that you created in the first step into the Username for the Inbound Communication section. Set the Authentication Method to User ID and Password.
            - Select **Authorization Group ID**  ⇒ SAP_DI
            - Click on Save.
            - Click on New.
            - Select [S_PRIVILEGED_SQL1](SAP%20HANA/Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/Create%20a%20new%20Communication%20Scenario%20for%20CDS%20views/S_PRIVILEGED_SQL1.md) as Communication Scenario
            - Add your Communication System that you created the previous step.
            - Click on Save.
        - Create a Remote Source Using SQL Syntax
            - .
                ```
                CREATE REMOTE SOURCE <remote_source_name> ADAPTER "abapodbc"
    CONFIGURATION 'uidtype=alias;driver=ODBC_driver_for_ABAP.so;servicepath=/sap/bc/sql/sql1/sap/s_privileged;
    host=<abap_host_name>;port=<abap_port_number>;
    language=EN;typemap=semantic'
    WITH CREDENTIAL TYPE 'PASSWORD' USING 'user=<username>;password=<password>';
                ```
            - servicepath  ⇒ Service path to the ABAP SQL service for privileged data access. This path is accessed with a technical user. Typically, the path name is the following: /sap/bc/sql/sql1/sap/s_privileged
            - user  ⇒ ID or alias name of the ABAP user, depending on the value of the uidtype property
            - uidtype (optional) ⇒ Either uid (default) or alias
            - uid: The user name in the credential is the ABAP user ID.
            - alias: The user name in the credential is the alias name of the ABAP user.
            - port : Port number of the ABAP cloud system: 443
            - Host address of the ABAP cloud system  
            - ![](https://remnote-user-data.s3.amazonaws.com/0ZqEvr1XBc4Ivq9ahh2ZCWtL26IqGInNeDrw3F2TnI3siN3iWrPH-hpJBOTkEWgA-U1rMYUuX7F4gUS7PgsCKF5gCTnRpQimv5xcucxCrKMdvOaBa6x7njo4d9cqZFJH.png)
        - To verify that a remote source is correctly configured, execute the following command:
        - .
            ```
            CALL CHECK_REMOTE_SOURCE('<remote_source_name>');
            ```
        - Business User
            - Data restrictions are applied
            - Create an IAM app
                - Add SQL service to IAM app
                - ![](https://remnote-user-data.s3.amazonaws.com/vBWRQ1IvRBfTRFCgpOX8x4Exu2rI-GkL6ErZ9rxv_bEkDh0uNaqYs3w2on3VC5z5QGqQRNi0S7jlu45FR3Cc0LCaBqevgRbPwwisZwPpwelggPN0upSHMhEZT6JT8dIL.png)
                - Add authorization object
                - Set Restriction Fields
            - Add IAM app to the Business Catalog
            - Publish Locally
            - Maintain Business User
                - Assign the business role
                - ![](https://remnote-user-data.s3.amazonaws.com/2MZggSkGLNiuzmOanb8JR6xzgjcRxt2FLN3XLOmXF_hbqsFZq_WhFFvgUlZbLdPPeeCjWDMxPHxTsyhvuCmynFCWn5ytu_J_Bo8WDDS-p3g7Zfb3CbLJ5bLcob5WQfd4.png)
                - Assign the business catalog to business role
                - ![](https://remnote-user-data.s3.amazonaws.com/hoBTJF2qSjxjqEkHXU2CnfCRY15RqW-rSu4i8sCiJchL6kueRZE5AHIAD9nqtHtw3bSsUkqvNc3YNoE7xVX-ep_AUF6fA52K04pqjgMlDI4JpZ9QyBPzL251L6Nl7bZH.png)
                - Maintain Business Role ⇒ Click on Display Restrictions
                - ![](https://remnote-user-data.s3.amazonaws.com/8TVx9CdlpitIRlGt8w1PUINEgafP0ymu6efIOMYleDjP8xqrFTgB1lAxbAFFRXGO3fvPEfVU3hdeANygm1Gv2qb1jnTUBMG8zdqCBar5ke6Yl7J0pPWXRxLcB-3dXADc.png)
            - ![](https://remnote-user-data.s3.amazonaws.com/3CZGyGKaOzaYXePPqB1V1LcVIv2j6JMjHfZEmicepScrHY7C69GcG2Td7aHHhyWvkx2YCKDoeK-ORFKX2Lzcw8U0KFO_D8C8QwguCIxxGQqFO5ALnzAaxeEUObm6SrG9.png)
    - 
- Learning By Experience
    - Use static filter or dynamic filter at the bottom as possible in the graphical view of calculation view on that exact table Because join are costly so before join condition try to filter
    - Each Dimension table will have multiple items in fact table so the cardinality is 1..N
    - Unions are less expensive than joins
- SAP Hana Express Edition On Premise
    - Installation
        - When you download **SAP HANA Express Edition** from the **SAP Download Manager**, it is typically provided as a **preconfigured virtual machine image. ** 
        - **.ova file** (for VirtualBox or VMware) – This is an "Open Virtual Appliance" format that contains the entire virtual machine setup, including the operating system, SAP HANA software, and preconfigured settings
        - PDF document for configuring in VMware. passwords, steps
        - [VMware Workstation or VMware Player: ]()
        - [Oracle VirtualBox: ]()
        -  **Why can't you directly run it without VirtualBox or VMware?** 
        - The **.ova file** is designed to run inside a virtualized environment that mimics the hardware and software configuration required by SAP HANA. It already includes a Linux-based OS (SLES or RHEL) and all necessary dependencies.
        - Without a hypervisor like **VMware** or **VirtualBox**, you would have to manually install and configure SAP HANA on a bare-metal server running a compatible OS, which is complex and time-consuming.
        - [Host mapping]()
    - Check status of Apps
        - login with admin u: xs-admin-login
        - password is same as the one you used while creating the database
        - xs apps | grep webide
        - You should see STARTED and 1/1 which mean 1 out of 1 instance is used
    - Check status of HDB
        - In the Virtual machine 
        - Open CLI ⇒ HDB info
    - WEBIDE/DB Explorer
        - open edge
        - [Host mapping]()
        - hxehost:39030 or 192.168.1.7:39030
        - click webide u:XSA_ADMIN p:same which you created at the time of installation
- References
    - SQL Reference Guide
        - SAP Hana Cloud ⇒ SAP Hana Cloud Database ⇒ Development ⇒ SAP Hana Cloud Database SQL Reference Guide
    - 
- Data integration in ABAP Cloud
    - DATA CONSUMPTION VIA CDS EXTERNAL ENTITIES
        - ![](https://remnote-user-data.s3.amazonaws.com/GyY6u1DEehHFS-ZY6es4DzvyM59UjHCQ8PAZkWgGoXPgYVSGUBfxwO9eODGwH03Aj55HyZZG1PqllBN4SOXwd2D2DT2dngScffsFrpVSKUB0enXx9M5EbiEziQwA0cm6.png)
        - Outbound SQL access
        - CDS external entities to model the structure of remote views/tables - Dynamic Access
            - .
                ```
                @EndUserText.label: 'Sports school'
define external entity ZEE_SPORTSCHOOL external name SPORTSSCHOOL //same as virtual table name
{
    key SchoolUuid     : abap.char(16) external name SCHOOLUUID;
    SchoolId           : abap.char(16) external name SCHOOLID;
    SchoolName         : abap.char(100) external name SCHOOLNAME;
    SchoolLocation     : abap.char(40) external name SCHOOLLOCATION;
    SchoolCountry      : abap.char(40) external name SCHOOLCOUNTRY;
} with federated data
  provided at runtime

                ```
            - SPORTSSCHOOL ⇒ //same as virtual table name in our Hana data source
            - SQL access via ABAP SQL
            - .
                ```
                Select .. from MyView provided by myRemote
                ```
        - Logical external schema as a pointer to a remote system
            - ![](https://remnote-user-data.s3.amazonaws.com/AsTZKII8MFqBGbZQOEGiYKct9wAot2-7jQxw5VI6Ls0xFWkXt80eYVzIg02X1i0dwTOM5rS76uqQLL79iEXb0nDimJ_lC-_Qzio1tdKkmbWOw3tvFasDCqHQ2Jfn7efr.png)
        - Communication Scenario with outbound service
            - ![](https://remnote-user-data.s3.amazonaws.com/dHBxt27_43bHGJGWEMvWATKojwPvTrvkp3o35sGB1rxgfViZCM61cQKgGKwWU2IKGsmVFUL9BaXiIPYEBvh9JKHnutp7lpcbcaC12hGBQA89ioku1abRyy2iF54GGn_i.png)
        - Communication Arrangement
            - ![](https://remnote-user-data.s3.amazonaws.com/Pvjl8h94Zv3Leyhbz-D99iWViP_r8bbmhudU_LZp7nzQ_Y4LeKmKWP5tj4W8RDAYauP-vuQbWpte60xaUSWZso6LZNkUXpvDE61uGKUWDCT9XZ9HklqaaH3Yzshd3j6d.png)
        - Communication System
            - ![](https://remnote-user-data.s3.amazonaws.com/U5qL1jyVX6iRo8lQLYYG4AohgFZzI0-PmnPHt5W_tomOa40K5hcrNFiLZ_hkfXAjXTzX--S52q5xiTAwh-RpNZg5Qo7R4sjT4ZgpCJSIU65q9rC1Wa4ldbG7w00e44h0.png)
            - ![](https://remnote-user-data.s3.amazonaws.com/cLA9GR21MaMkBm3soXvh8pm3zWMpJRT9IFoJUjgMc7mwbvf2ZuJow3E9fa4bvePZyqRTHaSgYQmzIXieXVeQ4_1wKIFsxanaEQjoPJGytWGJRSPl2Fhx3AKbtYvrw8Gi.png)
        - Based on SAP Hana Smart Data Access
    - 
