# Field-Compass-IQ Architecture Overview

This document provides a comprehensive overview of the Field-Compass-IQ architecture, including system design, data flow, and key architectural decisions.

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Customer Environments                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │   Customer A    │  │   Customer B    │  │   Customer C    │ │
│  │   GCP Project   │  │   AWS Account   │  │  Azure Tenant   │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                  Multi-Tenant Platform                     │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │   Frontend   │  │   Backend    │  │Infrastructure│     │
│  │   React/TS   │  │   .NET Core  │  │  Terraform   │     │
│  │   Tailwind   │  │   CQRS/DDD   │  │  Multi-Cloud │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

### Multi-Tenancy Strategy

#### Database-per-Tenant
Each customer gets their own isolated database within their cloud project:

```
Customer Environment (GCP Project)
├── Compute Instance(s)
├── Database Instance (PostgreSQL)
│   ├── Application Database
│   ├── Analytics Database (optional)
│   └── Audit Database
├── Monitoring Stack
│   ├── Metrics Collection
│   ├── Log Aggregation
│   └── Alerting
└── Networking
    ├── VPC
    ├── Load Balancer
    └── Firewall Rules
```

#### Benefits of Database-per-Tenant
- **Complete data isolation**: No risk of cross-tenant data access
- **Compliance friendly**: Easier GDPR, CCPA, and regional compliance
- **Performance isolation**: Customer workloads don't affect each other
- **Customizable schemas**: Can adapt schema per customer needs
- **Backup flexibility**: Per-customer backup and recovery strategies

## Application Architecture

### Backend Architecture (.NET Core)

