- 
- Cross-Container Access as User Provided Service >>>
    - To enable access to external schema you need a user-provided service
    - You simply choose the name of the service and a user id and password that has privileges to all external objects that you wish to access through the service
    - These privileges in turn will be granted by this service user, to the container's technical user and application users using a .hdbgrants file.
    - After you create the service, open the mta.yaml file, and notice how the user-provided service has been automatically added as a resource assigned to your HDB module.
    - **Steps** 
    - Create a new schema and import data in SAP Hana DB main instance (SYSTEM Instance)
    - Create a new user and role [CREATE USER AND ROLE WITH DBADMIN](../SAP%20HANA/Security/CREATE%20USER%20AND%20ROLE%20WITH%20DBADMIN.md)
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
    - [SYNTAX](../SAP%20HANA/SQLSCRIPT/SYNTAX.md)
    - [Remote Subscriptions](Data%20Provisioning/Remote%20Subscriptions.md) - Verify the replication activation
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
    - Only the [SAP Technical User](Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/SAP%20Technical%20User.md) credential type is supported.
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
        - Select [SAP_COM_0531 ](Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/Create%20a%20new%20Communication%20Scenario%20for%20CDS%20views/SAP_COM_0531.md) as Communication Scenario
        - Enter a name for your Arrangement Name. Then click on Create.
        - ![](https://remnote-user-data.s3.amazonaws.com/bDUtGHSfKjm24gVW4NYiDREjwdObsGYSYPuAGckc0ZWHvhavrMVo7inusWYCyoP9KDeIUiRgqq9ch6Du6hh6x8oqbwsshqGrYN4NjtsT2CDbFnkBENjsbQ1B64XBkFBe.png)
        - Add your Communication System that you created the previous step.
        - Add your Communication User that you created in the first step into the Username for the Inbound Communication section. Set the Authentication Method to User ID and Password.
        - Select **Authorization Group ID**  ⇒ SAP_DI
        - Click on Save.
        - Click on New.
        - Select [S_PRIVILEGED_SQL1](Data%20Provisioning/Create%20an%20ABAP%20Cloud%20Remote%20Source/Create%20a%20new%20Communication%20Scenario%20for%20CDS%20views/S_PRIVILEGED_SQL1.md) as Communication Scenario
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
