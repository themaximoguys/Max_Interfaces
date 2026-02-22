# Contributing to Maximo API Collections

Thank you for your interest in contributing to the Maximo API Discovery project. This guide covers how to add new modules, update existing collections, and submit changes.

## Table of Contents

- [Getting Started](#getting-started)
- [Adding a New Module](#adding-a-new-module)
- [Updating an Existing Collection](#updating-an-existing-collection)
- [Collection Standards](#collection-standards)
- [Testing](#testing)
- [Pull Request Process](#pull-request-process)

## Getting Started

### Prerequisites

- Python 3.6+
- Access to a Maximo Application Suite instance with API key
- [Postman](https://www.postman.com/) (for testing collections)
- `curl` and `jq` for API exploration

### Setup

```bash
git clone https://github.com/your-org/maximo-interfaces.git
cd maximo-interfaces/api-discovery
```

## Adding a New Module

### Step 1: Fetch the OAS3 Specification

```bash
# Replace MODULE_CODE with the Maximo module code (e.g., WO, ASSET)
curl -s -H "apikey: YOUR_API_KEY" \
  "https://your-instance.maximo.com/maximo/oslc/oas?module=MODULE_CODE&includeactions=1" \
  -o oas3/MODULE_CODE.json
```

### Step 2: Analyze the Spec

```bash
# Count paths and identify resources
python3 -c "
import json
spec = json.load(open('oas3/MODULE_CODE.json'))
paths = spec.get('paths', {})
print(f'Paths: {len(paths)}')
resources = set()
for p in paths:
    parts = p.split('?')[0].strip('/').split('/')
    if len(parts) >= 2 and not parts[1].startswith('{'):
        resources.add(parts[1])
print(f'Resources: {sorted(resources)}')
"
```

### Step 3: Generate Postman Collections

Create both OSLC and REST collection files following the [Collection Standards](#collection-standards) below.

### Step 4: Test the Endpoints

```bash
# Verify at least the list endpoint works
curl -s -H "apikey: YOUR_API_KEY" \
  "https://your-instance.maximo.com/maximo/oslc/os/RESOURCE_NAME?oslc.pageSize=1&lean=1"
```

### Step 5: Submit a Pull Request

See [Pull Request Process](#pull-request-process).

## Updating an Existing Collection

1. Check the current collection in `postman-oslc/` or `postman-rest/`
2. Make your changes (add endpoints, fix URLs, update descriptions)
3. Ensure the JSON is properly formatted with 2-space indentation
4. Test the modified endpoints
5. Update the endpoint count in the collection's `info.description`

## Collection Standards

### Naming Convention

- **OSLC collections:** `Maximo OSLC - {Module Name} ({CODE})`
- **REST collections:** `Maximo REST - {Module Name} ({CODE})`

Examples:
- `Maximo OSLC - Work Orders (WO)`
- `Maximo REST - Assets (ASSET)`

### File Naming

- One file per module: `{CODE}.json`
- Placed in `postman-oslc/` for OSLC and `postman-rest/` for REST

### Collection Structure

```json
{
  "info": {
    "name": "Maximo OSLC - Module Name (CODE)",
    "description": "Maximo Module Name (CODE) - Instance description.\nN endpoints from OAS3 spec.\nUse 'Environment Name' environment.",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "auth": {
    "type": "apikey",
    "apikey": [
      { "key": "key", "value": "apikey", "type": "string" },
      { "key": "value", "value": "{{apikey}}", "type": "string" },
      { "key": "in", "value": "header", "type": "string" }
    ]
  },
  "variable": [
    { "key": "baseUrl", "value": "https://your-instance.maximo.com/maximo" }
  ],
  "item": []
}
```

### Request Organization

Collections are organized by resource with this folder hierarchy:

```
Collection Root
├── resource_name_1/
│   ├── Get resource by ID
│   ├── List resource
│   ├── Create resource
│   ├── Update resource
│   ├── Delete resource
│   └── Actions/
│       ├── POST wsmethod:changeStatus
│       └── POST wsmethod:otherAction
├── resource_name_2/
│   └── ...
└── Common OSLC Actions/    (if applicable)
    ├── POST lock
    ├── POST unlock
    └── ...
```

### Request Naming

| Operation | OSLC Name | REST Name |
|-----------|-----------|-----------|
| List | `List {resource}` | `List {resource}` |
| Get by ID | `Get {resource} by ID` | `Get {resource} by ID` |
| Create | `Create {resource}` | `Create {resource}` |
| Update | `Update {resource}` | `Update {resource}` |
| Delete | `Delete {resource}` | `Delete {resource}` |
| Action | `POST wsmethod:{action}` | `POST wsmethod:{action}` |

### URL Patterns

**OSLC:**
```
{{baseUrl}}/oslc/os/{resource}
{{baseUrl}}/oslc/os/{resource}/:id
```

**REST:**
```
{{baseUrl}}/api/os/{resource}
{{baseUrl}}/api/os/{resource}/:id
```

### Query Parameters for List Endpoints

All list (GET collection) endpoints should include these query parameters:

```json
{
  "query": [
    { "key": "oslc.select", "value": "*" },
    { "key": "oslc.where", "value": "", "disabled": true },
    { "key": "oslc.pageSize", "value": "20" },
    { "key": "oslc.orderBy", "value": "", "disabled": true },
    { "key": "lean", "value": "1" }
  ]
}
```

### JSON Formatting

- 2-space indentation
- No trailing whitespace
- Newline at end of file

Validate formatting:
```bash
python3 -c "import json; json.load(open('postman-oslc/MODULE.json'))" && echo "Valid JSON"
```

## Testing

### Manual Testing

1. Import the collection into Postman
2. Set up the environment with `baseUrl` and `apikey`
3. Run each request type (List, Get by ID, Create, Update, Delete)
4. Verify response codes and data structure

### Automated Testing (curl)

```bash
APIKEY="your-api-key"
BASE="https://your-instance.maximo.com/maximo"

# Test OSLC list
curl -s -H "apikey: ${APIKEY}" \
  "${BASE}/oslc/os/{resource}?oslc.pageSize=1&lean=1" \
  -w "\nHTTP %{http_code}\n"

# Test REST list
curl -s -H "apikey: ${APIKEY}" \
  "${BASE}/api/os/{resource}?oslc.pageSize=1&lean=1" \
  -w "\nHTTP %{http_code}\n"
```

### Expected Results

- List endpoints: HTTP 200, returns `{ "member": [...], "responseInfo": {...} }`
- Get by ID: HTTP 200, returns single resource object
- 404 means the object structure is not deployed on the instance
- 401 means invalid or missing API key

## Pull Request Process

1. **Fork** the repository
2. **Create a branch:** `git checkout -b add-module-CODE` or `fix/description`
3. **Make changes** following the standards above
4. **Format JSON:** Ensure 2-space indentation (`python3 -c "import json; d=json.load(open('file.json')); json.dump(d, open('file.json','w'), indent=2)"`)
5. **Test** your changes against a live Maximo instance
6. **Commit** with a clear message: `Add MODULE_NAME (CODE) collections with N endpoints`
7. **Push** and create a Pull Request

### PR Checklist

- [ ] Collection follows naming convention (`Maximo OSLC/REST - Name (CODE)`)
- [ ] JSON is properly formatted (2-space indent)
- [ ] Both OSLC and REST versions are included
- [ ] All list endpoints have standard query parameters
- [ ] Collection-level auth is set to `apikey` header
- [ ] `{{baseUrl}}` variable is used (no hardcoded URLs)
- [ ] Endpoint count in description matches actual count
- [ ] At least the list endpoint has been tested (HTTP 200)

## Questions?

Open an issue if you have questions about contributing or encounter problems with the collections.
