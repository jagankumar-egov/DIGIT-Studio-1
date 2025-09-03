# Public Services Module - Technical Documentation

## Overview

The Public Services Module is a React-based UI package for the DIGIT platform that provides a framework for creating and managing government service applications. It enables dynamic form creation, workflow management, and service configuration through MDMS (Master Data Management System).

## Module Information

- **Package Name**: `@egovernments/digit-ui-module-public-services`
- **Version**: 0.0.1
- **Author**: Jagankumar <jagan.kumar@egov.org.in>
- **License**: MIT

## Architecture Overview

The module follows a layered architecture:

1. **Presentation Layer**: React components for UI rendering
2. **Configuration Layer**: MDMS-driven service configurations
3. **Service Layer**: API integrations and data transformations
4. **Utility Layer**: Helper functions and common utilities

## Directory Structure

```
src/
├── Module.js                 # Main module entry point
├── components/               # Reusable UI components
│   ├── PublicServicesCard.js # Main module card
│   ├── WorkflowActions.js    # Workflow action handlers
│   ├── ActionBar.js          # Action bar component
│   ├── ActionModal.js        # Action modal dialogs
│   ├── Request.js            # HTTP request handler
│   └── ...
├── configs/                  # Configuration files
│   ├── serviceConfiguration.js
│   ├── UICustomizations.js
│   ├── inboxGenericConfig.js
│   └── ...
├── pages/                    # Page components
│   └── employee/             # Employee-specific pages
├── services/                 # API service layers
├── utils/                    # Utility functions
└── ...
```

## Core Components

### 1. PublicServicesModule (`src/Module.js`)

**Purpose**: Main module component that initializes the Public Services functionality.

**Key Features**:
- Fetches service data from `/public-service/v1/service` API
- Manages module loading states
- Initializes dynamic module codes based on service data
- Integrates with DIGIT store system

**Props**:
- `stateCode`: State identifier
- `userType`: Type of user (employee/citizen)
- `tenants`: Tenant information

### 2. PublicServicesCard (`src/components/PublicServicesCard.js`)

**Purpose**: Main dashboard card component for the Public Services module.

**Key Features**:
- Displays module information on the main dashboard
- Provides navigation links to service applications
- Uses internationalization for labels

### 3. DigitDemoComponent (`src/pages/employee/DigitDemo/digitDemoComponent.js`)

**Purpose**: Main form component for service applications with stepper functionality.

**Key Features**:
- Multi-step form creation based on service configuration
- Form data persistence in localStorage
- Draft functionality for partial form saves
- Dynamic form configuration from MDMS
- Workflow integration for form submission

**Key Methods**:
- `onSubmit()`: Handles form submission and step progression
- `persistData()`: Saves form data to localStorage
- `onDraftLabelClick()`: Saves application as draft

### 4. WorkflowActions (`src/components/WorkflowActions.js`)

**Purpose**: Handles workflow-based actions for applications.

**Key Features**:
- Dynamic action buttons based on workflow configuration
- Role-based action filtering
- Integration with workflow services
- Action modal dialogs for user input
- Toast notifications for action results

### 5. InboxService (`src/pages/employee/DigitDemo/InboxService.js`)

**Purpose**: Inbox functionality for viewing and managing applications.

**Key Features**:
- Generic inbox configuration
- Service-specific filtering
- Parallel workflow support
- Integration with InboxSearchComposer

## Configuration System

### Service Configuration

The module uses MDMS-based service configuration located at:
- `configs/serviceConfiguration.js` - Main service configuration schema
- Dynamic configuration fetch from MDMS v2 API

**Configuration Structure**:
```javascript
{
  "module": "ModuleName",
  "service": "ServiceName", 
  "fields": [...], // Form field definitions
  "workflow": {...}, // Workflow configuration
  "documents": [...], // Document requirements
  "calculator": {...}, // Fee calculation
  "validation": {...} // Validation rules
}
```

### UI Customizations

**File**: `src/configs/UICustomizations.js`

**Purpose**: Provides middleware functions for customizing search and inbox behavior.

**Key Customizations**:
- `searchGenericConfig`: Custom search preprocessing and validation
- `InboxGenericConfig`: Inbox data preprocessing and link generation

## Utility Functions

### Core Utilities (`src/utils/index.js`)

**Key Functions**:

