<div align="center">

# IBM Maximo 7.x, 8.x, MAS 9.x — API Collections

### The most comprehensive Postman collection suite for IBM Maximo Application Suite APIs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Postman](https://img.shields.io/badge/Postman-v2.1-FF6C37?logo=postman&logoColor=white)](https://www.postman.com/)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-3.0-6BA539?logo=openapi-initiative&logoColor=white)](https://swagger.io/specification/)
[![Maximo Version](https://img.shields.io/badge/Maximo-7.x%20|%208.x%20|%20MAS%209.x-054ADA?logo=ibm&logoColor=white)](https://www.ibm.com/products/maximo)
[![Endpoints](https://img.shields.io/badge/Endpoints-2%2C439+-brightgreen)]()
[![Scripts](https://img.shields.io/badge/Automation%20Scripts-29-blue)]()
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

**2,439+ endpoints** across **14 modules** + **29 production-ready automation scripts** with a fully automated deployment pipeline.

[Quick Start](#-quick-start) · [Automation Scripts](#-automation-scripts) · [Deployment](#-deployment) · [API Patterns](#-api-patterns) · [Contributing](#-contributing)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Quick Start](#-quick-start)
- [Collection Generations](#collection-generations)
- [Automation Scripts](#-automation-scripts)
- [Deployment](#-deployment)
- [API Patterns](#-api-patterns)
- [Module Coverage](#-module-coverage)
- [Authentication](#-authentication)
- [Project Structure](#-project-structure)
- [API Behavior Notes](#-api-behavior-notes)
- [Troubleshooting](#-troubleshooting)
- [FAQ](#-faq)
- [Security](#-security)
- [Contributing](#-contributing)
- [License](#-license)

---

## Overview

Production-ready Postman collections and automation scripts for IBM Maximo Application Suite (MAS) APIs. Covers both the legacy **OSLC** and modern **NextGen REST** API patterns, plus a library of 29 Jython automation scripts deployable via API.

### Key Metrics

| Metric | Value |
|--------|-------|
| **Collection Generations** | 2 (Original + MAS 9) |
| **Total Modules** | 14 (of 18 discovered) |
| **Original OSLC Endpoints** | 738 |
| **Original REST Endpoints** | 570 |
| **MAS 9 OSLC Requests** | ~1,011 |
| **MAS 9 REST Requests** | ~1,131 |
| **Automation Scripts** | 29 (12 object, 9 field, 4 action, 3 cron, 1 integration) |
| **Deployment Requests** | 120 (deploy + verify + update + rollback + cron setup) |

---

## Quick Start

### Prerequisites

- [Postman](https://www.postman.com/) desktop or web app (or [Newman](https://www.npmjs.com/package/newman) for CLI)
- IBM Maximo MAS 9.x instance with API access enabled
- API key with appropriate permissions

### 1. Import Collections into Postman

```bash
# For MAS 9 collections (recommended)
File > Import > Select all files from postman-mas9-oslc/ or postman-mas9-rest/

# For original 7.x/8.x collections
File > Import > Select all files from postman-oslc/ or postman-rest/
```

### 2. Set Up Environment

Create a Postman environment with these variables:

| Variable | Value | Type |
|----------|-------|------|
| `baseUrl` | `https://your-instance.maximo.com/maximo` | default |
| `apikey` | `your-api-key-here` | secret |

### 3. Make Your First Request

```
GET {{baseUrl}}/api/os/mxapiwodetail?oslc.select=wonum,description,status&oslc.pageSize=5&lean=1
```

---

## Collection Generations

This project contains **two generations** of Postman collections:

| Generation | Directories | Target | Modules | Notes |
|-----------|-------------|--------|---------|-------|
| **Original** | `postman-oslc/`, `postman-rest/` | Maximo 7.x / 8.x | 14 | Auto-generated from OAS3 specs |
| **MAS 9** | `postman-mas9-oslc/`, `postman-mas9-rest/` | MAS 9.x | 13 + deployment | Enhanced with admin, setup, and script deployment |

### MAS 9 Collections

The MAS 9 collections include significantly more requests per module and add new capabilities:

| Collection | Requests | Description |
|-----------|----------|-------------|
| `ADMIN.json` | 38 | Script execution, CI/CD deployment lifecycle |
| `SETUP.json` | 195 | Full administration (domains, scripts, cron, actions) |
| `WO.json` | 165 | Work Orders with 38+ action calls |
| `ASSET.json` | 196 | Asset management |
| `INVENTOR.json` | 81 | Inventory management |
| `PURCHASE.json` | 84 | Purchase orders and requests |
| `SD.json` | 50 | Service Desk |
| `UTIL.json` | 110 | System configuration and utilities |
| `PLANS.json` | 19 | Job Plans |
| `PM.json` | 19 | Preventive Maintenance |
| `CONTRACT.json` | 18 | Contracts |
| `FINANCIAL.json` | 18 | Financial |
| `INT.json` | 18 | Integration |
| `autoscripts.json` | 120 | Unified deployment pipeline (REST only) |

---

## Automation Scripts

### Demo Script Library (29 Scripts)

A production-grade library of Jython automation scripts organized by type:

#### Object Scripts (12)

| Script | Object | Event | Description |
|--------|--------|-------|-------------|
| `workorder.obj.save.before` | WORKORDER | Before Save | Auto-populate priority/dates for CM/EM work types |
| `workorder.obj.statuschange.before` | WORKORDER | Before Save | 6-state transition matrix, child WO checks, labor/material validation |
| `workorder.obj.statuschange.after` | WORKORDER | After Save | KPI calculation (response time, wrench time, schedule compliance) |
| `asset.obj.save.before` | ASSET | Before Save | Install date validation, depreciation, parent location sync |
| `locations.obj.init` | LOCATIONS | Initialize | Hierarchy path generation, ORGID/SITEID defaulting |
| `pm.obj.save.before` | PM | Before Save | Frequency/job plan validation, NEXTDATE calculation |
| `po.obj.save.before` | PO | Before Save | Total cost calculation, vendor validation |
| `po.obj.statuschange.before` | PO | Before Save | 3-way match (PO/receipt/invoice), budget reservation |
| `po.obj.statuschange.after` | PO | After Save | PR linkage sync, vendor performance scorecard |
| `pr.obj.save.before` | PR | Before Save | Required date auto-set, sourcing validation |
| `pr.obj.statuschange.before` | PR | Before Save | 4-tier approval authority engine |
| `pr.obj.statuschange.after` | PR | After Save | Auto PO generation, budget commit/release |

#### Field Validators (9)

| Script | Object | Attribute | Description |
|--------|--------|-----------|-------------|
| `workorder.fld.status.validate` | WORKORDER | STATUS | Block COMP/CLOSE/APPR based on child WOs |
| `workorder.fld.status.action` | WORKORDER | STATUS | Dynamic READONLY/REQUIRED on 20+ fields per status |
| `asset.fld.serialnum.validate` | ASSET | SERIALNUM | Regex format validation, uniqueness check |
| `locations.fld.streetaddress.validate` | LOCATIONS | STREETADDRESS | Required address for COURIER/STOREROOM types |
| `pm.fld.frequency.validate` | PM | FREQUENCY | Range validation per FREQUNIT |
| `po.fld.totalcost.validate` | PO | TOTALCOST | Non-negative check, vendor credit limit |
| `po.fld.status.action` | PO | STATUS | Field group locking, budget impact preview |
| `pr.fld.requireddate.validate` | PR | REQUIREDDATE | Past/future date limits, priority-based lead times |
| `pr.fld.status.action` | PR | STATUS | Editability matrix, approval summary |

#### Action Scripts (4)

| Script | Object | Description |
|--------|--------|-------------|
| `workorder.action.notify` | WORKORDER | Email supervisor with WO details |
| `workorder.action.escalation` | WORKORDER | Tiered escalation (email, priority bump, manager notify) |
| `asset.action.downtime` | ASSET | Calculate/record downtime hours, create meter readings |
| `pr.action.autoapprove` | PR | Auto-approve PRs under threshold with segregation of duties |

#### Cron Tasks (3)

| Script | Schedule | Description |
|--------|----------|-------------|
| `cron.wo.autoclose` | Daily | Auto-close completed WOs after configurable days |
| `cron.pm.generate.wo` | Daily | Generate WOs from active PMs with job plan resources |
| `cron.inv.reorder` | Daily | Check stock levels, auto-create PRs grouped by vendor |

#### Integration Scripts (1)

| Script | Direction | Description |
|--------|-----------|-------------|
| `publish.mxpo.extexit.out` | Outbound | MXPO ERP integration with field mapping and retry |

---

## Deployment

### Automated Deployment with Newman

The `autoscripts.json` collection provides a complete CI/CD pipeline for deploying all 29 scripts via API.

#### Install Newman

```bash
npm install -g newman
```

#### Deploy All Scripts

```bash
# Step 1: Deploy scripts and launch points
newman run postman-mas9-rest/autoscripts.json \
  -e your-environment.json \
  --folder "1. Deploy All Scripts"

# Step 2: Set up cron task definitions
newman run postman-mas9-rest/autoscripts.json \
  -e your-environment.json \
  --folder "5. Cron Task Setup"
```

#### Other Operations

```bash
# Verify all scripts exist
newman run postman-mas9-rest/autoscripts.json \
  -e your-environment.json \
  --folder "2. Verify All"

# Update source code on existing scripts
newman run postman-mas9-rest/autoscripts.json \
  -e your-environment.json \
  --folder "3. Update Source Code"

# Rollback (delete all scripts)
newman run postman-mas9-rest/autoscripts.json \
  -e your-environment.json \
  --folder "4. Rollback (Delete)"
```

### Deployment Pipeline Folders

| Folder | Requests | Description |
|--------|----------|-------------|
| 1. Deploy All Scripts | 29 | Creates script + launch point(s) in one API call |
| 2. Verify All | 29 | Confirms each script exists with its launch point |
| 3. Update Source Code | 29 | Push source changes to existing scripts |
| 4. Rollback (Delete) | 29 | Delete scripts and their launch points |
| 5. Cron Task Setup | 3 | Create cron task definitions |

### Post-Deployment: Launch Point Fix

The Maximo REST API has a known limitation where `objectevent` values are recomputed from boolean flags, preventing direct setting of After Save (objectevent=7) timing. After deploying, a utility script (`UTIL.FIX.LAUNCHPOINT`) can be deployed and executed to fix this via direct SQL.

See [API Behavior Notes](#-api-behavior-notes) for details.

---

## API Patterns

Maximo exposes two API patterns for the same resources. Both are fully covered.

### OSLC API (`/oslc/os/`)

```
GET    /oslc/os/{resource}          # List (paginated)
GET    /oslc/os/{resource}/{id}     # Get by ID
POST   /oslc/os/{resource}          # Create
POST   /oslc/os/{resource}/{id}     # Update (x-method-override: PATCH)
DELETE /oslc/os/{resource}/{id}     # Delete
POST   /oslc/script/{scriptName}    # Execute automation script
```

### REST API (`/api/os/`)

```
GET    /api/os/{resource}           # List (paginated)
GET    /api/os/{resource}/{id}      # Get by ID
POST   /api/os/{resource}           # Create
PATCH  /api/os/{resource}/{id}      # Update (native PATCH — returns 501, use OSLC method)
DELETE /api/os/{resource}/{id}      # Delete
POST   /api/script/{scriptName}     # Execute automation script
```

> **Note:** Native `PATCH` returns `501 Not Implemented` on MAS 9. Use `POST` with `x-method-override: PATCH` and `patchtype: MERGE` headers instead.

### Common Query Parameters

| Parameter | Example | Description |
|-----------|---------|-------------|
| `oslc.select` | `wonum,description,status` | Select specific fields |
| `oslc.where` | `status="APPR"` | Filter records |
| `oslc.pageSize` | `20` | Records per page |
| `oslc.orderBy` | `+wonum` | Sort order (`+` asc, `-` desc) |
| `lean` | `1` | Compact response (recommended) |
| `collectioncount` | `1` | Include total record count |

---

## Module Coverage

### Tier 1 — Core Operations

| Module | Code | OSLC | REST | Primary Resource |
|--------|------|:----:|:----:|------------------|
| Work Orders | WO | 166 | 166 | `mxapiwodetail` |
| Assets | ASSET | 118 | 118 | `mxapiasset` |
| Inventory | INVENTOR | 60 | 60 | `mxapiinventory` |
| Service Desk | SD | 52 | 52 | `mxapisr` |

### Tier 2 — Extended Operations

| Module | Code | OSLC | REST | Primary Resource |
|--------|------|:----:|:----:|------------------|
| Purchasing | PURCHASE | 45 | 45 | `mxapipo`, `mxapipr` |
| Utilities | UTIL | 57 | 57 | `mxapiperson` |
| Preventive Maintenance | PM | 6 | 6 | `mxapipm` |
| Job Plans | PLANS | 11 | 11 | `mxapijobplan` |

### Tier 3 — Administrative & Financial

| Module | Code | OSLC | REST | Primary Resource |
|--------|------|:----:|:----:|------------------|
| Financial | FINANCIAL | 62 | 20 | `mxapicoa` |
| Contracts | CONTRACT | 52 | 10 | `mxapicontract` |
| IT Infrastructure | CI | 52 | 10 | `mxapiauthci` |
| Company | COMPANY | 47 | 5 | `mxapicommodity` |
| Analytics | ANALYTICS | 5 | 5 | `mxapikpigraphic` |
| Scheduler | SCHEDULER | 5 | 5 | `mxapilbslocation` |

---

## Authentication

All collections use API key authentication via the `apikey` HTTP header:

```
Header: apikey: <your-api-key>
```

| Mode | Header | Use Case |
|------|--------|----------|
| **API Key** (recommended) | `apikey: your-key` | Automation, CI/CD |
| **Basic Auth** | `Authorization: Basic base64(user:pass)` | Development |
| **LTPA Token** | `Cookie: LtpaToken2=...` | Browser sessions |

---

## Project Structure

```
maximo-interfaces/
├── README.md
├── CONTRIBUTING.md
├── LICENSE
├── .gitignore
├── maximo-api-catalog.json              # 18-module API catalog
│
├── postman-oslc/                        # Original OSLC collections (14 modules)
├── postman-rest/                        # Original REST collections (14 modules)
│
├── postman-mas9-oslc/                   # MAS 9 OSLC collections (13 modules)
│   ├── ADMIN.json                       #   Script execution & CI/CD deployment
│   ├── SETUP.json                       #   Full administration (195 requests)
│   ├── WO.json                          #   Work Orders (165 requests)
│   └── ...                              #   10 more module files
│
├── postman-mas9-rest/                   # MAS 9 REST collections (13 + deployment)
│   ├── autoscripts.json                 #   Unified deployment pipeline (120 requests)
│   └── ...                              #   13 module files (same as OSLC)
│
├── autoscripts/                         # Automation scripts (git-ignored)
│   └── demo-library/                    #   29-script demo library
│       ├── object_scripts/              #     12 object event scripts
│       ├── field_validators/            #     9 attribute validators
│       ├── action_scripts/              #     4 action scripts
│       ├── cron_tasks/                  #     3 scheduled cron scripts
│       └── integration_scripts/         #     1 outbound integration
│
├── postman-mas9-environment/            # Postman environment (git-ignored)
└── api-discovery/                       # OAS3 specs & discovery (git-ignored)
```

---

## API Behavior Notes

### PATCH Returns 501

MAS 9 REST API does not support native HTTP `PATCH`. Use `POST` with headers:
```
x-method-override: PATCH
patchtype: MERGE
```

### Launch Point Event Codes

The `mxapiautoscript` API uses `SCRIPTLAUNCHPOINT.OBJECTEVENT` (INTEGER) to control script timing:

| objectevent | Meaning | How to Set via API |
|:-----------:|---------|-------------------|
| 0 | None | Default for ACTION/ATTRIBUTE types |
| 1 | Initialize | `initialize: true` in launch point body |
| 4 | Save (generic) | `update: true` only |
| 6 | Before Save | `add: true` + `update: true` + `beforesave: true` |
| 7 | After Save | **Cannot be set via API** — requires direct SQL |
| 8 | After Commit | Set via `attributeevent: 1` (Run Action) |

**Known limitation:** The `mxapiautoscript` Application Object Structure recomputes `objectevent` from boolean flags during MBO save. The `aftersave` and `runaction` boolean flags are silently ignored. Use numeric `attributeevent` for attribute launch points.

### SCRIPTLAUNCHPOINT Column Names

The `SCRIPTLAUNCHPOINT` table in SQL Server uses lowercase column names. `ADD` is a reserved word — use bracket syntax `[ADD]` in raw SQL queries.

### Launch Point Name Length

`SCRIPTLAUNCHPOINT.LAUNCHPOINTNAME` has a **30-character maximum**. Plan naming conventions accordingly.

---

## Troubleshooting

### Connection Issues

- Verify `{{baseUrl}}` includes the full path: `https://your-instance.maximo.com/maximo`
- Check network/VPN connectivity
- Increase request timeout in Postman Settings

### Authentication Errors (401)

- Verify API key is valid and not expired
- Ensure the `apikey` header name matches your instance configuration
- Confirm the Postman environment variable `{{apikey}}` is set

### OSLC Query Errors (400)

- Enclose string values in double quotes: `status="WAPPR"`
- Check OSLC `where` clause syntax
- Test with a simple query first

### Script Deployment Errors (400)

- **Duplicate script name** — Script already exists. Use the Update folder instead.
- **Launch point name too long** — Max 30 characters. Shorten the name.
- **Invalid attribute** — The attribute doesn't exist on the target object in your instance.

---

## FAQ

<details>
<summary><strong>Which Maximo versions are supported?</strong></summary>

These collections target **IBM MAS 9.x**. The original collections (`postman-oslc/`, `postman-rest/`) may work with Maximo 7.6.1.x instances with REST API enabled.
</details>

<details>
<summary><strong>What's the difference between OSLC and REST collections?</strong></summary>

Both access the same data. OSLC uses `/oslc/os/` paths with `POST` + `x-method-override` for updates. REST uses `/api/os/` paths. Note: native `PATCH` returns 501 on MAS 9, so both patterns effectively use the same update mechanism.
</details>

<details>
<summary><strong>Can I use Newman for CI/CD deployment?</strong></summary>

Yes. The `autoscripts.json` collection is designed for Newman-based deployment:
```bash
npm install -g newman
newman run postman-mas9-rest/autoscripts.json -e your-env.json --folder "1. Deploy All Scripts"
```
</details>

<details>
<summary><strong>Why can't I set After Save (objectevent=7) via the API?</strong></summary>

The `mxapiautoscript` Application Object Structure recomputes `objectevent` from boolean flags during save, overriding any explicit value. After Save requires direct SQL via a utility script. See [API Behavior Notes](#-api-behavior-notes).
</details>

---

## Security

### Credential Safety

- **Never commit API keys or passwords** to version control
- All collections use `{{apikey}}` environment variable — no hardcoded credentials
- The `.gitignore` excludes environment files, credentials, discovery artifacts, and automation scripts
- OAS3 specs are git-ignored as they contain instance-specific information

### Reporting Vulnerabilities

1. **Do not** open a public GitHub issue
2. Open a private security advisory on the repository
3. We will acknowledge receipt within 48 hours

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for full guidelines.

1. **Fork** the repository
2. **Create a branch:** `git checkout -b add-module-CODE`
3. **Add collections** for both OSLC and REST patterns
4. **Test** against a live Maximo instance
5. **Submit** a Pull Request

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**[Back to Top](#ibm-maximo-7x-8x-mas-9x--api-collections--automation-scripts)**

Built for the Maximo community — API collections, automation scripts, and deployment pipelines.

</div>
