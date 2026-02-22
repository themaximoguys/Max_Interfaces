<div align="center">

# IBM Maximo 7.x, 8.x, MAS 9.x — API Collections

### The most comprehensive Postman collection suite for IBM Maximo Application Suite APIs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Postman](https://img.shields.io/badge/Postman-v2.1-FF6C37?logo=postman&logoColor=white)](https://www.postman.com/)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-3.0-6BA539?logo=openapi-initiative&logoColor=white)](https://swagger.io/specification/)
[![Maximo Version](https://img.shields.io/badge/Maximo-7.x%20|%208.x%20|%20MAS%209.x-054ADA?logo=ibm&logoColor=white)](https://www.ibm.com/products/maximo)
[![Endpoints](https://img.shields.io/badge/Endpoints-1%2C308-brightgreen)]()
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

**1,308 endpoints** across **14 modules** — Work Orders, Assets, Inventory, Service Desk, Purchasing, Utilities, Preventive Maintenance, Job Plans, Financial, Contracts, IT Infrastructure, Company, Analytics, and Scheduling.

[Quick Start](#-quick-start) · [API Patterns](#-api-patterns) · [Module Coverage](#-module-coverage) · [Contributing](#-contributing)

</div>

---

## Table of Contents

- [Overview](#-overview)
- [Quick Start](#-quick-start)
  - [Import Collections](#1-import-collections-into-postman)
  - [Set Up Environment](#2-set-up-environment)
  - [Make Your First Request](#3-make-your-first-request)
- [API Patterns](#-api-patterns)
  - [OSLC API](#oslc-api-oslcos)
  - [REST API](#rest-api-apios)
  - [Common Query Parameters](#common-query-parameters)
  - [Example Requests](#example-requests)
- [Module Coverage](#-module-coverage)
  - [Tier 1 — Core Operations](#tier-1--core-operations)
  - [Tier 2 — Extended Operations](#tier-2--extended-operations)
  - [Tier 3 — Administrative & Financial](#tier-3--administrative--financial)
- [Authentication](#-authentication)
- [Project Structure](#-project-structure)
- [Troubleshooting](#-troubleshooting)
- [FAQ](#-faq)
- [Security](#-security)
- [Contributing](#-contributing)
- [License](#-license)
- [Acknowledgments](#-acknowledgments)

---

## Overview

Production-ready Postman collections for IBM Maximo Application Suite (MAS) APIs, auto-generated from OpenAPI 3.0 specifications. Covers both the legacy **OSLC** and modern **NextGen REST** API patterns.

### Why This Project?

- **Complete Coverage** — 14 modules with every discoverable endpoint from the OAS3 specs
- **Dual API Patterns** — Both OSLC (`/oslc/os/`) and REST (`/api/os/`) collections for the same resources
- **Ready to Use** — Import into Postman and start making requests in under 2 minutes
- **Enterprise Tested** — Every endpoint verified against a live MAS 9.x instance
- **Community Driven** — Open source with clear contribution guidelines

### Key Metrics

| Metric | Value |
|--------|-------|
| **Total Modules** | 14 (of 18 discovered) |
| **OSLC Endpoints** | 738 |
| **REST Endpoints** | 570 |
| **Total Endpoints** | 1,308 |
| **Target Instance** | Any MAS 9.x instance |
| **Collection Format** | Postman v2.1.0 |
| **Source Format** | OpenAPI 3.0 (OAS3) |

---

## Quick Start

### Prerequisites

- [Postman](https://www.postman.com/) desktop or web app
- IBM Maximo MAS 9.x instance with API access enabled
- API key with appropriate permissions

### 1. Import Collections into Postman

```bash
# Import all OSLC collections
File > Import > Select all files from postman-oslc/

# Import all REST collections
File > Import > Select all files from postman-rest/
```

> You can import individual module files or all 14 at once. Each collection is self-contained.

### 2. Set Up Environment

Create a Postman environment with these variables:

| Variable | Value | Type |
|----------|-------|------|
| `baseUrl` | `https://your-instance.maximo.com/maximo` | default |
| `apikey` | `your-api-key-here` | secret |

### 3. Make Your First Request

Select any collection, choose a **"List"** request, and hit **Send**. All collections inherit the `apikey` header authentication and `{{baseUrl}}` variable automatically.

```
GET {{baseUrl}}/oslc/os/mxapiwodetail?oslc.select=wonum,description,status&oslc.pageSize=5&lean=1
```

You should receive a `200 OK` response with a `member` array containing Maximo records.

---

## API Patterns

Maximo exposes two API patterns for the same resources. Both are fully covered in this project.

### OSLC API (`/oslc/os/`)

The original Maximo API pattern using OSLC (Open Services for Lifecycle Collaboration) standards.

```
GET    /oslc/os/{resource}          # List (paginated)
GET    /oslc/os/{resource}/{id}     # Get by ID
POST   /oslc/os/{resource}          # Create
POST   /oslc/os/{resource}/{id}     # Update (x-method-override: PATCH)
DELETE /oslc/os/{resource}/{id}     # Delete
```

**Update note:** OSLC uses `POST` with `x-method-override: PATCH` header for updates. OSLC collections also include common framework actions (lock, unlock, bookmark, etc.) grouped into a **"Common OSLC Actions"** folder.

### REST API (`/api/os/`)

The NextGen REST API pattern — same resources, standard REST conventions.

```
GET    /api/os/{resource}           # List (paginated)
GET    /api/os/{resource}/{id}      # Get by ID
POST   /api/os/{resource}           # Create
PATCH  /api/os/{resource}/{id}      # Update (native PATCH)
DELETE /api/os/{resource}/{id}      # Delete
```

**Key difference:** REST collections use native `PATCH` for updates and do not include generic OSLC framework actions.

### Common Query Parameters

Both API patterns support the same query parameters:

| Parameter | Example | Description |
|-----------|---------|-------------|
| `oslc.select` | `wonum,description,status` | Select specific fields |
| `oslc.where` | `status="APPR"` | Filter records |
| `oslc.pageSize` | `20` | Records per page |
| `oslc.orderBy` | `+wonum` | Sort order (`+` asc, `-` desc) |
| `lean` | `1` | Compact response (recommended) |
| `collectioncount` | `1` | Include total record count |

### Example Requests

**List work orders (page of 5, key fields only):**

```
GET {{baseUrl}}/oslc/os/mxapiwodetail?oslc.select=wonum,description,status&oslc.pageSize=5&lean=1
```

**Get a specific asset:**

```
GET {{baseUrl}}/api/os/mxapiasset/12345?lean=1
```

**Filter purchase orders by status:**

```
GET {{baseUrl}}/oslc/os/mxapipo?oslc.where=status="APPR"&oslc.pageSize=10&lean=1
```

**Create a work order (POST body):**

```json
{
  "description": "Repair centrifugal pump bearing failure",
  "assetnum": "PUMP-001",
  "location": "BR-300",
  "worktype": "CM",
  "priority": 2,
  "siteid": "BEDFORD"
}
```

**Update with OSLC (requires x-method-override header):**

```
POST {{baseUrl}}/oslc/os/mxapiwodetail/{id}
Header: x-method-override: PATCH
```

**Update with REST (native PATCH):**

```
PATCH {{baseUrl}}/api/os/mxapiwodetail/{id}
```

---

## Module Coverage

### Tier 1 — Core Operations

| Module | Code | OSLC Endpoints | REST Endpoints | Primary Resource |
|--------|------|:--------------:|:--------------:|------------------|
| Work Orders | WO | 166 | 166 | `mxapiwodetail` |
| Assets | ASSET | 118 | 118 | `mxapiasset` |
| Inventory | INVENTOR | 60 | 60 | `mxapiinventory` |
| Service Desk | SD | 52 | 52 | `mxapisr` |

### Tier 2 — Extended Operations

| Module | Code | OSLC Endpoints | REST Endpoints | Primary Resource |
|--------|------|:--------------:|:--------------:|------------------|
| Purchasing | PURCHASE | 45 | 45 | `mxapipo`, `mxapipr` |
| Utilities | UTIL | 57 | 57 | `mxapiperson` |
| Preventive Maintenance | PM | 6 | 6 | `mxapipm` |
| Job Plans | PLANS | 11 | 11 | `mxapijobplan` |

### Tier 3 — Administrative & Financial

| Module | Code | OSLC Endpoints | REST Endpoints | Primary Resource |
|--------|------|:--------------:|:--------------:|------------------|
| Financial | FINANCIAL | 62 | 20 | `mxapicoa` |
| Contracts | CONTRACT | 52 | 10 | `mxapicontract` |
| IT Infrastructure | CI | 52 | 10 | `mxapiauthci` |
| Company | COMPANY | 47 | 5 | `mxapicommodity` |
| Analytics | ANALYTICS | 5 | 5 | `mxapikpigraphic`* |
| Scheduler | SCHEDULER | 5 | 5 | `mxapilbslocation` |

> \*ANALYTICS (`mxapikpigraphic`) may not be deployed on all instances. Returns 404 if the object structure is not configured.

### Not Included (No API Endpoints)

| Module | Reason |
|--------|--------|
| CONFIGUR | No endpoints in OAS3 spec |
| INTEGRATION | No endpoints in OAS3 spec |
| SAFETY | No endpoints in OAS3 spec |
| SLA | No endpoints in OAS3 spec |

---

## Authentication

All collections use API key authentication via the `apikey` HTTP header:

```
Header: apikey: <your-api-key>
```

The API key is stored as the `{{apikey}}` Postman environment variable. Each collection inherits this authentication at the collection level — individual requests do not need separate auth configuration.

### Authentication Modes

| Mode | Header | Use Case |
|------|--------|----------|
| **API Key** (recommended) | `apikey: your-key` | Automation, CI/CD, MCP servers |
| **Basic Auth** | `Authorization: Basic base64(user:pass)` | Development, manual testing |
| **LTPA Token** | `Cookie: LtpaToken2=...` | Browser-based sessions |

### Security Best Practices

- **Never commit credentials** — Use Postman environment variables (type: secret)
- **Use API keys over passwords** — API keys can be scoped and rotated independently
- **HTTPS only** — All connections to Maximo should use TLS
- **Principle of least privilege** — Use API keys with minimal required permissions
- **Rotate regularly** — Implement key rotation policies

---

## Project Structure

```
maximo-interfaces/
├── README.md                        # This file
├── CONTRIBUTING.md                  # Contribution guidelines
├── LICENSE                          # MIT License
├── .gitignore                       # Git ignore rules
├── maximo-api-catalog.json          # Module catalog (18 modules)
│
├── postman-oslc/                    # OSLC Postman collections (14 modules)
│   ├── ANALYTICS.json
│   ├── ASSET.json
│   ├── CI.json
│   ├── COMPANY.json
│   ├── CONTRACT.json
│   ├── FINANCIAL.json
│   ├── INVENTOR.json
│   ├── PLANS.json
│   ├── PM.json
│   ├── PURCHASE.json
│   ├── SCHEDULER.json
│   ├── SD.json
│   ├── UTIL.json
│   └── WO.json
│
├── postman-rest/                    # REST Postman collections (14 modules)
│   └── (same 14 module files)
│
└── api-discovery/                   # Discovery artifacts (git-ignored)
    ├── oas3/                        # OAS3 specs (git-ignored)
    └── oas3-full/                   # Full OAS3 specs (git-ignored)
```

### Collection File Format

Each collection file follows the [Postman Collection v2.1.0](https://schema.getpostman.com/json/collection/v2.1.0/collection.json) schema with:

- **Collection-level auth** — API Key header inherited by all requests
- **`{{baseUrl}}` variable** — No hardcoded URLs
- **Organized by resource** — Folders per object structure (e.g., `mxapiwodetail`, `mxapiasset`)
- **Standard operations** — List, Get by ID, Create, Update, Delete per resource
- **Actions folder** — Module-specific and common OSLC actions (wsmethod calls)
- **Pre-configured query params** — `oslc.select`, `oslc.where`, `oslc.pageSize`, `lean` on all List endpoints

---

## Troubleshooting

### Connection Issues

**Problem:** Requests timeout or return connection errors.

**Solutions:**
- Verify `{{baseUrl}}` includes the full path with protocol: `https://your-instance.maximo.com/maximo`
- Check network/VPN connectivity to the Maximo server
- Increase Postman's request timeout in Settings > General

### Authentication Errors

**Problem:** `401 Unauthorized` responses.

**Solutions:**
- Verify your API key is valid and not expired
- Ensure the API key has permissions for the object structures you're accessing
- Check that the `apikey` header name matches your Maximo configuration (some instances use `maxauth`)
- Confirm the environment variable `{{apikey}}` is set and the correct environment is selected

### OSLC Query Errors

**Problem:** `400 Bad Request` on list endpoints with filters.

**Solutions:**
- Enclose string values in double quotes: `status="WAPPR"` not `status=WAPPR`
- Use valid field names for the object structure
- Check OSLC `where` clause syntax — Maximo uses its own dialect
- Test with a simple query first: `oslc.where=status="APPR"`

### Module Not Found (404)

**Problem:** A module returns 404 for all endpoints.

**Solutions:**
- The object structure may not be deployed on your instance
- Check with your Maximo admin that the API object structures are configured
- ANALYTICS (`mxapikpigraphic`) is known to return 404 on instances without KPI graphics deployed

---

## FAQ

<details>
<summary><strong>Which Maximo versions are supported?</strong></summary>

These collections are built for **IBM Maximo Application Suite (MAS) 9.x** and its OSLC-based REST API. They may work with Maximo 7.6.1.x instances that have the REST API enabled, but this is not officially tested.
</details>

<details>
<summary><strong>What's the difference between OSLC and REST collections?</strong></summary>

Both patterns access the **same data and object structures**. The differences are:
- **URL pattern:** OSLC uses `/oslc/os/`, REST uses `/api/os/`
- **Updates:** OSLC uses `POST` with `x-method-override: PATCH`, REST uses native `PATCH`
- **Actions:** OSLC collections include generic framework actions (lock, unlock, bookmark). REST collections only include resource-specific operations.
- **Endpoint count:** OSLC has 738 endpoints (including framework actions), REST has 570 endpoints.

Choose REST if you prefer standard HTTP methods. Choose OSLC if you need the full action set.
</details>

<details>
<summary><strong>Can I use these with automation tools (Newman, CI/CD)?</strong></summary>

Yes. All collections are standard Postman v2.1.0 JSON files. You can:
- Run them with [Newman](https://www.npmjs.com/package/newman) (Postman's CLI runner)
- Import them into CI/CD pipelines
- Use them with any tool that supports the Postman collection format
</details>

<details>
<summary><strong>Why are some modules missing (CONFIGUR, INTEGRATION, SAFETY, SLA)?</strong></summary>

These modules were discovered in the Maximo module registry (`maximomodules.jsp`) but their OAS3 specifications contain zero API endpoints. They may use internal-only interfaces or require additional configuration to expose APIs.
</details>

<details>
<summary><strong>How do I add a custom object structure?</strong></summary>

If your Maximo instance has custom object structures (e.g., `mxapicustom`), you can:
1. Fetch the OAS3 spec for the module containing the custom object
2. Generate a new collection following the patterns in [CONTRIBUTING.md](CONTRIBUTING.md)
3. Submit a PR if the object structure is standard across MAS instances
</details>

---

## Security

### Credential Safety

- **Never commit API keys or passwords** to version control
- All collections use `{{apikey}}` environment variable — no hardcoded credentials
- The `.gitignore` excludes `.env`, `credentials.json`, `config.json`, and `.mcp.json`
- OAS3 spec files are git-ignored as they may contain instance-specific information

### Reporting Vulnerabilities

If you discover a security issue with these collections:

1. **Do not** open a public GitHub issue
2. Open a private security advisory on the repository
3. Include details about the issue and any affected files
4. We will acknowledge receipt within 48 hours

---

## Contributing

We welcome contributions from the Maximo community. See [CONTRIBUTING.md](CONTRIBUTING.md) for full guidelines including:

- Adding new modules
- Updating existing collections
- Collection naming conventions and standards
- Testing requirements
- Pull request checklist

### Quick Contribution Guide

1. **Fork** the repository
2. **Create a branch:** `git checkout -b add-module-CODE`
3. **Add collections** for both OSLC and REST patterns
4. **Test** against a live Maximo instance
5. **Submit** a Pull Request

---

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- **IBM** — for the Maximo Application Suite and its comprehensive REST API
- **Postman** — for the collection format and API development platform
- **OpenAPI Initiative** — for the OAS3 specification standard
- All **contributors** who help improve and maintain these collections

---

<div align="center">

**[Back to Top](#ibm-maximo-mas-9x--api-discovery--postman-collections)**

Built for the Maximo community — empowering developers with ready-to-use API collections for every module.

</div>
