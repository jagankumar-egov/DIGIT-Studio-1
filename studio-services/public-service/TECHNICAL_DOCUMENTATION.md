# Public Service - Technical Documentation

## Overview

The Public Service is a Go-based microservice within the DIGIT Studio ecosystem that manages public services and applications. It provides comprehensive APIs for service lifecycle management, application processing, and workflow integration with multi-tenant support.

## Architecture

### System Design
- **Language**: Go 1.24
- **Architecture Pattern**: Clean Architecture with layered design
- **Database**: PostgreSQL 15 with Flyway migrations
- **Message Broker**: Apache Kafka for asynchronous processing
- **Container**: Multi-stage Docker builds for optimized deployment

### Service Dependencies
- **Individual Service**: Citizen/applicant management
- **MDMS (Master Data Management Service)**: Configuration and master data
- **Workflow Service**: Application state management and transitions
- **Billing Service**: Fee calculation and demand generation
- **ID Generation Service**: Unique identifier generation
- **SMS/Notification Service**: Communication services

## Core Components

### 1. Controllers Layer
- **`public_controller.go`** - Service CRUD operations (create, update, search)
- **`application_controller.go`** - Application lifecycle management and workflow integration

### 2. Service Layer
- **`public_service.go`** - Core service management business logic
- **`application_service.go`** - Application processing and workflow orchestration
- **`enrichment_service.go`** - Data enrichment and validation
- **`workflow_service.go`** - Workflow state management
- **`validation_service.go`** - Business rule validation
- **Integration Services**: Individual, MDMS, Billing, SMS, Indexer

### 3. Repository Layer
- **`public_repository.go`** - Service data access and persistence
- **`application_repository.go`** - Application data operations
- **`rest_call_repository.go`** - External service communication
- **`db.go`** - Database connection management

### 4. Model Layer
Comprehensive data models including:
- **Core Models**: Service, Application, Document, User
- **Request/Response Models**: Structured API payloads
- **Integration Models**: Individual, Payment, SMS, Workflow
- **MDMS Models**: Master data structures

### 5. Infrastructure
- **Kafka Producer/Consumer**: Asynchronous message processing
- **Database Migrations**: Flyway-managed schema evolution
- **Configuration Management**: Environment-based configuration
- **Logging**: Structured logging with custom utilities

## API Specification

### Service Management APIs

#### Create Service
```
POST /public-service/v1/service
Headers: X-Tenant-Id (required)
```

#### Update Service
```
PUT /public-service/v1/service/{serviceCode}
Headers: X-Tenant-Id (required)
```

#### Search Services
```
GET /public-service/v1/service
Headers: X-Tenant-Id (required), auth-token (required)
Query Parameters: module, businessService, serviceCode
```

### Application Management APIs

#### Create Application
```
POST /public-service/v1/application/{serviceCode}
Headers: X-Tenant-Id (required)
```

#### Update Application
```
PUT /public-service/v1/application/{serviceCode}
Headers: X-Tenant-Id (required)
```

#### Search Applications
```
GET /public-service/v1/application/{serviceCode}
Headers: X-Tenant-Id (required)
```

#### Search My Applications
```
GET /public-service/v1/application
Headers: X-Tenant-Id (required)
```

### Utility APIs
- **`POST /public-service/_calculate`** - Fee calculation
- **`POST /public-service/_deleteMDMSSchema`** - MDMS schema cleanup

## Data Flow

### Service Creation Flow
1. **Request Validation**: Validate input payload and headers
2. **ID Generation**: Generate unique service identifiers
3. **Enrichment**: Enhance service data with master data
4. **Validation**: Apply business rules and constraints
5. **Checklist Generation**: Create service-specific checklists
6. **Persistence**: Store service data in PostgreSQL
7. **Response**: Return created service details

### Application Processing Flow
1. **Service Validation**: Verify service exists and is active
2. **Individual Management**: Create/update citizen records
3. **Document Processing**: Handle document uploads and validation
4. **Demand Generation**: Calculate fees and create billing demands
5. **Workflow Initiation**: Start application workflow process
6. **Notification**: Send SMS/email notifications
7. **Indexing**: Update search indexes for discoverability

## Deployment

### Docker Build Process
The service uses a multi-stage Docker build:

**Stage 1: Build**
- Base: `golang:1.24-alpine`
- Cross-platform compilation support
- Dependency caching optimization
- Binary compilation: `service-binary`

