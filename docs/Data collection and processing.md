# ANEEL Public Data Collection for Energy Quality and Loss Analysis

## Summary

As a user of the Tecsys energy analytics system, I want the system to automatically collect public ANEEL data on energy quality and losses so that this information can be stored and later used to generate analyses, indicators, and rankings for the electricity sector.

## 1. Introduction

### 1.1 Business Logic and Rules

The system must automatically collect public data made available by the Brazilian National Electric Energy Agency (ANEEL), using official open datasets published by the regulatory agency.

The collected data will be used to analyze and generate comparative indicators across:

- distributors
- network regions
- electrical groups

For the current project scope, the datasets used are public and regulatory in nature, intended for electricity sector analysis, with no planned intentional processing of personal data in the main content ingested by the application.

Even so, the collection process must include structural validation of source files, with rejection or blocking of fields not defined in the application's data model, especially if there is any possibility of unexpected inclusion of data incompatible with the system's purpose.

The collection must occur automatically, ensuring periodic updates of the data used in analyses.

For this phase of the project, a quarterly update is recommended, which is sufficient to keep analyses current without generating excessive ingestion workload.

The system must also allow manual execution of the collection by authorized administrators when necessary for historical data import, reprocessing, or correction of inconsistencies.

Throughout the process, the system must record collection execution logs, allowing traceability of:

- collection start
- data processing
- successful completion
- failures during the process
- manual import execution

### 1.2 Purpose of the Data Collection

The collected data must be used exclusively for:

- regulatory analysis
- indicator generation
- comparison between distributors and regions
- creation of rankings and analytical dashboards for the electricity sector

The data must not be used for purposes incompatible with the project scope.

### 1.3 Governance and Scope Delimitation

The system must maintain logical separation between:

- regulatory data collected from ANEEL
- application operational data
- process execution logs

If the collection process involves manual execution by an administrator, the operational records of that action may generate logs containing a reference to the system operator, exclusively for auditing, security, and traceability purposes.

These operational records must not be confused with the regulatory content collected from ANEEL.

## 2. Indicators Used

The collected data contains regulatory indicators of electricity supply quality, such as DEC and FEC, which are widely used in the electricity sector.

**DEC**  
Equivalent Duration of Interruption per Consumer Unit.  
Represents the average amount of time consumers were without electricity during a given period.

**FEC**  
Equivalent Frequency of Interruption per Consumer Unit.  
Represents how many times power interruptions occurred for consumers.

These indicators are reported monthly by distributors to ANEEL and allow evaluation of service quality.

For the initial phase of the project, it is recommended to prioritize analysis of these two indicators, since they directly represent service continuity.

Data related to energy losses may also be collected and stored, but deeper analysis of those metrics may be addressed in later stages.

## 3. Data Sources

The system must collect data from the following public sources:

