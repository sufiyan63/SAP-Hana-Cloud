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
