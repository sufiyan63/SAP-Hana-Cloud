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
    - [Value Help Based on Hierarchies](Data%20Model%20Concepts/Value%20Help%20Based%20on%20Hierarchies.md)
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
    - Can also be used when you have scalar or table functions in your Calculation View. The parameters of these functions can be fed with values that you enter at runtime using the [Parameter Mapping](Data%20Model%20Concepts/Parameter%20Mapping.md), when querying your calculation view
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
        - Filter pushdown does not occur per default for [Rank Node](../SAP%20HANA/Nodes/Rank%20Node.md), [Window function nodes](../SAP%20HANA/Nodes/Window%20function%20nodes.md), and nodes that feed into two other nodes  
        - However, you can force a pushdown even in these situations by using one of the following options:  
        - **Allow Filter Push Down** – for [Rank Node](../SAP%20HANA/Nodes/Rank%20Node.md) and [Window function nodes](../SAP%20HANA/Nodes/Window%20function%20nodes.md)
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
