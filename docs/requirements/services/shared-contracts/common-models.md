# Shared Data Contracts

## Common Data Models

### Standard Response Envelope

All API responses follow a consistent envelope structure to provide metadata and standardize error handling across services.

```json
{
  "data": {
    // Actual response payload
  },
  "meta": {
    "timestamp": "2025-08-23T10:30:00Z",
    "requestId": "req_123456789",
    "version": "v1",
    "pagination": {
      "page": 1,
      "size": 20,
      "total": 150,
      "totalPages": 8
    }
  },
  "links": {
    "self": "https://api.platform.com/v1/resource",
    "next": "https://api.platform.com/v1/resource?page=2",
    "prev": null,
    "first": "https://api.platform.com/v1/resource?page=1",
    "last": "https://api.platform.com/v1/resource?page=8"
  }
}
```

### Error Response Structure

Standardized error responses provide consistent information for client-side error handling.

```json
{
  "errors": [
    {
      "id": "error_123456",
      "status": "400",
      "code": "VALIDATION_ERROR",
      "title": "Validation Failed",
      "detail": "The provided email address is not valid",
      "source": {
        "pointer": "/data/attributes/email",
        "parameter": "email"
      },
      "meta": {
        "timestamp": "2025-08-23T10:30:00Z",
        "requestId": "req_123456789",
        "field": "email",
        "rejectedValue": "invalid-email"
      }
    }
  ]
}
```

### Common Entity Identifiers

**User Reference**

```json
{
  "userId": "usr_123e4567-e89b-12d3-a456-426614174000",
  "email": "user@example.com",
  "displayName": "John Doe",
  "avatarUrl": "https://cdn.platform.com/avatars/usr_123e4567.jpg"
}
```

**Organization Reference**

```json
{
  "organizationId": "org_123e4567-e89b-12d3-a456-426614174000",
  "name": "Acme Corporation",
  "slug": "acme-corp",
  "logoUrl": "https://cdn.platform.com/logos/org_123e4567.jpg"
}
```

**Address Information**

```json
{
  "street": "123 Main Street",
  "city": "San Francisco",
  "state": "CA",
  "postalCode": "94105",
  "country": "US",
  "coordinates": {
    "latitude": 37.7749,
    "longitude": -122.4194
  }
}
```

### Audit and Metadata Fields

**Standard Audit Information**

```json
{
  "createdAt": "2025-08-23T10:30:00Z",
  "createdBy": {
    "userId": "usr_123e4567-e89b-12d3-a456-426614174000",
    "displayName": "John Doe"
  },
  "updatedAt": "2025-08-23T11:45:00Z",
  "updatedBy": {
    "userId": "usr_987654321-a456-42d3-e89b-426614174000",
    "displayName": "Jane Smith"
  },
  "version": 3,
  "status": "active"
}
```

**Soft Delete Information**

```json
{
  "deletedAt": "2025-08-23T12:00:00Z",
  "deletedBy": {
    "userId": "usr_123e4567-e89b-12d3-a456-426614174000",
    "displayName": "John Doe"
  },
  "deletionReason": "User requested account closure"
}
```

## Domain Events Schema

### Event Envelope Structure

All domain events follow a consistent structure for reliable processing and audit trails.

```json
{
  "eventId": "evt_123456789",
  "eventType": "UserRegistered",
  "aggregateId": "usr_123e4567-e89b-12d3-a456-426614174000",
  "aggregateType": "User",
  "eventVersion": "1.0",
  "occurredAt": "2025-08-23T10:30:00Z",
  "causationId": "cmd_987654321",
  "correlationId": "corr_456789123",
  "data": {
    // Event-specific payload
  },
  "metadata": {
    "publishedBy": "user-service",
    "publishedAt": "2025-08-23T10:30:00Z",
    "schemaVersion": "1.0",
    "traceId": "trace_abc123def456",
    "userId": "usr_123e4567-e89b-12d3-a456-426614174000"
  }
}
```

### Common Event Types

**User Events**

- `UserRegistered`: New user account created
- `UserEmailVerified`: User confirmed email address
- `UserProfileUpdated`: User profile information changed
- `UserDeactivated`: User account disabled
- `UserDeleted`: User account permanently removed

**Organization Events**

- `OrganizationCreated`: New organization established
- `OrganizationUpdated`: Organization information modified
- `UserJoinedOrganization`: User became organization member
- `UserLeftOrganization`: User removed from organization
- `OrganizationDeactivated`: Organization suspended

**Content Events**

- `DocumentCreated`: New document uploaded
- `DocumentUpdated`: Document content or metadata changed
- `DocumentShared`: Document shared with users or groups
- `DocumentDeleted`: Document removed from system
- `CommentAdded`: Comment added to document or discussion

