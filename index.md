---
layout: default
title: API Documentation
---

# Welcome to API Documentation

This repository hosts comprehensive API documentation using OpenAPI specifications and GitHub Pages.

## 📚 Available APIs

### Online Scout Manager (OSM) API v1.0.0

Complete REST API for managing scout sections, members, events, badges, invoices, and patrols.

**Base URL**: `https://www.onlinescoutmanager.co.uk`  
**Authentication**: OAuth 2.0 Bearer Token

[View OpenAPI Specification](api-specs/osm-api.yaml) | [View on Swagger Editor](https://editor.swagger.io/?url=https://raw.githubusercontent.com/MMollart/API-Documentation/main/api-specs/osm-api.yaml)

#### Available Endpoints

##### Members
- `POST /ext/members/contact/grid/` - Get members grid data (bulk retrieval)
- `GET /ext/members/contact/` - Get list of members or individual member
- `GET /ext/customdata/` - Get custom data for members
- `POST /ext/customdata/` - Update custom member data

##### Finance
- `GET /ext/finances/invoices/summary/` - Get invoice summary
- `GET /ext/finances/invoices/` - Get invoice details
- `POST /ext/finances/invoices/` - Create invoices or record payments

##### Events
- `GET /ext/events/summary/` - Get events list
- `GET /v3/events/event/{eventId}/basic_details` - Get event details
- `GET /v3/events/event/{eventId}/summary` - Get event summary
- `GET /v3/events/event/{eventId}/attendees` - Get event attendees

##### Badges
- `GET /ext/badges/records/` - Get available badges or badge records

##### Patrols
- `POST /ext/settings/patrols/` - Create or delete patrols
- `GET /ext/members/patrols/` - Get patrols with member assignments
- `POST /ext/members/patrols/` - Manage patrol assignments

##### Authentication
- `POST /oauth/token` - Get OAuth access token

## 🚀 Quick Start

### View Interactive Documentation

**Option 1: Swagger Editor**
```bash
# Visit Swagger Editor with our spec pre-loaded
https://editor.swagger.io/?url=https://raw.githubusercontent.com/MMollart/API-Documentation/main/api-specs/osm-api.yaml
```

**Option 2: Import to Postman**
1. Open Postman
2. Click **Import** → **Link**
3. Paste: `https://raw.githubusercontent.com/MMollart/API-Documentation/main/api-specs/osm-api.yaml`
4. Click **Import**

**Option 3: Generate Client SDK**
```bash
# Install OpenAPI Generator
npm install @openapitools/openapi-generator-cli -g

# Generate Python client
openapi-generator-cli generate \
  -i https://raw.githubusercontent.com/MMollart/API-Documentation/main/api-specs/osm-api.yaml \
  -g python \
  -o ./osm-python-client

# Other supported languages: javascript, typescript-axios, java, ruby, go, etc.
```

## 🔐 Authentication

All API endpoints (except OAuth token endpoint) require OAuth 2.0 authentication:

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Getting an Access Token

```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=YOUR_CLIENT_ID&client_secret=YOUR_CLIENT_SECRET&scope=section:member:read section:finance:read
```

### Available OAuth Scopes

| Scope | Description |
|-------|-------------|
| `section:member:read` | Read member information |
| `section:member:write` | Update member data |
| `section:finance:read` | View financial data |
| `section:finance:write` | Manage invoices and payments |
| `section:event:read` | Access event information |
| `section:badge:read` | View badge records |
| `section:admin:write` | Administrative operations |
| `section:attendance:write` | Manage attendance and patrols |

## 📖 Example Usage

### Get Members List

```bash
curl -X GET "https://www.onlinescoutmanager.co.uk/ext/members/contact/?action=getListOfMembers&sectionid=75013&termid=841318" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

### Create Invoice

```bash
curl -X POST "https://www.onlinescoutmanager.co.uk/ext/finances/invoices/?action=addInvoice&sectionid=75013" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "name=John Doe (WSJ01)&date=2025-12-31"
```

### Record Payment

```bash
curl -X POST "https://www.onlinescoutmanager.co.uk/ext/finances/invoices/?action=updateBatch&sectionid=75013" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "amount=100.00&type=Income&date=2025-10-27&section_id=75013&invoice_id=121472&category=Payment 1"
```

## 🛠️ Tools & Resources

### Validation

Validate OpenAPI specifications before making changes:

```bash
# Using Swagger CLI
npm install -g @apidevtools/swagger-cli
swagger-cli validate api-specs/osm-api.yaml

# Using OpenAPI Generator
openapi-generator-cli validate -i api-specs/osm-api.yaml
```

### Testing

Test API endpoints using the OpenAPI specification:

```bash
# Using Dredd (API testing framework)
npm install -g dredd
dredd api-specs/osm-api.yaml https://www.onlinescoutmanager.co.uk
```

## 🤝 Contributing

Contributions welcome! To add or improve documentation:

1. Fork this repository
2. Create a feature branch (`git checkout -b docs/improve-osm-api`)
3. Make your changes to the OpenAPI spec or documentation
4. Validate the spec: `swagger-cli validate api-specs/osm-api.yaml`
5. Commit your changes (`git commit -m 'Improve OSM API documentation'`)
6. Push to the branch (`git push origin docs/improve-osm-api`)
7. Open a Pull Request

## 📝 Documentation Standards

All API specifications in this repository follow:

- **OpenAPI 3.0.3** specification format
- Comprehensive parameter descriptions with examples
- Realistic request/response schemas
- Complete security requirements (OAuth scopes)
- Error response documentation (401, 403, 404, 429, 500)

## 🔗 Related Projects

- [OSM Python Client](https://github.com/MMollart/osm) - Official Python client library for OSM API
- More API clients coming soon!

## 📬 Contact & Support

- **Issues**: [GitHub Issues](https://github.com/MMollart/API-Documentation/issues)
- **Repository**: [MMollart/API-Documentation](https://github.com/MMollart/API-Documentation)

---

*Last updated: October 28, 2025*
