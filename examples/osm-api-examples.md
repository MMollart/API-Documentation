# OSM API Examples

This directory contains sample requests and responses for the Online Scout Manager API.

## Authentication Example

### Request: Get OAuth Token

```http
POST /oauth/token HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=your_client_id&client_secret=your_client_secret&scope=section:member:read
```

### Response: OAuth Token

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "def502004a3c8b...",
  "scope": "section:member:read"
}
```

## Members Examples

### Request: Get Members List

```http
GET /ext/members/contact/?action=getListOfMembers&sectionid=75013&termid=841318 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Members List

```json
{
  "identifier": "scoutid",
  "items": [
    {
      "scoutid": 76482,
      "firstname": "John",
      "lastname": "Doe",
      "dob": "2010-05-15",
      "patrol": "Eagle Patrol"
    },
    {
      "scoutid": 76483,
      "firstname": "Jane",
      "lastname": "Smith",
      "dob": "2011-03-22",
      "patrol": "Dragon Patrol"
    }
  ]
}
```

### Request: Update Custom Member Data

```http
POST /ext/customdata/?action=updateColumn&section_id=75013 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/x-www-form-urlencoded

service=desktop&associated_id=76482&associated_type=member&value=730.99&column_id=45247&group_id=5&context=members
```

### Response: Update Success

```json
{
  "status": true,
  "data": {}
}
```

## Finance Examples

### Request: Get Invoices

```http
GET /ext/finances/invoices/summary/?action=getInvoiceSummary&sectionid=75013&termid=841318&is_dgrid=true&archived=false HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Invoice Summary

```json
{
  "items": [
    {
      "invoiceid": 121472,
      "name": "John Doe (WSJ01)",
      "entrydate": "2025-01-15",
      "invoice_date": "2025-01-15",
      "in": 100.00,
      "exp": 0.00,
      "total": 100.00,
      "finalised": "No"
    }
  ]
}
```

### Request: Record Payment

```http
POST /ext/finances/invoices/?action=updateBatch&sectionid=75013 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/x-www-form-urlencoded

amount=100.00&comments=Bank transfer received&type=Income&date=2025-10-27&section_id=75013&invoice_id=121472&context=invoice&id=0&pay_username=_x_&category=Payment 1
```

### Response: Payment Recorded

```json
{
  "status": true,
  "data": {}
}
```

## Events Examples

### Request: Get Events

```http
GET /ext/events/summary/?action=getEvents&sectionid=75013&termid=841318&onlyArchived=0 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Events List

```json
{
  "events": [
    {
      "eventid": 1532189,
      "name": "Summer Camp 2025",
      "startdate": "2025-08-01T09:00:00Z",
      "enddate": "2025-08-07T17:00:00Z",
      "archived": "No"
    }
  ]
}
```

### Request: Get Event Attendees

```http
GET /v3/events/event/1532189/attendees HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Event Attendees

```json
[
  {
    "member_id": 76482,
    "full_name": "John Doe",
    "photo_guid": "abc123",
    "patrol_id": 5432
  },
  {
    "member_id": 76483,
    "full_name": "Jane Smith",
    "photo_guid": "def456",
    "patrol_id": 5433
  }
]
```

## Badges Examples

### Request: Get Available Badges

```http
GET /ext/badges/records/?action=getAvailableBadges&section=scouts&sectionid=57818&type_id=1&payload=1 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Available Badges

```json
{
  "badges": [
    {
      "badge_id": 94,
      "name": "Outdoor Challenge",
      "badge_version": 0,
      "badge_group": "Challenge Badges"
    },
    {
      "badge_id": 120,
      "name": "Adventure Challenge",
      "badge_version": 0,
      "badge_group": "Challenge Badges"
    }
  ]
}
```

### Request: Get Badge Records

```http
GET /ext/badges/records/?action=getBadgeRecords&section=scouts&sectionid=57818&term_id=864862&badge_id=94&type_id=1&badge_version=0&payload=1 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Badge Records

```json
{
  "status": true,
  "data": {
    "details": {},
    "members": [
      {
        "member_id": 76482,
        "firstname": "John",
        "lastname": "Doe",
        "completed": "2025-06-15"
      },
      {
        "member_id": 76483,
        "firstname": "Jane",
        "lastname": "Smith",
        "completed": ""
      }
    ]
  }
}
```

## Patrols Examples

### Request: Create Patrol

```http
POST /ext/settings/patrols/?action=add HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/x-www-form-urlencoded

sectionid=75013&name=Dragon Patrol&termid=841318
```

### Response: Patrol Created

```json
{
  "ok": true,
  "patrol_id": 12345
}
```

### Request: Get Patrols with Members

```http
GET /ext/members/patrols/?action=getPatrolsWithPeople&sectionid=75013&termid=841318 HTTP/1.1
Host: www.onlinescoutmanager.co.uk
Authorization: Bearer YOUR_ACCESS_TOKEN
```

### Response: Patrols with Members

```json
{
  "patrols": [
    {
      "patrol_id": 12345,
      "patrol_name": "Dragon Patrol",
      "members": [
        {
          "member_id": 76482,
          "firstname": "John",
          "lastname": "Doe"
        },
        {
          "member_id": 76483,
          "firstname": "Jane",
          "lastname": "Smith"
        }
      ]
    }
  ]
}
```

## Error Response Examples

### 401 Unauthorized

```json
{
  "error": "unauthorized",
  "error_description": "The access token is invalid or has expired",
  "message": "Authentication required"
}
```

### 403 Forbidden

```json
{
  "error": "forbidden",
  "error_description": "The provided token does not have the required scope",
  "message": "Insufficient permissions"
}
```

### 429 Rate Limit Exceeded

```json
{
  "error": "rate_limit_exceeded",
  "error_description": "Too many requests. Please retry after the specified time",
  "message": "Rate limit exceeded"
}
```

**Response Headers:**
```
Retry-After: 60
```

## Notes

- Replace `YOUR_ACCESS_TOKEN` with your actual OAuth access token
- All timestamps are in ISO 8601 format
- Numeric IDs (section_id, term_id, member_id) are examples and should be replaced with your actual values
- Form-urlencoded requests must properly encode special characters
