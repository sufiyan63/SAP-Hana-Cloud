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
    - Then in semantics ⇒ [Label Column Field](../SAP%20HANA/Nodes/Semantics%20node/Label%20Column%20Field.md)
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
