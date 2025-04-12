-  Persistence Layer >>>
    - In-Memory
        - The HANA database operates in-memory, providing fast data processing and real-time analytics.
        - [DRAM(Dynamic Random Access Memory)](Concepts/DRAM(Dynamic%20Random%20Access%20Memory).md) and [PMEM (Persistent Memory)](Concepts/PMEM%20(Persistent%20Memory).md) for high-speed data processing.
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
    - Supports multiple [Data models](Concepts/Data%20models.md) and processing engines:
    - A Data Model is a blueprint that tells a database how to organize, store, and manage data
    - Combine both OLAP and OLTP in a single platform
    - **SQL**, **Document Store**, **Graph**, **Spatial**, and **Text** processing capabilities.
    - [Multi-Core Architecture ](Concepts/Persistence%20Layer/Multi-Core%20Architecture.md)
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
    - Allows SAP HANA [Column Tables](../SAP%20HANA/Views/Column%20Tables.md) to handle fast data inserts and updates without slowing down the operations.
    - In a sales application, every new sale record is first written to the delta buffer. This ensures that your system remains responsive even when many transactions happen simultaneously.
    - Delta Merge
        - Periodically, SAP HANA merges the data from the delta buffer into the main [Column Tables](../SAP%20HANA/Views/Column%20Tables.md) . This makes sure that your data stays organized and optimized for queries.
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
