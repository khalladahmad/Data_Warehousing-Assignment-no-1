# ☁️ Azure Data Factory — Complete Beginner to Advanced Guide


##  What We Built — Screenshot Index

Throughout this assignment, screenshots were taken at every major step and included in the Word document submission. Here is what each screenshot covers:

---

## 📚 Table of Contents

1. [What is Cloud Computing?](#1-what-is-cloud-computing)
2. [What is Microsoft Azure?](#2-what-is-microsoft-azure)
3. [Core Azure Concepts](#3-core-azure-concepts)
4. [What is Azure Data Factory?](#4-what-is-azure-data-factory)
5. [ETL vs ELT](#5-etl-vs-elt)
6. [ADF Architecture](#6-adf-architecture)
7. [ADF Studio — The Three Tabs](#7-adf-studio--the-three-tabs)
8. [Linked Services](#8-linked-services)
9. [Datasets](#9-datasets)
10. [Pipelines & Activities](#10-pipelines--activities)
11. [Copy Activity — Deep Dive](#11-copy-activity--deep-dive)
12. [Triggers](#12-triggers)
13. [Control Flow Activities](#13-control-flow-activities)
14. [Parameterization & Dynamic Content](#14-parameterization--dynamic-content)
15. [Monitoring & Debugging](#15-monitoring--debugging)
16. [Incremental Load & Watermark Pattern](#16-incremental-load--watermark-pattern)
17. [REST API Integration](#17-rest-api-integration)
18. [Git Integration & CI/CD](#18-git-integration--cicd)
19. [Error Handling](#19-error-handling)
20. [Deployment Strategy](#20-deployment-strategy)
21. [What We Implemented — Full Summary](#21-what-we-implemented--full-summary)
22. [Key Expressions Reference](#22-key-expressions-reference)
23. [Common Errors & Solutions](#23-common-errors--solutions)
24. [Best Practices](#24-best-practices)
25. [Glossary](#25-glossary)

---

## 1. What is Cloud Computing?

Before understanding Azure, you need to understand **cloud computing** — the foundation of everything.

**Traditional computing (before cloud):**
- A company buys physical servers (machines)
- Stores them in a room called a "data centre"
- Pays for electricity, cooling, maintenance, and IT staff
- If the server breaks → data is lost or inaccessible
- If the business grows → they must buy more servers (takes months)

**Cloud computing:**
- Instead of buying servers, you **rent** computing power from a provider
- The provider (Microsoft, Amazon, Google) owns the physical machines
- You access them over the internet instantly
- You pay only for what you use — like paying an electricity bill
- If you need more power, you get it in seconds — called **scaling**

**The three types of cloud services:**

| Type | Full Name | What it means | Example |
|---|---|---|---|
| IaaS | Infrastructure as a Service | Rent raw servers and storage | Azure Virtual Machines |
| PaaS | Platform as a Service | Rent a ready-made platform to build on | Azure SQL Database |
| SaaS | Software as a Service | Use ready-made software over the internet | Microsoft 365, Gmail |

> Azure Data Factory is a **PaaS** service — Microsoft manages all the infrastructure, you just use the tool.

---

## 2. What is Microsoft Azure?

**Microsoft Azure** is Microsoft's cloud computing platform. It is one of the three largest cloud providers in the world alongside Amazon Web Services (AWS) and Google Cloud Platform (GCP).

Azure provides over **200 services** including:

- **Compute** — Virtual Machines, Azure Functions (run code without servers)
- **Storage** — Blob Storage, Data Lake, File Storage
- **Databases** — Azure SQL, Cosmos DB, MySQL, PostgreSQL
- **Analytics** — Azure Synapse Analytics, Azure Databricks
- **AI & Machine Learning** — Azure Machine Learning, Cognitive Services
- **Integration** — Azure Data Factory, Logic Apps, Event Hub
- **DevOps** — Azure DevOps, GitHub Actions
- **Security** — Azure Active Directory, Key Vault

**Why companies use Azure:**
- No upfront hardware investment
- Available in 60+ regions worldwide (your data stays close to your users)
- Complies with 90+ international standards (GDPR, ISO, HIPAA)
- Integrates perfectly with Microsoft tools (Excel, Power BI, SQL Server, Teams)
- Pay-as-you-go — scale up or down any time

**Azure Free Account:**
New users get **$200 free credit for 30 days** + 12 months of free popular services. This is what we used for this entire assignment.

---

## 3. Core Azure Concepts

### Subscription
An Azure **Subscription** is your billing account. All resources you create are tied to a subscription. One person can have multiple subscriptions (e.g., one for personal projects, one for work). When your free trial ends, resources stop working unless you upgrade.

### Resource Group
A **Resource Group** is a logical container (like a folder) that holds related Azure resources together. 

**Why it matters:**
- Organise resources by project — all resources for this assignment live in `ADF-Assignment-RG`
- Delete everything at once — delete the Resource Group and all resources inside it are deleted too
- Apply permissions at the group level — give a teammate access to the whole project with one click
- See costs per project — the billing dashboard shows cost per Resource Group

**Naming convention we used:** `ADF-Assignment-RG` (hyphens instead of spaces, descriptive name)

### Region
A **Region** is a physical location in the world where Azure has data centres (server buildings). Examples: West Europe (Netherlands), East US (Virginia), Southeast Asia (Singapore).

**Why it matters:**
- Data stored in West Europe physically lives in the Netherlands
- Choose a region close to your users for faster performance
- Some services are not available in all regions
- Always use the **same region** for all resources in one project to avoid data transfer costs

### Azure Resource Manager (ARM)
**ARM** is the management layer behind everything in Azure. When you click "Create" in the portal, ARM processes your request, validates it, and provisions the resource. ARM Templates are JSON files that describe your infrastructure — used for automated deployment across environments.

---

## 4. What is Azure Data Factory?

**Azure Data Factory (ADF)** is Microsoft Azure's fully managed, serverless data integration service. It allows you to create, schedule, and manage data pipelines that move and transform data at scale.

**Simple analogy:**  
Think of ADF as a **smart postal service for data**. You tell it:
- Pick up data from *this location* (source)
- Optionally transform it along the way
- Deliver it to *this location* (destination)
- Do it automatically on a schedule
- If delivery fails, retry 3 times, then notify me

**What problems does ADF solve?**

| Problem | Without ADF | With ADF |
|---|---|---|
| Data in many places | Manual exports and imports | Automated pipelines connect everything |
| Different data formats | Write custom code | Built-in format conversion |
| Scheduled data movement | Cron jobs on servers | Schedule triggers, no servers needed |
| Error handling | Custom code + emails | Built-in retry, alerts, monitoring |
| Scale | More servers needed | Serverless — scales automatically |
| Team collaboration | No version control | Git integration built-in |

**ADF supports 90+ connectors including:**
- Azure Blob Storage, Azure SQL, Azure Synapse, Azure Data Lake
- Amazon S3, Amazon Redshift, Google BigQuery
- Salesforce, SAP, Oracle, MySQL, PostgreSQL
- REST APIs, FTP, SFTP, HTTP
- Excel, CSV, JSON, XML, Parquet, Avro files

**ADF is NOT a:**
- Computation engine (it doesn't run complex transformations — use Databricks for that)
- Reporting tool (use Power BI for that)
- Real-time streaming tool (use Azure Event Hub or Stream Analytics for that)

**ADF IS a:**
- Data movement and orchestration service
- Pipeline scheduler and monitor
- Connection hub between hundreds of data sources

---

## 5. ETL vs ELT

These are the two main patterns for data pipeline design. Understanding the difference is fundamental to data engineering.

### ETL — Extract, Transform, Load

```
Source ──► [Extract] ──► [Transform] ──► [Load] ──► Destination
                              ↑
                    Transformation happens
                    BEFORE reaching destination
```

**How it works:**
1. **Extract** — Read raw data from the source system
2. **Transform** — Clean, filter, aggregate, join, rename the data (this happens in a staging area or in ADF Data Flows)
3. **Load** — Write the clean, transformed data to the destination

**When to use ETL:**
- Destination storage is expensive (on-premise data warehouses)
- Data must be clean before it enters the warehouse
- Compliance requirements — no raw data allowed in destination
- Legacy systems built before cloud became mainstream

**Example:** Read sales data from SAP → clean and filter it in ADF Data Flow → load only valid sales records into on-premise SQL Server

### ELT — Extract, Load, Transform

```
Source ──► [Extract] ──► [Load] ──► [Transform] ──► Analytics
                                         ↑
                              Transformation happens
                              INSIDE the destination
```

**How it works:**
1. **Extract** — Read raw data from the source
2. **Load** — Dump ALL raw data into a cloud data warehouse immediately
3. **Transform** — Use SQL or Spark inside the warehouse to transform data

**When to use ELT:**
- Cloud data warehouses (Azure Synapse, Snowflake, BigQuery) — cheap storage and compute
- Data scientists need raw data to explore
- Multiple teams transform data differently for their own needs
- Modern data lakehouse architecture

**Example:** Copy all raw API responses into Azure Data Lake → analysts use Azure Synapse to transform and query it

### ADF's Role in Both

| Pattern | ADF's Role |
|---|---|
| ETL | ADF performs transformations using **Data Flows** (visual Spark-based transformation) |
| ELT | ADF simply **copies raw data** to Azure Synapse or Data Lake — transformation happens there |

> **In this assignment** we implemented a simple version of ELT — we copied data from sources into Azure Blob Storage and SQL without heavy transformation.

---

## 6. ADF Architecture

ADF has several key architectural components that work together:

```
┌─────────────────────────────────────────────────────────┐
│                  Azure Data Factory                      │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │   Pipelines   │  │   Datasets   │  │Linked Services│  │
│  │  (workflows)  │  │  (pointers)  │  │ (connections) │  │
│  └──────────────┘  └──────────────┘  └───────────────┘  │
│                                                          │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │   Triggers   │  │ Data Flows   │  │  Integration  │  │
│  │  (schedule)  │  │(transforms)  │  │   Runtime     │  │
│  └──────────────┘  └──────────────┘  └───────────────┘  │
└─────────────────────────────────────────────────────────┘
         │                    │                   │
         ▼                    ▼                   ▼
   Source Systems        Monitoring           Git Repo
  (Blob, SQL, API)      (Azure Monitor)      (GitHub)
```

**Integration Runtime (IR):**  
The IR is the compute infrastructure ADF uses to perform data movement. There are three types:

| Type | Where it runs | Use case |
|---|---|---|
| Azure IR | Microsoft's cloud | Moving data between cloud services |
| Self-hosted IR | Your own server | Connecting to on-premise databases |
| Azure-SSIS IR | Azure cloud | Running legacy SSIS packages |

> In this assignment we used the default **Azure IR** — it's created automatically and handles all our cloud-to-cloud data movement.

---

## 7. ADF Studio — The Three Tabs

When you open ADF Studio at `adf.azure.com`, you see three main sections:

### ✏️ Author Tab (Pencil Icon)
This is where you **build** everything. It contains:
- **Pipelines** — your data workflows
- **Datasets** — pointers to your data
- **Data flows** — visual data transformation designer
- **Power Query** — Excel-like data transformation

Everything you create here is saved as a **JSON file** behind the scenes.

### 📊 Monitor Tab (Bar Chart Icon)
This is your **control room**. It shows:
- **Pipeline runs** — every run, its status, duration, trigger type
- **Activity runs** — individual activity details within a run
- **Trigger runs** — history of when triggers fired
- **Integration runtime** — health of your compute infrastructure

The Monitor tab is where you debug failures — click the glasses icon 👓 on any activity to see exact input, output, and error messages.

### ⚙️ Manage Tab (Wrench Icon)
This is **settings and configuration**. It contains:
- **Linked services** — all your data source connections
- **Integration runtimes** — compute configuration
- **Triggers** — view and manage all triggers
- **Git configuration** — connect to GitHub or Azure DevOps
- **Parameters** — global parameters shared across all pipelines

---

## 8. Linked Services

A **Linked Service** is ADF's connection to an external system. It stores the credentials and connection details needed to authenticate with a data source or destination.

**Analogy:** A Linked Service is like a saved Wi-Fi password on your phone. You save it once, then any app (pipeline) on your phone (ADF) can use the internet without you typing the password again.

**What a Linked Service stores:**
- The type of connection (Azure Blob Storage, SQL Database, REST API, etc.)
- The server address / URL
- Authentication credentials (account key, username/password, OAuth token)
- Network settings (if using private endpoints)

**Linked Services we created:**

| Name | Type | Connects to |
|---|---|---|
| `LS_AzureBlobStorage` | Azure Blob Storage | Our storage account with input/output containers |
| `LS_AzureSQL` | Azure SQL Database | Our ADF_DB database with employee data |
| `LS_REST_API` | REST | JSONPlaceholder public API |

**Important:** Credentials in Linked Services should ideally be stored in **Azure Key Vault** in production — never hardcoded. ADF integrates with Key Vault natively.

**Linked Service vs Dataset relationship:**
```
Linked Service = the road between ADF and your system
Dataset        = the specific address/location on that road
```

---

## 9. Datasets

A **Dataset** is a named reference to the data you want to use in a pipeline. It points to a specific file, table, folder, or API endpoint within a Linked Service.

**Analogy:** If a Linked Service is the library building, a Dataset is the specific book on a specific shelf inside that library.

**A Dataset defines:**
- Which Linked Service to use (the connection)
- The specific location of the data (container + filename, or table name)
- The format of the data (CSV, JSON, Parquet, etc.)
- The schema (column names and types)

**Datasets we created:**

| Name | Type | Points to |
|---|---|---|
| `DS_Source_CSV` | DelimitedText (CSV) | Blob input container — employees.csv |
| `DS_Sink_CSV` | DelimitedText (CSV) | Blob output container |
| `DS_SQL_Source` | Azure SQL | employees_source table |
| `DS_SQL_Watermark` | Azure SQL | watermark_table |
| `DS_REST_Posts` | REST | /posts endpoint |

**Parameterized Datasets:**  
Instead of creating one dataset per file, you can parameterize a dataset to accept the filename at runtime. This way one dataset can represent thousands of different files.

---

## 10. Pipelines & Activities

### Pipeline
A **Pipeline** is a logical grouping of activities that together perform a task. It is the workflow definition — it says "do this, then do that, then if it fails do this other thing."

A pipeline itself does not run code. It orchestrates activities. Think of it as a recipe — it lists the steps but doesn't cook the food itself.

**Pipelines we created:**

| Pipeline | Purpose |
|---|---|
| `PL_Copy_Employees` | Simple copy of one CSV file |
| `PL_ForEach_MultiFile` | Loop and copy multiple files |
| `PL_Parameterized_Copy` | Dynamic pipeline accepting any filename |
| `PL_Incremental_Load` | Copy only new rows using watermark |
| `PL_REST_API_Load` | Fetch data from REST API |

### Activities
An **Activity** is a single step inside a pipeline. Each activity does one specific thing.

**Categories of activities:**

**Data movement:**
| Activity | What it does |
|---|---|
| Copy Data | Copies data from source to sink |
| Get Metadata | Reads file/folder properties (size, last modified, exists) |
| Delete | Deletes files from storage |

**Control flow:**
| Activity | What it does |
|---|---|
| ForEach | Loops over a list of items |
| If Condition | Branches based on true/false |
| Switch | Multiple conditional branches |
| Until | Loops until a condition is met |
| Set Variable | Assigns a value to a pipeline variable |
| Lookup | Reads a single value from a source |
| Wait | Pauses for a set number of seconds |
| Execute Pipeline | Calls another pipeline |
| Filter | Filters an array based on a condition |

**Transformation:**
| Activity | What it does |
|---|---|
| Data Flow | Visual Spark-based transformation |
| Stored Procedure | Runs a SQL stored procedure |
| Script | Runs a SQL script |
| HDInsight | Runs Hadoop/Spark jobs |
| Databricks | Runs Azure Databricks notebooks |

**Other:**
| Activity | What it does |
|---|---|
| Web | Calls any HTTP endpoint |
| Azure Function | Triggers an Azure Function |
| Send Email | Sends an email via Logic Apps |

---

## 11. Copy Activity — Deep Dive

The **Copy Activity** is the most used activity in ADF. It moves data from a source to a sink.

**What happens internally when Copy runs:**

1. ADF reads the **Source Dataset** to know where to read from
2. ADF authenticates with the source using the **Linked Service** credentials
3. ADF reads data in parallel chunks using **Data Integration Units (DIUs)** — more DIUs = faster copy
4. Optionally applies **column mapping** — rename or reorder columns
5. Writes data to the **Sink Dataset** destination
6. Logs every metric: rows read, rows written, bytes transferred, duration
7. Reports success or failure back to the pipeline

**Copy Activity performance settings:**

| Setting | What it does |
|---|---|
| Data Integration Units (DIU) | CPU/memory for the copy. More DIUs = faster but more expensive |
| Degree of copy parallelism | How many files copied in parallel |
| Staged copy | Use Blob Storage as a staging area for large copies |

**Supported copy scenarios:**
- Cloud to cloud (Blob to SQL, API to Data Lake)
- On-premise to cloud (SQL Server on your laptop to Azure SQL)
- Cloud to on-premise
- Binary copy (copy files without reading content — fastest)

---

## 12. Triggers

A **Trigger** defines when a pipeline runs. Without a trigger, a pipeline only runs when you manually click Debug or Trigger Now.

### Manual Trigger
- You click "Trigger Now" in ADF Studio
- Used for: one-time runs, testing, re-running a failed pipeline
- Not automated

### Schedule Trigger
- Runs on a fixed time schedule
- Examples: every day at 2 AM, every Monday at 8 AM, every hour
- Uses cron expressions or a simple recurrence UI
- Supports time zones — important for international companies

**We created:** `TR_Daily_Copy` — runs every day, timezone set to Islamabad/Karachi

### Tumbling Window Trigger
- Runs at regular intervals like a schedule trigger
- BUT tracks each time window separately
- Used for: reprocessing historical data, filling data gaps
- Example: process Jan 1 window, then Jan 2 window, then Jan 3 window — independently

### Event-based Trigger
- Fires when something happens in Azure Blob Storage
- Examples: new file arrives, file is deleted
- Used for: real-time or near-real-time pipelines
- No polling needed — Azure sends a notification the instant a file arrives

| Trigger type | When to use |
|---|---|
| Manual | Testing and one-time runs |
| Schedule | Daily/weekly batch jobs |
| Tumbling Window | Reprocessing historical periods |
| Event-based | React immediately when new data arrives |

---

## 13. Control Flow Activities

Control flow activities make pipelines **intelligent**. Instead of one fixed sequence of steps, you can have loops, conditions, and dynamic behaviour.

### ForEach Activity
Loops over a list (array) of items and runs the inner activities once for each item.

```
fileList = ["employees.csv", "products.csv", "departments.csv"]

ForEach item in fileList:
    CopyActivity(source = item, sink = "output_" + item)
```

**Key settings:**
- **Items** — the array to loop over (use `@variables('myArray')` or `@activity('Lookup').output.value`)
- **Sequential** — process items one by one (safe but slow)
- **Batch count** — how many items to process in parallel (faster but uses more resources)

**Used in:** `PL_ForEach_MultiFile` — looped over 3 CSV files

### If Condition Activity
Evaluates a true/false expression and runs different activities depending on the result.

```
If @greater(activity('GetCount').output.firstRow.count, 0):
    TRUE path  → Copy the data
    FALSE path → Send "no data" notification
```

### Lookup Activity
Reads data from a source and returns the result to the pipeline. Other activities can then use this result.

**Used in:** `PL_Incremental_Load` — two Lookup activities read the old and new watermark values from SQL

### Set Variable Activity
Assigns a value to a pipeline variable. Variables can be String, Integer, Boolean, or Array type.

**Variables vs Parameters:**
| | Variables | Parameters |
|---|---|---|
| Set by | Set Variable activity (at runtime) | Passed in when pipeline is triggered |
| Can change | Yes — can be updated during the run | No — read-only during run |
| Used for | Tracking state during a run | Configuring the pipeline from outside |

---

## 14. Parameterization & Dynamic Content

### Why Parameterize?

A **static pipeline** has hardcoded values:
```
Source: input/employees.csv  ← hardcoded
Sink: output/employees_out.csv  ← hardcoded
```
This means you need a separate pipeline for every file. With 100 files → 100 pipelines. Unmaintainable.

A **parameterized pipeline** accepts values at runtime:
```
Source: input/@{pipeline().parameters.fileName}  ← dynamic
Sink: output/@{pipeline().parameters.sinkFile}   ← dynamic
```
One pipeline handles all 100 files. Professional standard.

### Dynamic Content — ADF's Expression Language

ADF has a built-in expression language that starts with `@`. You use it anywhere you'd type a value.

**Expression syntax:**
```
@{expression}           ← inside a string
@expression             ← standalone expression
```

**Most important expressions:**

| Expression | What it returns | Example result |
|---|---|---|
| `@pipeline().parameters.myParam` | Value of a parameter | `employees.csv` |
| `@variables('myVar')` | Value of a variable | `["file1","file2"]` |
| `@activity('MyActivity').output.firstRow.ColumnName` | Output of a previous activity | `2024-01-15` |
| `@item()` | Current item in a ForEach loop | `employees.csv` |
| `@utcNow()` | Current UTC date and time | `2024-03-15T08:00:00Z` |
| `@formatDateTime(utcNow(), 'yyyyMMdd')` | Formatted date | `20240315` |
| `@concat('prefix_', item())` | Join strings together | `prefix_employees.csv` |
| `@pipeline().RunId` | Unique ID of current run | `abc123-def456` |
| `@greater(10, 5)` | Compare two values | `true` |

**Used in our pipelines:**
- `@variables('fileList')` — fed the file list to ForEach
- `@item()` — got current filename inside the loop
- `@concat('output_', item())` — built dynamic sink filenames
- `@pipeline().parameters.sourceFile` — read pipeline parameters
- `@activity('Get_Old_Watermark').output.firstRow.WatermarkValue` — read SQL lookup result

---

## 15. Monitoring & Debugging

### Debug Mode
Debug runs the pipeline immediately in a temporary mode:
- Does NOT require publishing first
- Runs with real data
- Results appear in the Output tab at the bottom
- Marked as "Debug" in Monitor — does not count as a triggered run
- Free to run — no trigger history recorded

### Triggered Runs
When a pipeline runs via a trigger or "Trigger Now":
- Recorded permanently in Monitor → Pipeline Runs
- Shows in Trigger Runs history
- Can be re-run from Monitor if it fails

### Reading the Monitor Tab

**Pipeline runs view:**
- Click pipeline name → see all activities within that run
- Click 👓 glasses on any activity → see:
  - **Input tab** — what configuration ADF used
  - **Output tab** — rows read, rows written, bytes, duration
  - **Error tab** — full error message and error code

**Important metrics to check:**
| Metric | Where to find it | Why it matters |
|---|---|---|
| Duration | Pipeline runs list | Slow pipelines need optimization |
| Rows read | Activity output tab | Verify correct data volume |
| Rows copied | Activity output tab | Should match rows read (unless filtered) |
| Throughput (MB/s) | Activity output tab | Low throughput = network or DIU issue |
| Error message | Activity error tab | Full details for debugging |

### Retry Policy
Set on individual activities (General tab):

| Setting | Recommended value | Why |
|---|---|---|
| Retry | 3 | Handles transient network errors |
| Retry interval | 30–60 seconds | Gives the system time to recover |
| Timeout | 1–2 hours | Kills stuck pipelines automatically |

---

## 16. Incremental Load & Watermark Pattern

### Why Incremental Load is Critical

In production systems:
- Tables can have **millions or billions of rows**
- Data is added continuously throughout the day
- Only a small fraction changes each run

Copying everything every time would be:
- **Slow** — copying 1 billion rows takes hours
- **Expensive** — you pay for every byte transferred
- **Wasteful** — 99% of the data is identical to last run
- **Risky** — duplicates data in the destination

**Incremental load copies ONLY new or changed records** — this is standard practice in all production data pipelines.

### The Watermark Pattern — Step by Step

A **watermark** is simply the timestamp of the last successfully processed record. It marks the "water level" — everything below has been processed, everything above needs to be processed.

**Setup needed:**
1. Source table must have a column that changes when a row is updated — usually `UpdatedAt`, `ModifiedDate`, or `CreatedAt`
2. A watermark table in SQL to store the last processed timestamp

**Watermark table structure:**
```sql
CREATE TABLE watermark_table (
    TableName VARCHAR(100),    -- which table this watermark is for
    WatermarkValue DATETIME    -- last processed timestamp
);

-- Initial value (start of time → first run copies everything)
INSERT INTO watermark_table VALUES ('employees_source', '2024-01-01');
```

**The 4-activity pipeline:**

```
[Lookup: Get Old Watermark] ──► [Lookup: Get New Watermark] ──► [Copy: New Rows Only] ──► [Stored Proc: Update Watermark]
         │                                │                              │                           │
  SELECT WatermarkValue           SELECT MAX(UpdatedAt)         WHERE UpdatedAt >          UPDATE watermark_table
  FROM watermark_table            AS NewWatermark               OldWatermark AND            SET WatermarkValue =
  WHERE TableName =               FROM employees_source         UpdatedAt <=                NewWatermark
  'employees_source'                                            NewWatermark
```

**Run 1 example:**
- OldWatermark = `2024-01-01` (initial value)
- NewWatermark = `2024-02-20` (MAX date in source)
- Query copies all 4 rows (all are after Jan 1)
- Watermark updated to `2024-02-20`

**Run 2 example:**
- OldWatermark = `2024-02-20` (saved from Run 1)
- NewWatermark = `2024-02-20` (nothing new added)
- Query copies 0 rows — nothing newer than watermark
- Watermark stays at `2024-02-20`

**Run 3 (after new data added):**
- OldWatermark = `2024-02-20`
- NewWatermark = `2024-03-10` (new records added)
- Query copies only records between Feb 20 and Mar 10
- Watermark updated to `2024-03-10`

---

## 17. REST API Integration

### What is a REST API?

A **REST API** (Representational State Transfer Application Programming Interface) is a standard way for software systems to communicate over the internet using HTTP.

When you use any modern app — weather apps, payment systems, social media — they all use REST APIs behind the scenes.

**How a REST API works:**
```
Client (ADF)                          Server (API)
     │                                     │
     │── GET /posts ──────────────────────►│
     │                                     │ finds all posts
     │◄── 200 OK + JSON data ─────────────│
     │                                     │
     │── POST /posts (with body) ─────────►│
     │                                     │ creates new post
     │◄── 201 Created ────────────────────│
```

**HTTP Methods:**
| Method | What it does |
|---|---|
| GET | Read/fetch data |
| POST | Create new data |
| PUT | Update existing data |
| DELETE | Delete data |

**HTTP Status Codes:**
| Code | Meaning |
|---|---|
| 200 | Success |
| 201 | Created successfully |
| 400 | Bad request (your request has an error) |
| 401 | Unauthorized (wrong credentials) |
| 404 | Not found |
| 429 | Too many requests (rate limit hit) |
| 500 | Server error |

### ADF REST Connector

ADF's REST Linked Service connects to any HTTP API. Key settings:

**Base URL:** The root URL of the API. Relative paths are added per dataset.
```
Base URL: https://jsonplaceholder.typicode.com
Dataset relative URL: /posts
Full URL called: https://jsonplaceholder.typicode.com/posts
```

**Authentication types we learned:**
- **Anonymous** — no credentials (public APIs like JSONPlaceholder)
- **Basic** — username + password in HTTP header
- **API Key** — secret key passed in header or query string
- **OAuth2** — token-based (enterprise APIs — ADF auto-refreshes tokens)
- **Managed Identity** — ADF uses its own Azure identity (most secure)

**Pagination:**
Most APIs return data in pages (e.g. 100 records per page). ADF handles this automatically using pagination rules — it keeps calling the next page URL until no more pages exist.

**What we built:** `PL_REST_API_Load` — connected to JSONPlaceholder, fetched 100 posts, saved to Blob Storage as a JSON file.

---

## 18. Git Integration & CI/CD

### Why Version Control for Data Pipelines?

ADF pipelines are JSON files behind the scenes. Without version control:
- One wrong click in production → pipeline broken → business stops
- No history → can't see what changed or when
- Multiple developers → overwrite each other's work
- No way to test before going live

### Git Integration in ADF

ADF integrates natively with **GitHub** and **Azure DevOps**.

**How it works:**
- Every pipeline, dataset, and linked service is stored as a JSON file in your Git repo
- Changes made in ADF Studio are saved to a branch first
- Only published changes go live
- Full commit history — you can restore any previous version

**Branches in ADF:**

| Branch | Purpose |
|---|---|
| `feature/xxx` | Developer builds new pipelines here — safe sandbox |
| `main` | Reviewed and approved code — team's stable version |
| `adf_publish` | Auto-generated by ADF — contains ARM templates ready to deploy |

**We created:** `feature/advanced-pipelines` branch for our advanced work.

### CI/CD — Continuous Integration / Continuous Deployment

**CI (Continuous Integration):**  
Every time code is pushed to a branch, automated tests run to verify nothing is broken. In ADF context — ARM template validation runs automatically.

**CD (Continuous Deployment):**  
When code is approved and merged to main, it is automatically deployed to the next environment (Test, then Production) without manual steps.

**ADF CI/CD Flow:**
```
Developer → feature branch → Pull Request → Code Review → 
merge to main → Publish → adf_publish branch → 
ARM template → deploy to Test ADF → approval → 
deploy to Production ADF
```

**ARM Template:**  
When you click "Publish" in ADF, it generates an **ARM Template** (JSON file) in the `adf_publish` branch. This file can be used to recreate your entire ADF instance in any Azure subscription — perfect for deploying to Test and Production environments identically.

---

## 19. Error Handling

### Activity Outcome Paths

Every activity in ADF has 4 possible outcome paths:

| Path | Colour | When it fires |
|---|---|---|
| On Success | 🟢 Green | Activity completed without errors |
| On Failure | 🔴 Red | Activity threw an error |
| On Completion | 🔵 Blue | Activity finished — success OR failure |
| On Skip | ⚫ Grey | Activity was skipped (due to upstream condition) |

**Professional error handling pattern:**
```
                    ┌─────────────────┐
                    │   Copy Data      │
                    └────────┬────────┘
                    ┌────────┴────────┐
               🟢 Success          🔴 Failure
                    │                  │
                    ▼                  ▼
             ┌──────────┐      ┌──────────────┐
             │Log Success│      │  Log Failure  │
             │  + notify │      │ + send alert  │
             └──────────┘      └──────────────┘
```

**Web Activity for notifications:**  
The Web activity calls an HTTP endpoint. You can use it to:
- Post to a Microsoft Teams webhook (sends a Teams message)
- Call a Logic App (sends an email)
- Log to a custom monitoring system

### Retry Policy

Set on the General tab of any activity:

```
Retry: 3                    ← try 3 more times after first failure
Retry interval: 60 sec      ← wait 60 seconds between retries
Timeout: 01:00:00           ← give up after 1 hour total
```

**Why retries matter:**  
Network errors, API rate limits (HTTP 429), and momentary database locks are transient — they fix themselves in seconds. Without retry, your pipeline fails on something that would have worked on the second attempt.

---

## 20. Deployment Strategy

### Why Multiple Environments Are Essential

In professional data engineering, you **never develop directly in production**.

| Risk without environments | What can go wrong |
|---|---|
| Testing on live data | Corrupt or duplicate real business data |
| Breaking production pipelines | Revenue loss, compliance breach |
| No rollback path | Cannot undo a bad deployment |
| Regulatory | Finance, healthcare, and banking require environment separation |

### Three-Environment Architecture

**Development (Dev):**
- Where all building and experimenting happens
- Connected to Dev storage accounts and Dev SQL databases (with fake/sample data)
- Developers work on feature branches
- Frequent changes, sometimes broken — that's okay

**Test / UAT (User Acceptance Testing):**
- Near-identical copy of Production
- Business users test new features here before they go live
- Connected to test databases with realistic (but anonymised) data
- Changes only arrive here after code review approval

**Production (Prod):**
- Live system serving real business users
- Real data — any corruption has business impact
- No developer can directly edit pipelines
- Changes only arrive through the automated CD pipeline
- Monitored 24/7 with alerts

**What changes between environments:**

| Component | Dev | Test | Prod |
|---|---|---|---|
| Pipeline code | Same | Same | Same |
| Linked Service URLs | dev-storage.blob.core.windows.net | test-storage.blob.core.windows.net | prod-storage.blob.core.windows.net |
| SQL Server | dev-sql.database.windows.net | test-sql.database.windows.net | prod-sql.database.windows.net |
| Trigger schedules | Disabled or frequent | Disabled or moderate | Active, production schedule |

ADF handles environment differences using **Global Parameters** — they override Linked Service URLs automatically when the ARM template is deployed to each environment.

---

## 21. What We Implemented — Full Summary

Here is everything we built in this assignment, in order:

### Azure Infrastructure
- ✅ Azure free account with $200 credit
- ✅ Resource Group: `ADF-Assignment-RG` (West Europe)
- ✅ Azure Blob Storage: `adfstorageahmed` with `input` and `output` containers
- ✅ Azure SQL Database: `ADF_DB` on server `adf-sql-ahmed`
- ✅ Azure Data Factory V2: `adf-assignment-ahmed`

### Linked Services
- ✅ `LS_AzureBlobStorage` — Account key authentication
- ✅ `LS_AzureSQL` — SQL authentication to ADF_DB
- ✅ `LS_REST_API` — Anonymous to JSONPlaceholder

### Datasets
- ✅ `DS_Source_CSV` — Blob input container, CSV format
- ✅ `DS_Sink_CSV` — Blob output container, CSV format
- ✅ `DS_SQL_Source` — employees_source table
- ✅ `DS_SQL_Watermark` — watermark_table
- ✅ `DS_REST_Posts` — /posts endpoint

### Pipelines Built
- ✅ `PL_Copy_Employees` — Basic copy activity, debug + manual trigger
- ✅ `PL_ForEach_MultiFile` — ForEach loop over 3 CSV files with dynamic naming
- ✅ `PL_Parameterized_Copy` — Parameters for source/sink file and container
- ✅ `PL_Incremental_Load` — 4-activity watermark pipeline
- ✅ `PL_REST_API_Load` — REST API to Blob with error handling paths

### Triggers
- ✅ `TR_Daily_Copy` — Schedule trigger, daily, Islamabad timezone

### SQL Objects
- ✅ `employees_source` table with `UpdatedAt` column
- ✅ `watermark_table` with initial value
- ✅ `usp_UpdateWatermark` stored procedure

### DevOps
- ✅ GitHub repository: `adf-assignment`
- ✅ ADF connected to GitHub, `main` as collaboration branch
- ✅ `adf_publish` branch with ARM templates
- ✅ `feature/advanced-pipelines` branch created

---

## 22. Key Expressions Reference

Quick reference for all expressions used in this assignment:

```javascript
// Read a pipeline parameter
@pipeline().parameters.fileName

// Read a pipeline variable
@variables('fileList')

// Current item in ForEach loop
@item()

// Output of a previous Lookup activity
@activity('Get_Old_Watermark').output.firstRow.WatermarkValue

// Current UTC date and time
@utcNow()

// Format date as YYYYMMDD
@formatDateTime(utcNow(), 'yyyyMMdd')

// Concatenate strings
@concat('output_', item())

// Build a dated filename
@concat('posts_', formatDateTime(utcNow(), 'yyyyMMdd'), '.json')

// Pipeline run ID (unique identifier)
@pipeline().RunId

// Compare two numbers (returns true/false)
@greater(10, 5)

// Check if string is empty
@empty(variables('myVar'))

// Length of an array
@length(variables('fileList'))
```

---

## 23. Common Errors & Solutions

| Error | Cause | Solution |
|---|---|---|
| `Connection failed` | Wrong account key or expired key | Manage → Linked Services → Edit → re-enter key → Test connection |
| `File not found` | Wrong container or filename spelling | Check dataset file path, verify file exists in Blob Storage |
| `Access denied` | Missing permissions | Storage Account → IAM → Add role → Storage Blob Data Contributor |
| `Invalid expression` | Syntax error in dynamic content | Check for missing quotes, brackets, or misspelled function names |
| `Timeout` | Pipeline ran too long | Increase timeout in General tab, or optimise query with indexes |
| `HTTP 429` | API rate limit hit | Increase retry interval to 60+ seconds, reduce parallelism |
| `Conversion failed` | Data type mismatch | Check column mapping in Copy activity Mapping tab |
| `Login failed for user` | Wrong SQL credentials | Manage → LS_AzureSQL → Edit → verify username and password |
| `Firewall block` | SQL server blocking ADF | SQL Server → Networking → Allow Azure services = ON |

---

## 24. Best Practices

### Naming Conventions
Always use consistent prefixes — it makes pipelines scannable:
```
PL_  → Pipelines     (e.g. PL_Copy_Employees)
DS_  → Datasets      (e.g. DS_Source_CSV)
LS_  → Linked Svc    (e.g. LS_AzureSQL)
TR_  → Triggers      (e.g. TR_Daily_Copy)
DF_  → Data Flows    (e.g. DF_Transform_Sales)
```

### Pipeline Design
- **Never hardcode values** — always use parameters or variables
- **One pipeline = one responsibility** — don't make one giant pipeline for everything
- **Always add retry** — at minimum 3 retries, 30 second interval
- **Always add error paths** — red arrows should go somewhere meaningful
- **Set timeouts** — a stuck pipeline should not run forever
- **Use Execute Pipeline** for reusable logic — don't copy-paste activities

### Performance
- **Use parallelism** in ForEach for independent files (set batch count to 4–8)
- **Incremental load** — never full load unless it's a small table
- **Use Binary copy** when you don't need to read file content — it's the fastest
- **Stage large copies** through Blob Storage for SQL loads

### Security
- **Never store secrets in pipeline parameters** — use Azure Key Vault
- **Use Managed Identity** for Linked Services where possible — no passwords
- **Minimum permissions** — give ADF only the access it needs
- **Private endpoints** in production — don't expose storage/SQL to public internet

### Git & Deployment
- **Always work on a feature branch** — never directly on main
- **Pull Request required** for all changes — peer review catches mistakes
- **Test in Dev first** — verify with real data before promoting
- **Global parameters** for environment-specific values

---

## 25. Glossary

| Term | Definition |
|---|---|
| **Activity** | A single step inside an ADF pipeline (e.g. Copy Data, Lookup, ForEach) |
| **ARM Template** | JSON file describing Azure resources — used for automated deployment |
| **Azure** | Microsoft's cloud computing platform with 200+ services |
| **Blob Storage** | Azure's object storage for unstructured files (CSV, JSON, images, etc.) |
| **CI/CD** | Continuous Integration / Continuous Deployment — automated testing and deployment pipeline |
| **Cloud computing** | Renting computing resources (servers, storage, databases) over the internet |
| **Dataset** | A named pointer to specific data within a Linked Service |
| **DIU** | Data Integration Unit — unit of compute power used by Copy activity |
| **Dynamic content** | ADF expression language using @ to insert values at runtime |
| **ELT** | Extract, Load, Transform — load raw data first, transform in destination |
| **ETL** | Extract, Transform, Load — clean data before loading to destination |
| **ForEach** | ADF activity that loops over an array of items |
| **Git** | Version control system that tracks changes to files over time |
| **IaaS** | Infrastructure as a Service — renting raw cloud servers |
| **Incremental load** | Copying only new/changed records since the last run |
| **Integration Runtime** | The compute engine that executes ADF activities |
| **Linked Service** | Saved connection details and credentials for an external system |
| **Lookup** | ADF activity that reads a single value from a source for use in the pipeline |
| **Managed Identity** | Azure feature allowing services to authenticate without passwords |
| **Parameter** | A value passed into a pipeline at runtime — read-only during run |
| **PaaS** | Platform as a Service — renting a managed platform to build on |
| **Pipeline** | A logical container of activities that together perform a data task |
| **Region** | A geographic location where Azure has data centres |
| **Resource Group** | A logical container grouping related Azure resources |
| **REST API** | Standard HTTP-based interface for systems to communicate |
| **SaaS** | Software as a Service — using software over the internet (Gmail, M365) |
| **Schedule Trigger** | Runs a pipeline automatically at set times |
| **Sink** | The destination where ADF writes data to |
| **Source** | The origin where ADF reads data from |
| **Stored Procedure** | Pre-written SQL code stored in a database and called by name |
| **Subscription** | Azure billing account that resources are created under |
| **Trigger** | Defines when an ADF pipeline runs |
| **Variable** | A value that can be read and updated during a pipeline run |
| **Watermark** | A timestamp storing the last successfully processed record's date |

---

## 📁 Repository Structure

```
adf-assignment/
│
├── pipeline/
│   ├── PL_Copy_Employees.json
│   ├── PL_ForEach_MultiFile.json
│   ├── PL_Parameterized_Copy.json
│   ├── PL_Incremental_Load.json
│   └── PL_REST_API_Load.json
│
├── dataset/
│   ├── DS_Source_CSV.json
│   ├── DS_Sink_CSV.json
│   ├── DS_SQL_Source.json
│   ├── DS_SQL_Watermark.json
│   └── DS_REST_Posts.json
│
├── linkedService/
│   ├── LS_AzureBlobStorage.json
│   ├── LS_AzureSQL.json
│   └── LS_REST_API.json
│
├── trigger/
│   └── TR_Daily_Copy.json
│
└── README.md   ← You are here
```

---

## 👤 Author

**[M Ahmad]**  
 
Azure Data Factory Assignment — Beginner to Advanced  

---