## API Standards

### Request Headers

**Required Headers**

- `Authorization`: Bearer token for authenticated requests
- `Content-Type`: MIME type of request body (application/json)
- `Accept`: Acceptable response MIME types
- `X-Request-ID`: Unique identifier for request tracing

**Optional Headers**

- `X-Correlation-ID`: Business process correlation identifier
- `Accept-Language`: Preferred response language
- `X-Organization-ID`: Organization context for multi-tenant operations
- `If-Match`: ETag for optimistic concurrency control

### Response Headers

**Standard Response Headers**

- `Content-Type`: Response MIME type
- `X-Request-ID`: Echo of request identifier
- `X-Rate-Limit-Remaining`: Remaining requests in current window
- `X-Rate-Limit-Reset`: Time when rate limit resets
- `ETag`: Entity tag for caching and concurrency control

### HTTP Status Codes

**Success Responses**

- `200 OK`: Successful GET, PUT, PATCH requests
- `201 Created`: Successful POST request creating new resource
- `202 Accepted`: Request accepted for async processing
- `204 No Content`: Successful DELETE request

**Client Error Responses**

- `400 Bad Request`: Invalid request syntax or parameters
- `401 Unauthorized`: Authentication required or failed
- `403 Forbidden`: Authenticated but insufficient permissions
- `404 Not Found`: Requested resource does not exist
- `409 Conflict`: Request conflicts with current resource state
- `422 Unprocessable Entity`: Valid syntax but semantic errors

**Server Error Responses**

- `500 Internal Server Error`: Unexpected server error
- `502 Bad Gateway`: Invalid response from upstream server
- `503 Service Unavailable`: Server temporarily unavailable
- `504 Gateway Timeout`: Timeout waiting for upstream server

## Data Validation Rules

### Common Field Validations

**Email Addresses**

- Must match RFC 5322 email format specification
- Maximum length of 254 characters
- Case-insensitive comparison for uniqueness
- Blocked disposable email domains for business accounts

**Passwords**

- Minimum 12 characters, maximum 128 characters
- Must contain uppercase, lowercase, digit, and special character
- Cannot contain user's email, name, or common dictionary words
- Cannot reuse last 5 passwords for the same account

**Phone Numbers**

- E.164 international format preferred (+1234567890)
- Support for national formats with country context
- Validation against libphonenumber library
- SMS verification required for security-sensitive operations

**Dates and Times**

- ISO 8601 format with timezone information (2025-08-23T10:30:00Z)
- UTC timezone for storage, local timezone for display
- Date ranges validated for logical consistency
- Support for various cultural date formats in user interfaces

### Business Rule Validations

**Unique Constraints**

- Email addresses unique across all user accounts
- Organization slugs unique for URL generation
- Document names unique within organization or folder
- API keys unique across all client applications

**Referential Integrity**

- User references validated against active user accounts
- Organization references checked for user membership
- Parent-child relationships maintained for hierarchical data
- Soft-deleted entities excluded from reference validation

## Integration Contracts

### Service-to-Service Communication

**Request Authentication**

- Service-to-service requests use client certificate authentication
- JWT tokens with service-specific claims and scopes
- Request signing with HMAC-SHA256 for high-security operations
- Rate limiting based on service identity and operation type

**Circuit Breaker Configuration**

- Failure threshold: 5 consecutive failures
- Timeout duration: 30 seconds in open state
- Success threshold: 3 consecutive successes to close
- Fallback responses for critical dependency failures

### External API Integration

**Third-Party Service Standards**

- OAuth 2.0 for user-authorized external services
- API key management for service-to-service integrations
- Webhook verification using signature validation
- Retry policies with exponential backoff for transient failures

**Data Exchange Formats**

- JSON as primary format for structured data
- CSV for bulk data exports and imports
- XML support for legacy system integration
- Protocol Buffers for high-performance internal communication

## Versioning Strategy

### API Versioning

**Version Numbering**

- Semantic versioning for internal API tracking (1.2.3)
- Major version in URL path for breaking changes (/v1/, /v2/)
- Minor and patch versions handled transparently
- Deprecation notices 6 months before removal

**Backward Compatibility**

- Additive changes (new fields) are non-breaking
- Field removal or type changes require major version increment
- Default values provided for new required fields
- Migration guides provided for breaking changes

### Event Schema Evolution

**Schema Versioning**

- Event schema version included in event metadata
- Multiple schema versions supported simultaneously
- Automatic schema migration for minor version changes
- Manual intervention required for breaking schema changes

**Consumer Compatibility**

- Event consumers declare supported schema versions
- Event publishers maintain backward compatibility
- Schema registry for centralized schema management
- Version negotiation for optimal compatibility