**Stage 2: Runtime**
- Base: `alpine:3.18`
- Minimal runtime environment
- Working directory: `/opt/egov`
- Startup script: `start.sh`

### Database Migrations
- **Tool**: Flyway 10.7.1
- **Location**: `scripts/migration/sql/`
- **Management**: Version-controlled schema changes
- **Strategy**: Baseline on migrate with out-of-order support

### Environment Configuration
Key environment variables:
- **Database**: `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`
- **Kafka**: `KAFKA_BOOTSTRAP_SERVERS`, `KAFKA_PAYMENT_CONSUMER_ENABLED`
- **Services**: `MDMS_SERVICE_HOST`, `WORKFLOW_HOST`, `INDIVIDUAL_HOST`
- **Features**: `FLYWAY_ENABLED`, `SERVER_PORT`

### Local Development
```bash
# Start dependencies
docker-compose up db flyway

# Run service
go mod download
go run main.go
```

## Integration Points

### External Services
- **MDMS Service**: Master data and configuration management
- **Individual Service**: Citizen profile management
- **Workflow Service**: Application state transitions
- **Billing Service**: Fee calculation and demand management
- **ID Generation Service**: Unique identifier creation
- **SMS Service**: Notification delivery

### Message Queues
- **Payment Consumer**: Processes payment completion events
- **Kafka Topics**: Asynchronous communication for workflow events

## Data Models

### Core Entities
- **Service**: Service definitions with business rules and configurations
- **Application**: Service applications with applicant details and documents
- **Business Service**: Service configuration and workflow mapping
- **Process Instance**: Workflow execution state

### Supporting Entities
- **Individual**: Citizen/applicant profiles
- **Document**: File attachments and metadata
- **Address**: Location information
- **Demand**: Billing and fee structures
- **Status**: Workflow state tracking

## Security

### Authentication & Authorization
- **Tenant-based**: Multi-tenant isolation via `X-Tenant-Id` header
- **Token-based**: Authentication token validation for search operations
- **Role-based**: Creator, Viewer, Editor roles for service access

### Data Validation
- **Input Validation**: JSON schema validation and business rules
- **Enrichment**: Data enhancement and consistency checks
- **Audit Trail**: Complete audit logging for all operations

## Monitoring & Observability

### Logging
- **Structured Logging**: Custom logger implementation
- **Error Tracking**: Comprehensive error handling and reporting
- **Performance Monitoring**: Request/response logging

### Health Checks
- Database connectivity validation
- External service dependency checks
- Kafka connectivity monitoring

## Development Guidelines

### Code Organization
- **Clean Architecture**: Separation of concerns across layers
- **Dependency Injection**: Constructor-based service initialization
- **Error Handling**: Consistent error propagation and logging
- **Testing**: Unit tests for business logic validation

### Database Schema
- **Versioned Migrations**: Flyway-managed schema evolution
- **Audit Columns**: Creation and modification tracking
- **Constraints**: Data integrity enforcement
- **Indexing**: Optimized query performance

## Performance Considerations

### Optimization Strategies
- **Connection Pooling**: Database connection management
- **Caching**: Master data caching for performance
- **Async Processing**: Kafka-based background processing
- **Minimal Docker Images**: Alpine-based containers for faster deployment

### Scalability
- **Stateless Design**: Horizontal scaling capability
- **Multi-tenant**: Isolated tenant data processing
- **Load Balancing**: HTTP endpoint distribution
- **Background Processing**: Decoupled workflow execution

## Troubleshooting

### Common Issues
- **Database Connectivity**: Verify PostgreSQL connection and credentials
- **Kafka Integration**: Check broker connectivity and topic configuration
- **Service Dependencies**: Ensure MDMS and other services are accessible
- **Migration Failures**: Review Flyway logs and schema conflicts

### Debugging
- **Log Analysis**: Review structured logs for error patterns
- **Database Queries**: Monitor PostgreSQL performance
- **API Testing**: Validate request/response formats
- **Workflow States**: Track application progression through states

## Recent Updates

### Version Management
- Added version field support for Service and Application entities
- Enhanced role-based access control (Creator, Viewer, Editor)
- Improved localization support for workflow screens
- Updated search screen internationalization

### Database Schema Changes
- `V20250902__add_version_service_and_application.sql`: Version field additions
- Enhanced application document management
- Process table creation for workflow tracking