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
    - SQLScript enables you to access SAP HANA-specific features like [Column Tables](../SAP%20HANA/Views/Column%20Tables.md), parameterized calculation views, [Delta Buffers and Merge](../SAP%20HANA/Concepts/Delta%20Buffers%20and%20Merge.md), working with multiple result sets in parallel, built-in [Standard SAP Currency Conversion Tables](../SAP%20HANA/Project%20Structure/Assign%20Semantics/Currency%20Conversion/Standard%20SAP%20Currency%20Conversion%20Tables.md) at database level, fuzzy text searching, spatial data types, and predictive analysis libraries.
    - SQLScript also adds extra [data types](https://learning.sap.com/learning-journeys/develop-data-models-with-sap-hana-cloud/working-with-sqlscript_cd4b02d8-fad9-4a26-99d7-8d9ec61691a5) to help address missing features from standard SQL - for example, spatial data types and text data types.
    - .
        ```
        books_per_publisher = SELECT publisher, COUNT (*) AS num_books FROM BOOKS GROUP BY publisher;

publisher_with_most_books = SELECT * FROM :books_per_publisher WHERE num_books >= (SELECT MAX(num_books) FROM :books_per_publisher);
        ```
    - The SQLScript compiler and optimizer determine how to best execute these statements, whether by using a common sub-query expression with database hints or by combining this into a single complex query.  
    - [Conditional Logic](SQLSCRIPT/Conditional%20Logic.md)
    - [Variable](../SAP%20HANA/Data%20Model%20Concepts/Variable.md)
    - [Input Parameter](../SAP%20HANA/Data%20Model%20Concepts/Input%20Parameter.md)
    - [Table functions](../SAP%20HANA/Views/Table%20functions.md)
    - [Table Function Node](../SAP%20HANA/Nodes/Table%20Function%20Node.md)
    - [Scalar Function](../SAP%20HANA/Views/Scalar%20Function.md) 
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
