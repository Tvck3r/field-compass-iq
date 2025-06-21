# LLM Architecture Context - Field-Compass-IQ

*Concise architectural guide for AI-assisted development*

## Core Principles
- **Multi-tenant**: Database-per-tenant, customer cloud isolation
- **Event-driven**: Webhooks for all data changes
- **API-first**: CQRS/MediatR, REST + GraphQL, versioned
- **Customizable**: Dynamic theming, external IDs, white-label ready
- **Secure**: OWASP compliant, customer IdP integration

## Tech Stack
**Backend**: .NET 9, CQRS/MediatR, EF Core, PostgreSQL, SignalR  
**Frontend**: React 18, TypeScript, Tailwind, Shadcn, Next.js  
**Infrastructure**: Terraform, GCP (primary), AWS/Azure (planned)  
**Code Quality**: CSharpier (C#), Prettier (TS), required VS Code extensions

## Architecture Patterns

### Backend (.NET)
```
API → Command/Query Handlers → Domain Aggregates → Repository → Database
```
- **Commands**: Write operations, return void/ID only
- **Queries**: Read operations, return DTOs only (never entities)
- **DTOs**: All API responses use DTOs, auto-generate TypeScript types
- **External IDs**: All customer tables have `externalId` field
- **Versioning**: URL-based (`/api/v1/`, `/api/v2/`)
- **Bulk Ops**: Dedicated endpoints for batch operations

### Frontend (React/TS)
```
Pages → Components (Shadcn) → Hooks → API Client → Backend
```
- **No hardcoded styles**: Use CSS custom properties for theming
- **File-based routing**: Next.js app directory
- **Type safety**: Auto-generated from OpenAPI specs
- **State**: React Query (server), Context (UI), Zustand (client)

### Database
- **Per-tenant databases**: Complete isolation per customer
- **Audit tables**: Track all changes for compliance
- **External ID mapping**: Support customer data integration

## Implementation Rules

### API Development
```csharp
// Command pattern
public class CreateCustomerCommand : IRequest<Guid>
public class CreateCustomerCommandHandler : IRequestHandler<CreateCustomerCommand, Guid>
public class CreateCustomerCommandValidator : AbstractValidator<CreateCustomerCommand>

// Query pattern  
public class GetCustomerQuery : IRequest<CustomerDto>
public class GetCustomerQueryHandler : IRequestHandler<GetCustomerQuery, CustomerDto>
```

### Frontend Components
```tsx
// Dynamic theming with CSS variables
const Button = ({ variant = "primary" }) => (
  <button className="bg-primary-500 hover:bg-primary-600 text-white px-4 py-2 rounded-md">
    {children}
  </button>
);

// Auto-generated types from OpenAPI
import type { CustomerDto, CreateCustomerRequest } from '@/types/api';
```

### Infrastructure
```hcl
# Terraform per-customer environments
module "customer_environment" {
  source = "./modules/gcp/customer"
  customer_id = var.customer_id
  region = var.region
  environment = var.environment
}
```

## Key Constraints
- **Never expose domain entities** in API responses
- **All styling must be themeable** via CSS custom properties  
- **All data changes must trigger webhooks** for customer integration
- **Support external IDs** for bidirectional data mapping
- **Per-tenant monitoring** and resource isolation
- **API versioning required** for breaking changes

## File Naming Conventions
**Backend**: `{Entity}{Action}Command.cs`, `{Entity}{Action}Query.cs`  
**Frontend**: `{feature}-{component}.tsx`, `use-{hook-name}.ts`  
**Types**: Auto-generated from OpenAPI, no manual API types

## Security Requirements
- Customer IdP integration for authentication
- JWT token validation with customer-specific keys
- OWASP Top 10 compliance mandatory
- All secrets in cloud secret managers
- Database encryption with customer-managed keys

## When Implementing Features:
1. **Backend**: Create command/query handlers, never expose entities
2. **Frontend**: Use Shadcn components, CSS variables for styling
3. **Database**: Add external ID fields, audit columns
4. **API**: Version if breaking, document in Swagger
5. **Events**: Emit domain events for webhook notifications
6. **Tests**: Unit tests for handlers, integration tests for APIs

## Common Patterns
**Customer Onboarding**: Terraform provisions → Database setup → Webhook config  
**Data Operations**: Command → Aggregate → Repository → Event → Webhook  
**UI Customization**: Theme JSON → CSS variables → Component rendering  
**Multi-tenant Queries**: Filter by tenant context automatically

*Refer to `/docs/adr/` for detailed architectural decisions*