**Collective continuity indicators (DEC and FEC)**  
[https://dadosabertos.aneel.gov.br/dataset/indicadores-coletivos-de-continuidade-dec-e-fec](https://dadosabertos.aneel.gov.br/dataset/indicadores-coletivos-de-continuidade-dec-e-fec)

**Distributor Geographic Database - BDGD**  
[https://dadosabertos.aneel.gov.br/dataset/base-de-dados-geografica-da-distribuidora-bdgd](https://dadosabertos.aneel.gov.br/dataset/base-de-dados-geografica-da-distribuidora-bdgd)

**Electric energy loss data**  
[https://portalrelatorios.aneel.gov.br/luznatarifa/perdasenergias](https://portalrelatorios.aneel.gov.br/luznatarifa/perdasenergias)

The data is usually available in CSV, spreadsheet, or equivalent formats, containing historical records by period and distributor.

## 4. Collected Information

To support analysis and ranking generation, the system must store the following information:

| Field | Type | Required | Notes |
|---|---|---|---|
| Year | Integer | Yes | Reference year |
| Month | Integer | No | When available |
| Distributor | Text | Yes | Responsible company |
| State | Text | No | Served region |
| Electrical Group | Text | Yes | Group of consumer units |
| Substation | Text | No | When available |
| Measured DEC | Decimal | Yes | Actual recorded value |
| DEC Limit | Decimal | No | Regulatory limit |
| Measured FEC | Decimal | Yes | Actual frequency |
| FEC Limit | Decimal | No | Regulatory limit |
| Technical Losses | Decimal | No | Energy lost in the grid |
| Non-Technical Losses | Decimal | No | For example, energy theft |
| Collection Date | DateTime | Yes | Collection record timestamp |
| Source | Text | Yes | Dataset or source URL |
| Import Batch | Text | Yes | Identifier of the collection run |

This information will enable historical analysis and comparisons across distributors, electrical groups, and network regions.

## 5. Data Collection Strategy

The system must collect data automatically, ensuring that the indicators used in analyses remain up to date.

### 5.1 Automatic Collection

Automatic collection must occur at scheduled intervals.

For this phase of the project, a quarterly execution schedule is recommended.

This process may be implemented through an automated schedule such as a cron job or an equivalent mechanism.

Example flow:

- check for updates in ANEEL datasets
- download available files
- validate file format and structure
- process and normalize the data
- store the records
- record execution logs

## 6. Data Processing

After collecting the data, the system must:

- validate the file format
- validate the expected column structure
- reject or flag files with incompatible structure
- extract relevant information
- normalize and standardize the data according to the system model
- store the records in the database
- record collection execution logs

The system must accept only fields defined in the ingestion model. Unexpected fields or fields incompatible with the application's purpose must be rejected, ignored with traceability, or sent for technical review according to the project's policy.

The data must be organized in a way that supports querying and aggregation by:

- period
- distributor
- electrical group
- network region

If certain years, regions, or electrical groups do not have available data in the public datasets, the system must not remove valid historical records from the analysis.

In such cases, the data must be presented with an appropriate status, such as:

- missing data

This approach avoids analytical distortion and preserves the historical consistency of the information.

### 6.1 Duplicate Control and Reprocessing

The system must provide a strategy to prevent improper duplication of records during recurring collections.

The ingestion flow must allow controlled reprocessing, with identification of the import batch and a safe update rule for records, preserving historical consistency and traceability of the data origin.

## 7. Collection Logging

The system must record logs for each data collection execution.

Events that must generate logs:

- start of the collection process
- start of processing
- successful completion of the collection
- collection failure
- processing failure
- manual file import
- file rejection due to invalid structure
- batch reprocessing

These records must support technical auditing and failure analysis in the ingestion process.

### 7.1 Data Collection Governance

The collected data comes from official public datasets and must be used exclusively for analysis and indicator generation in the electricity sector.

The system must maintain control over collection and processing execution through log records, ensuring traceability of operations and facilitating identification of ingestion failures.

Whenever possible, each collection run must record:

- batch identifier
- source of origin
- date and time of execution
- number of processed records
- number of imported records
- number of rejected records
- final execution status

## 8. Acceptance Criteria

- The system must automatically collect ANEEL datasets.
- Automatic collection must run periodically through scheduling.
- The recommended collection frequency is quarterly.
- The system must extract DEC and FEC indicators.
- The system must store the collected data in the database.
- The system must record collection execution logs.
- The system must validate file format and structure before processing the data.
- The system must allow manual collection execution only by authorized administrators.
- The system must record the source, collection date, and import batch of processed data.
- The system must reject, flag, or otherwise handle in a controlled way any fields not defined in the ingestion model.
- The system must allow this data to be used for analysis and ranking generation.
- If data for a given period or region is unavailable, the system must record that information as missing, unavailable, or outdated without removing valid historical records from the analysis.
- The system must provide a mechanism to avoid improper duplication during reprocessing or new collection runs.

## 9. Links and References

### 9.1 References

| Item | Link |
|---|---|
| Prototype | [Figma](https://www.figma.com/design/lH1iaR2cBfTC9PUxxo93dX/Sem-t%C3%ADtulo?node-id=0-1&m=dev) |
| Epic (Jira) | [ATS-5: Data collection and processing](https://athos-software.atlassian.net/browse/ATS-5) |

Se quiser, eu também posso converter os 3 textos em um padrão único de GitHub, por exemplo:
- `User Story`
- `Business Rules`
- `Acceptance Criteria`
- `References`

Isso deixaria tudo mais consistente para colar no Jira ou GitHub.
