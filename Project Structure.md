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
    - Go to [Semantics node](../SAP%20HANA/Nodes/Semantics%20node.md)
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
