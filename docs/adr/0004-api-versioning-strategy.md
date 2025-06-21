# ADR-0004: API Versioning Strategy

**Status:** Accepted
**Date:** 2025-06-21
**Deciders:** Development Team

## Context

Field-Compass-IQ will serve enterprise customers who integrate deeply with our APIs. We need a versioning strategy that allows us to evolve the API while maintaining backward compatibility for existing customer integrations. Customers may not be able to update their integrations immediately when we release new API versions.

## Decision

We will implement **semantic versioning** with **URL path-based versioning** for our REST API, combined with **GraphQL schema versioning** for GraphQL endpoints.

**REST API Versioning**: `/api/v1/customers`, `/api/v2/customers`
**GraphQL Versioning**: Schema evolution with deprecation warnings

## Consequences

### Positive
- **Clear version communication**: Customers know exactly which version they're using
- **Backward compatibility**: Can maintain older versions while developing new features
- **Gradual migration**: Customers can migrate at their own pace
- **Documentation clarity**: Each version can have separate documentation
- **Testing isolation**: Can test different versions independently
- **Enterprise-friendly**: Meets enterprise customer expectations for API stability

### Negative
- **Maintenance overhead**: Need to maintain multiple API versions simultaneously
- **Code duplication**: Some logic may be duplicated across versions
- **Testing complexity**: Need to test all supported versions
- **Documentation overhead**: Multiple sets of documentation to maintain

### Neutral
- **Deprecation strategy**: Need clear timelines for version sunset
- **Migration tooling**: May need tools to help customers migrate between versions

## Alternatives Considered

1. **Header-based Versioning** (`Accept: application/vnd.api.v1+json`)
   - Rejected: Less discoverable, harder for customers to understand and test

2. **Query Parameter Versioning** (`/api/customers?version=1`)
   - Rejected: Easy to forget, not as clear in URLs, caching issues

3. **No Versioning / Breaking Changes**
   - Rejected: Unacceptable for enterprise customers with existing integrations

4. **Date-based Versioning** (`/api/2025-06-21/customers`)
   - Rejected: Less intuitive than semantic versioning

## Implementation Notes

### Versioning Rules
- **Major Version** (v1 → v2): Breaking changes that require customer code changes
- **Minor Version** (v1.1 → v1.2): New features, backward compatible (communicated via API responses)
- **Patch Version**: Bug fixes, no API changes (transparent to customers)

### REST API Structure
```
/api/v1/customers          # Version 1
/api/v2/customers          # Version 2
/api/v1/work-orders        # Version 1
/api/v2/work-orders        # Version 2
```

### GraphQL Versioning
- Single endpoint: `/graphql`
- Schema evolution with deprecation warnings
- Field deprecation with migration guides
- New fields added without breaking existing queries

### Version Support Policy
- **Current Version**: Full feature support and active development
- **Previous Version**: Bug fixes and security updates only
- **Legacy Versions**: Security updates for 12 months after deprecation
- **End of Life**: 18 months notice before discontinuation

### Implementation Requirements
- **Controller Versioning**: Separate controllers per major version
- **Shared Business Logic**: Use CQRS/MediatR to share business logic across versions
- **Version-Specific DTOs**: Different DTOs for different API versions
- **Swagger Documentation**: Separate Swagger docs per version
- **Testing**: Automated tests for all supported versions
- **Monitoring**: Version usage analytics to inform deprecation decisions

### Bulk Operations Versioning
- Bulk endpoints follow same versioning: `/api/v1/customers/bulk`
- Version-specific bulk operation DTOs
- Backward compatibility for bulk data formats

### Breaking Change Examples
- Removing fields from responses
- Changing field types (string → number)
- Changing required fields
- Modifying error response formats
- Changing authentication requirements

### Non-Breaking Change Examples
- Adding new optional fields
- Adding new endpoints
- Adding new query parameters
- Improving performance
- Adding new HTTP methods to existing endpoints

### Customer Communication
- **Release Notes**: Detailed changelog for each version
- **Migration Guides**: Step-by-step guides for version upgrades
- **Deprecation Notices**: 6-month advance notice for version deprecation
- **Support**: Dedicated support for enterprise customers during migrations
