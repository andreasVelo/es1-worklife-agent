
## Latest Release Notes [Rel Notes](https://github.com/andreasVelo/es1-worklife-agent/releases/latest)

[![Download Latest](https://img.shields.io/badge/Download-Latest-blue?style=for-the-badge)](https://github.com/andreasvelo/es1-worklife-agent/releases/latest/download/es1worklifeagent-setup.exe)


# ES1 WorkLife Agent

## Overview

ES1 WorkLife Agent is a background windows service that schedules and runs workplace integration tasks (attendance sync, device polling, and small automation jobs).

### Quick Start Guide

>Installation

1. Download and run the setup application
2. Enter the API Key that was given for the installation
3. Complete the Installation

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

>**Register a Device**

- Go to HCM Portal and add the Device
- type __devices 1 --state__ where 1 is the No of the Device
- user the arrow keys and select 'Active' after the device is registered on the HCM

The device now is ready to receive attendance events and forward the events to the HCM System.

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
| `devices` | Manage devices (use `devices --help` for `--info` and `--state <index>`). |
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