1. **`transformToApplicationPayload()`**: Transforms form data into API-compatible format
2. **`generateViewConfigFromResponse()`**: Creates view configuration from application data
3. **`getWorkflowState()`**: Determines workflow state transitions
4. **`transformResponseforModulePage()`**: Formats service data for module display
5. **`useWorkflowDetails()`**: Custom hook for workflow data fetching

### Form Generation (`src/utils/generateFormConfigFromSchemaUtil.js`)

**Purpose**: Dynamically generates form configurations from service schemas.

**Key Features**:
- Converts JSON schema to form field configurations
- Handles nested objects and arrays
- MDMS integration for dropdown options
- Multi-step form generation

## API Integrations

### Primary APIs

1. **Service API**: `/public-service/v1/service`
   - Fetches available services for tenant
   - Returns service metadata and configurations

2. **Application API**: `/public-service/v1/application/{serviceCode}`
   - Creates/updates service applications
   - Handles workflow transitions

3. **MDMS v2 API**: `/mdms-v2/v2/_search`
   - Fetches service configurations
   - Returns dynamic form schemas

4. **Workflow API**: `/egov-workflow-v2/egov-wf/businessservice/_search`
   - Fetches workflow configurations
   - Returns available actions and states

### Request Handling (`src/components/Request.js`)

**Purpose**: Centralized HTTP request handling with error management.

**Key Features**:
- Axios-based HTTP client
- Automatic authentication token injection
- Error handling and redirects
- Request caching capabilities
- Multipart form data support

## Data Flow

### Application Creation Flow

1. **Service Selection**: User selects service from module page
2. **Configuration Fetch**: System fetches service configuration from MDMS
3. **Form Generation**: Dynamic form is generated from configuration
4. **Form Filling**: User fills form across multiple steps
5. **Data Persistence**: Form data is saved to localStorage at each step
6. **Submission**: Final payload is transformed and submitted to API
7. **Workflow Initiation**: Workflow is initiated based on service configuration

### Workflow Action Flow

1. **Application View**: User views application details
2. **Action Fetch**: Available actions are fetched from workflow service
3. **Role Filtering**: Actions are filtered based on user roles
4. **Action Selection**: User selects and confirms action
5. **Payload Construction**: Action payload is constructed with user input
6. **API Submission**: Action is submitted to workflow API
7. **State Update**: Application state is updated and reflected in UI

## Key Features

### 1. Dynamic Form Generation
- Schema-driven form creation
- Support for complex nested forms
- MDMS integration for dropdown data
- Validation rule enforcement

### 2. Multi-step Forms
- Progressive form filling with stepper UI
- Data persistence across steps
- Navigation between completed steps
- Draft save functionality

### 3. Workflow Integration
- Role-based action availability
- Parallel workflow support
- Workflow history tracking
- State-based UI updates

### 4. Document Management
- Dynamic document upload based on configuration
- File type and size validation
- Document categorization
- View and download capabilities

### 5. Internationalization
- Complete i18n support
- Dynamic label generation
- Locale-specific formatting

## Technology Stack

- **React** 17.0.2
- **React Router DOM** 5.3.0
- **React Hook Form** 6.15.8
- **React Query** 3.6.1
- **Axios** for HTTP requests
- **DIGIT UI Components** for consistent UI

## Security Considerations

- Authentication token-based security
- Role-based access control
- XSS protection through proper data sanitization
- CSRF protection via token validation
- Secure file upload handling

## Performance Optimizations

- Request caching for frequently accessed data
- Lazy loading of components
- Optimized re-renders through proper React patterns
- localStorage usage for form data persistence

## Error Handling

- Comprehensive error boundaries
- User-friendly error messages
- Automatic token refresh handling
- Graceful degradation for API failures

## Testing Considerations

- Component unit tests with React Testing Library
- API integration tests
- Workflow scenario testing
- Cross-browser compatibility testing

## Deployment

The module is built using `microbundle-crl` and can be:
- Published to npm registry
- Integrated into DIGIT frontend applications
- Deployed as part of larger DIGIT ecosystem

## Future Enhancements

- Enhanced form validation capabilities
- Real-time collaboration features
- Advanced reporting and analytics
- Mobile-responsive optimizations
- Performance monitoring integration

## Maintenance

- Regular dependency updates
- Security vulnerability patches
- Performance monitoring and optimization
- Documentation updates
- Bug fixes and feature enhancements

---

*This documentation is generated for the Public Services Module of the DIGIT platform. For more information, refer to the official DIGIT documentation at https://core.digit.org*