```
┌─────────────────────────────────────────────────────────────┐
│                      API Layer                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    REST     │  │   GraphQL   │  │   SignalR   │        │
│  │ Controllers │  │   Queries   │  │    Hubs     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                  Application Layer                         │
│                     (CQRS/MediatR)                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  Commands   │  │   Queries   │  │  Behaviors  │        │
│  │  Handlers   │  │  Handlers   │  │ (Pipeline)  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                   Domain Layer                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ Aggregates  │  │   Entities  │  │   Events    │        │
│  │    Roots    │  │   Objects   │  │  Handlers   │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                Infrastructure Layer                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  Entity     │  │  External   │  │   Event     │        │
│  │ Framework   │  │ Services    │  │ Publishing  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

#### Key Patterns

**CQRS (Command Query Responsibility Segregation)**
- Commands: Write operations that change state
- Queries: Read operations that return data
- Separate models optimized for their specific use case

**Domain-Driven Design (DDD)**
- Aggregates: Consistency boundaries for related entities
- Value Objects: Immutable objects that describe characteristics
- Domain Events: Business events that trigger side effects

**MediatR Pipeline**
- Request/Response pattern for all business operations
- Pipeline behaviors for cross-cutting concerns
- Testable and maintainable business logic

### Frontend Architecture (React/TypeScript)

```
┌─────────────────────────────────────────────────────────────┐
│                     Presentation Layer                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    Pages    │  │ Components  │  │   Layouts   │        │
│  │(App Router) │  │   (Shadcn)  │  │   Themes    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    State Management                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │React Query  │  │   Context   │  │    Zustand  │        │
│  │(Server State│  │ (UI State)  │  │(Client State)       │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                    Service Layer                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  API Client │  │  GraphQL    │  │ WebSocket   │        │
│  │   (REST)    │  │   Client    │  │  Client     │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
```

#### Key Features

**Dynamic Theming**
- CSS custom properties for runtime theme switching
- Customer-provided brand configurations
- Dark/light mode support
- Accessibility-compliant color schemes

**Type Safety**
- Auto-generated TypeScript types from OpenAPI specs
- End-to-end type safety from API to UI
- Compile-time error detection

**Component Architecture**
- Shadcn/UI for consistent, accessible components
- Tailwind CSS for utility-first styling
- Compound component patterns for complex UI

## Data Flow

### Write Operations (Commands)

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Client    │───▶│     API     │───▶│   Command   │
│  (Frontend) │    │ Controller  │    │   Handler   │
└─────────────┘    └─────────────┘    └─────────────┘
                                               │
                                               ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Domain    │◀───│ Repository  │◀───│   Domain    │
│  Events     │    │   Pattern   │    │  Aggregate  │
└─────────────┘    └─────────────┘    └─────────────┘
       │
       ▼
┌─────────────┐    ┌─────────────┐
│  Webhook    │    │   External  │
│ Notification│───▶│  Services   │
└─────────────┘    └─────────────┘
```

### Read Operations (Queries)

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Client    │───▶│     API     │───▶│    Query    │
│  (Frontend) │    │ Controller  │    │   Handler   │
└─────────────┘    └─────────────┘    └─────────────┘
                                               │
                                               ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│     DTO     │◀───│   Entity    │◀───│  Database   │
│  Response   │    │   Mapping   │    │    Query    │
└─────────────┘    └─────────────┘    └─────────────┘
```

### Event-Driven Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Domain    │───▶│    Event    │───▶│  Webhook    │
│   Action    │    │     Bus     │    │  Delivery   │
└─────────────┘    └─────────────┘    └─────────────┘
                           │
                           ▼
                   ┌─────────────┐
                   │  Customer   │
                   │   Systems   │
                   └─────────────┘
```

## Infrastructure Architecture

### Multi-Cloud Strategy

#### Primary: Google Cloud Platform
```
GCP Project (Customer Specific)
├── Compute Engine
│   ├── Application Servers
│   └── Background Workers
├── Cloud SQL (PostgreSQL)
│   ├── Primary Database
│   └── Read Replicas
├── Cloud Monitoring
│   ├── Metrics Collection
│   ├── Log Analysis
│   └── Alerting Rules
├── Cloud Storage
│   ├── File Uploads
│   └── Backup Storage
└── Virtual Private Cloud
    ├── Subnets
    ├── Firewall Rules
    └── Load Balancers
```

#### Future: AWS Support
```
AWS Account (Customer Specific)
├── EC2 Instances
├── RDS PostgreSQL
├── CloudWatch
├── S3 Storage
└── VPC with Security Groups
```

#### Future: Azure Support
```
Azure Subscription (Customer Specific)
├── Virtual Machines
├── Azure Database for PostgreSQL
├── Azure Monitor
├── Blob Storage
└── Virtual Network
```

### Terraform Module Structure

```
terraform/
├── modules/
│   ├── gcp/
│   │   ├── project/           # GCP project setup
│   │   ├── compute/           # VM instances and scaling
│   │   ├── database/          # Cloud SQL configuration
│   │   ├── monitoring/        # Stackdriver setup
│   │   └── networking/        # VPC and firewall rules
│   ├── aws/                   # Future AWS modules
│   └── azure/                 # Future Azure modules
├── environments/
│   ├── customers/
│   │   └── [customer-id]/
│   │       ├── dev/
│   │       ├── staging/
│   │       └── production/
└── shared/
    ├── monitoring/            # Cross-customer monitoring
    └── security/              # Shared security policies
```

## Security Architecture

### Authentication & Authorization

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Client    │───▶│   Gateway   │───▶│ Customer    │
│Application  │    │    (JWT)    │    │   IdP       │
└─────────────┘    └─────────────┘    └─────────────┘
                           │
                           ▼
                   ┌─────────────┐
                   │Application  │
                   │   Server    │
                   └─────────────┘
```

#### Key Security Features
- **Customer Identity Integration**: Support for customer IdP systems
- **JWT Token Validation**: Stateless authentication
- **Role-Based Access Control**: Granular permissions
- **API Rate Limiting**: Per-customer rate limits
- **OWASP Compliance**: Security best practices

### Data Protection

#### Encryption
- **Data at Rest**: Database encryption with customer-managed keys
- **Data in Transit**: TLS 1.3 for all communications
- **Application Secrets**: Cloud-native secret management

#### Compliance
- **GDPR**: Data sovereignty with regional deployments
- **CCPA**: Data privacy and user rights
- **SOC 2 Type II**: Audit-ready security controls
- **OWASP Top 10**: Protection against common vulnerabilities

## Monitoring & Observability

### Per-Tenant Monitoring

```
Customer Environment
├── Application Metrics
│   ├── Request/Response Times
│   ├── Error Rates
│   └── Throughput
├── Infrastructure Metrics
│   ├── CPU/Memory Usage
│   ├── Database Performance
│   └── Network Latency
├── Business Metrics
│   ├── User Activity
│   ├── Feature Usage
│   └── Custom KPIs
└── Security Metrics
    ├── Authentication Events
    ├── Authorization Failures
    └── Suspicious Activity
```

### Centralized Management

```
Management Dashboard
├── Multi-Customer Overview
├── Health Status Monitoring
├── Performance Benchmarking
├── Security Event Correlation
└── Capacity Planning
```

## Scalability Considerations

### Horizontal Scaling
- **Stateless Application Design**: Easy to add more instances
- **Database Read Replicas**: Scale read operations
- **Message Queues**: Async processing for heavy operations
- **CDN Integration**: Global content delivery

### Vertical Scaling
- **Resource Monitoring**: Auto-scaling based on metrics
- **Database Optimization**: Query performance tuning
- **Caching Strategies**: Redis for session and data caching
- **Connection Pooling**: Efficient database connections

## Integration Architecture

### Webhook System

```
Database Change
       │
       ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Domain    │───▶│   Event     │───▶│  Webhook    │
│   Event     │    │ Processing  │    │  Delivery   │
└─────────────┘    └─────────────┘    └─────────────┘
                                               │
                                               ▼
                                      ┌─────────────┐
                                      │  Customer   │
                                      │   Endpoint  │
                                      └─────────────┘
```

### External ID Support
- **Bidirectional Mapping**: Internal IDs ↔ Customer IDs
- **Import/Export**: Bulk data operations with external IDs
- **Sync Capabilities**: Keep data in sync with customer systems

## Future Architecture Considerations

### Phase 2 Enhancements
- **Microservices Decomposition**: Break monolith into services
- **Event Sourcing**: Complete audit trail and replayability
- **CQRS Read Models**: Optimized query databases
- **API Gateway**: Centralized API management

### Phase 3 Advanced Features
- **Machine Learning Pipeline**: AI-powered routing optimization
- **Real-time Analytics**: Stream processing for live insights
- **Global Distribution**: Multi-region deployments
- **Edge Computing**: Regional processing capabilities

This architecture provides a solid foundation for Field-Compass-IQ's growth from MVP to enterprise-scale platform while maintaining security, compliance, and customer isolation requirements.
