## Latest Release Notes [Rel Notes](https://github.com/andreasVelo/es1-worklife-agent/releases/latest)

[![Download Latest](https://img.shields.io/badge/Download-Latest-blue?style=for-the-badge)](https://github.com/andreasvelo/es1-worklife-agent/releases/latest/download/es1worklifeagent-setup.exe)

# ES1 WorkLife Agent


## Overview

ES1 WorkLife Agent is a background windows service that schedules and runs workplace integration tasks (attendance sync, device polling, and small automation jobs).

### Quick Start Guide

>PreRequisites

- **System Requirements:**
  - Windows 10 or later (or Windows Server 2016 and later)
  - 4 GB minimum RAM
  - 500 MB minimum disk space
  - Administrator privileges for installation

- [Firewall Configuration](#firewall-configuration)

>Installation

1. Download and run the setup application
2. Enter the API Key that was given for the installation
3. Complete the Installation

>Silent Installation
Command Line :  /VERYSILENT /SUPPRESSMSGBOXES  /APIKEY=%TEST_API_KEY%

The default installation folder is [Program Files]\ES1WorklifeAgent

>**Connect a Time Attendance Device**

1. ZKTeko

- Go to Communication menu
- Go to Cloud Server
- Enter the IP of the machine where the agent is installed
- Enter 8081 for port
- Use HTTP

>**View Device Information**

- Open the ES1.Worklife.Cli.exe with administrative rights from the installation folder
- type  __devices --info__ and press enter. The connected devices will be returned.

>**Add  a Device**

- Go to HCM Portal and add the Device
- Open the ES1.Worklife.Cli.exe with administrative rights
- Type __devices --new__ to add a device 
- Use __devices --setlastatt <deviceId>__ to set the initial sync position (last attendance time) if needed

>** Register a Device**
- type __devices 1 --state__ where 1 is the No of the Device
- user the arrow keys and select 'Active' after the device is registered on the HCM

The device now is ready to receive attendance events and forward the events to the HCM System.

>**Test Data Provider (File or Database)**

- Type __devices --test__ to validate connectivity and retrieve sample attendance records from a File or Database provider
- Useful for troubleshooting configuration issues before enabling the device for production



**Basic Commands**

| Command | Description |
|:---|:---|
| `install` | Install the agent as a Windows service |
| `uninstall` | Uninstall the Windows service |
| `start` | Start the Windows service |
| `stop` | Stop the Windows service |
| `restart` | Restart the Windows service |
| `status` | Show service status  |
| `job` | Job management (use `job --help` for subcommands: `--list`, `--start`, `--stop`, `--enable`, `--disable`, `--schedule`, `--history`). |
| `update` | Check for available updates and install them via the agent. |
| `db` | Database utilities (use `db --help` for subcommands such as `--info` and `--list <table>`). |
| `devices --info` | Show connected devices with status, connection state, health, and last ping time. |
| `devices --new` | Add a new device. Prompts for device type (ZKTeco, Hundure, Database, or File), IP/port for hardware devices, query/file settings for data providers, and initial sync positions (last attendance time and file record ID). |
| `devices --show <deviceId>` | Show detailed information about a specific device, including configuration, firmware version, and any error messages. |
| `devices --state <deviceId>` | Change device connection state (Active, Inactive, Suspended, or Test). |
| `devices --setlastatt <deviceId>` | Set the last attendance timestamp and file record position for a device. Displays current values and allows pressing Enter to skip updates. Useful for resetting sync positions after data corrections. |
| `devices --test` | Test connectivity and sample data retrieval for File or Database providers. Allows selecting a provider, entering starting position, and viewing retrieved attendance records. |
| `devices --delete <deviceId>` | Delete a device and all associated configuration. |
| `events --info` | Show events summary: counts, oldest pending event, top events and batches. |
| `config` | Edit runtime settings. Use `--show` to display settings only. |
| `help` | Show the CLI help text. |
| `quit` / `q` | Exit interactive mode (when running CLI with no args). |

Note: Most top-level commands expose subcommands or flags; run the command with `--help` to see available options (for example `job --help` or `devices --help`).

---
>Device Status

| Status | Description |
|:---|:---|
| Online | The device is reachable and responding to requests |
| Offline | The device is not reachable on the network or turned off |
| Error | The device reported an error or failed to respond; check logs |

---
 >Connection State (HCM)

| State | Description |
|:---|:---|
| Active | Registered with the backoffice HCM and actively exchanging data |
| Inactive | Not registered or not currently connected to the backoffice HCM |
| Suspended | Registration/communication is paused for maintenance or administrative hold |
| Test | Registered in test mode |

## Firewall Configuration

To ensure proper communication between the ES1 Worklife Agent and connected devices, the following ports must be open in your firewall:

| Port | Service | Protocol | Direction | Description |
| :--- | :--- | :--- | :--- | :--- |
| 8081 | ES1 Worklife Agent (Cloud Server) | HTTP/TCP | Inbound | Allows ZKTeco devices to connect to the agent and transmit attendance events |
| 4370 | ZKTeco Device Communication | TCP/UDP | Outbound | Allows the agent to communicate with ZKTeco devices for polling and data synchronization |


**Configuration**

- **Config folder**: `ES1.Worklife.Agent/config/` — all agent configuration is stored in this folder.
- **Format**: Configuration files must be provided in YAML only; JSON is not used by the agent.

**Database Settings**

| Setting | Description | Values / Notes |
|:---|:---|:---|
| `Name` | Unique identifier for the query definition | string |
| `DatabaseType` | Type of database system | SqlServer, SQLite, Oracle, MySQL, Odbc |
| `ConnectionString` | Database connection string used by the provider | string (DB-specific) |
| `Query` | SQL text executed to retrieve attendance rows | string |
| `UseId` | If true, use numeric Id for incremental reads; if false, use timestamp | boolean |

**Query Requirements**

| Requirement | Description |
|:---|:---|
| Column aliases | The `SELECT` list must alias each column to the target field name the agent expects (use the column alias, not an ordinal). |
| Column order | The order of columns in the `SELECT` list should match the expected mapping order so the provider can map values consistently. |
| WHERE clause | The incremental filter in the `WHERE` clause must reference the same column (or its alias) used for incremental tracking (Id or timestamp) and use the provider's parameter placeholder syntax. |

**Required Column Aliases**

| Alias | Description |
|:---|:---|
| `Id` | Numeric incremental identifier for the attendance row |
| `UserId` | Employee or user identifier |
| `Device` | Device identifier or name |
| `CardNumber` | Card or badge number |
| `Timestamp` | UTC timestamp for the attendance event (datetime) |
| `Punch` | Attendance event type/code (numeric or mapped) |

**WHERE parameter names**

| Mode | Logical parameter name | Common provider placeholders |
|:---|:---:|:---|
| Id-based (UseId = true) | `lastId` | SQL Server / MySQL / SQLite: `@lastId` · Oracle: `:lastId` · ODBC: `?` (pass `lastId` value) |
| Timestamp-based (UseId = false) | `timeStamp` | SQL Server / MySQL / SQLite: `@timeStamp` · Oracle: `:timeStamp` · ODBC: `?` (pass `timeStamp` value) |

Note: The agent binds the logical parameter name (`lastId` or `timeStamp`) according to the provider; use the provider-specific placeholder style in the query text but supply the corresponding logical value when executing.

**File Settings**

| Setting | Description | Values / Notes |
|:---|:---|:---|
| `Name` | Identifier for the file definition | string |
| `FileType` | File format used to parse records | TabDelimited, CustomDelimited, FixedWidth, CSV |
| `Delimiter` | Delimiter character (for `CustomDelimited`) | single-character string (e.g. , ; |) |
| `FilePath` | Absolute or relative path to file(s) | path |
| `FilePattern` | Glob/wildcard pattern to match files | e.g. `attendance_*.txt` |
| `SkipHeaderRows` | Number of header rows to skip before parsing | integer (>= 0) |
| `Columns` | List of column definitions (see table below) | list |


**Column Definition Fields**

Common fields (apply to all formats):

| Field | Description | Values / Notes |
|:---|:---|:---|
| `ColumnName` | Human-readable name for this column (for logging and debugging) | string |
| `MapsTo` | Target field on the attendance model (e.g., `UserId`, `DeviceId`, `Timestamp`, `Punch`) | string |
| `Format` | Parse format or type (when needed) | examples: `yyyy-MM-dd HH:mm:ss`, `int`, `guid`, `boolean` |
| `MapValues` | Value mapping used to transform incoming values | comma-separated pairs, e.g. `In=1,Out=2` |
| `IsRequired` | Whether this column must be present for a valid record | boolean |

Delimited formats (TabDelimited, CustomDelimited, CSV):

| Field | Description | Values / Notes |
|:---|:---|:---|
| `ColumnIndex` | 1-based column index for delimited formats; agent converts to 0-based internally | integer (1..N) |

Fixed-width format:

| Field | Description | Values / Notes |
|:---|:---|:---|
| `StartPosition` | 0-based start position (character index) for fixed-width parsing | integer (0-based) |
| `Length` | Number of characters to read at `StartPosition` | integer |
   netstat -aon | findstr :8081
   tasklist /FI "PID eq <PID>"
