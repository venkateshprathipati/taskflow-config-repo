# TaskFlow Config Repository

## Overview

This repository serves as the centralized configuration store for the **TaskFlow Microservices Application**. It contains externalized configuration properties that are managed and served by Spring Cloud Config Server, enabling consistent and environment-specific configuration management across all microservices.

## Repository Structure

```
taskflow-config-repo/
├── README.md                          # This documentation
├── application.yml                    # Global configuration (shared across all services)
├── application-dev.yml               # Global development environment configuration
├── application-prod.yml              # Global production environment configuration
├── application-test.yml              # Global testing environment configuration
├── auth-service.yml                  # Authentication service configuration
├── auth-service-dev.yml             # Auth service development configuration
├── auth-service-prod.yml            # Auth service production configuration
├── auth-service-test.yml            # Auth service testing configuration
├── user-service.yml                 # User service configuration
├── user-service-dev.yml             # User service development configuration
├── user-service-prod.yml            # User service production configuration
├── user-service-test.yml            # User service testing configuration
├── task-service.yml                 # Task service configuration
├── task-service-dev.yml             # Task service development configuration
├── task-service-prod.yml            # Task service production configuration
├── task-service-test.yml            # Task service testing configuration
├── eureka-server.yml                # Service discovery configuration
├── eureka-server-dev.yml            # Eureka server development configuration
├── eureka-server-prod.yml           # Eureka server production configuration
└── api-gateway.yml                  # API Gateway configuration
```

## Configuration Files Description

### Global Configuration Files

- **`application.yml`**: Base configuration shared by all microservices
- **`application-{profile}.yml`**: Environment-specific global configuration that overrides base settings

### Service-Specific Configuration Files

Each microservice has its own configuration files following the naming convention:
- **`{service-name}.yml`**: Base configuration for the specific service
- **`{service-name}-{profile}.yml`**: Environment-specific configuration for the service

### Microservices Covered

1. **Auth Service** (`auth-service`) - Handles authentication and JWT token management
2. **User Service** (`user-service`) - Manages user profiles and user-related operations
3. **Task Service** (`task-service`) - Manages task creation, updates, and task-related operations
4. **Eureka Server** (`eureka-server`) - Service discovery and registration
5. **API Gateway** (`api-gateway`) - Request routing and cross-cutting concerns

## Environment Profiles

The configuration repository supports multiple environments:

- **`dev`** - Development environment
- **`test`** - Testing/QA environment  
- **`prod`** - Production environment

## Configuration Hierarchy

Spring Cloud Config follows this precedence order (highest to lowest priority):

1. `{service-name}-{profile}.yml` (Service-specific profile configuration)
2. `{service-name}.yml` (Service-specific base configuration)
3. `application-{profile}.yml` (Global profile configuration)
4. `application.yml` (Global base configuration)

## Usage

### Config Server Setup

1. **Clone this repository** to use as the configuration source for your Config Server:
```bash
git clone https://github.com/venkateshprathipati/taskflow-config-repo.git
```

2. **Configure your Config Server** to point to this repository:
```yaml
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/venkateshprathipati/taskflow-config-repo
          default-label: main
          clone-on-start: true
```

### Client Configuration

Each microservice should be configured to connect to the Config Server:

```yaml
spring:
  application:
    name: {service-name}  # e.g., auth-service, user-service
  config:
    import: configserver:http://localhost:8888
  profiles:
    active: dev  # or test, prod
```

### Accessing Configuration

The Config Server exposes REST endpoints to access configuration:

```bash
# Get configuration for a service with default profile
GET http://localhost:8888/{service-name}/default

# Get configuration for a service with specific profile
GET http://localhost:8888/{service-name}/{profile}

# Get configuration for a specific service, profile, and label
GET http://localhost:8888/{service-name}/{profile}/{label}
```

**Examples:**
```bash
curl http://localhost:8888/auth-service/dev
curl http://localhost:8888/user-service/prod
curl http://localhost:8888/task-service/test
```

## Configuration Properties

### Common Properties (application.yml)

- **Eureka Configuration**: Service discovery settings
- **Management Endpoints**: Actuator configuration for health checks
- **Logging Configuration**: Base logging levels
- **Security Configuration**: Basic security settings

### Service-Specific Properties

- **Database Configuration**: Connection strings, credentials, JPA settings
- **Server Configuration**: Port numbers, context paths
- **JWT Configuration**: Secret keys, expiration times (for auth-service)
- **Business Logic Configuration**: Service-specific behavioral settings

## Security Considerations

⚠️ **Important Security Notes:**

1. **Sensitive Data**: Never commit plain-text passwords, API keys, or secrets to this repository
2. **Encryption**: Use Spring Cloud Config's encryption features for sensitive properties:
   ```yaml
   encrypted.property: '{cipher}AQA...'
   ```
3. **Access Control**: Ensure the repository has appropriate access controls
4. **Environment Separation**: Use different repositories or branches for production configurations

## Development Workflow

### Making Configuration Changes

1. **Create a feature branch**:
```bash
git checkout -b feature/update-auth-service-config
```

2. **Make your changes** to the appropriate configuration files

3. **Test the changes** in your development environment

4. **Commit and push**:
```bash
git add .
git commit -m "Update auth-service JWT expiration time"
git push origin feature/update-auth-service-config
```

5. **Create a pull request** for review

6. **Refresh services** after merging (if using `@RefreshScope`):
```bash
curl -X POST http://localhost:8081/actuator/refresh
```

### Configuration Validation

Before deploying configuration changes:

1. **Validate YAML syntax** using tools like `yamllint`
2. **Test configurations** in development environment first
3. **Review sensitive data** to ensure no secrets are exposed
4. **Check property precedence** to avoid unexpected overrides

## Integration with TaskFlow Microservices

This configuration repository is designed to work seamlessly with the TaskFlow microservices architecture:

- **Spring Boot 3.x** compatibility
- **Kotlin/Java** based microservices support
- **PostgreSQL** database configuration
- **JWT authentication** setup
- **Docker containerization** support
- **Eureka service discovery** integration

## Monitoring and Maintenance

### Health Checks

Monitor configuration server health:
```bash
curl http://localhost:8888/actuator/health
```

### Configuration Refresh

Refresh configuration without service restart:
```bash
curl -X POST http://localhost:{service-port}/actuator/refresh
```

### Version History

All configuration changes are tracked through Git history, providing:
- **Change auditing**: Who made what changes and when
- **Rollback capability**: Easy reversion to previous configurations
- **Branch management**: Environment-specific configuration branches

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request with a clear description

## Support

For questions or issues related to configuration management:

1. **Check existing issues** in the repository
2. **Create a new issue** with detailed description
3. **Follow naming conventions** when adding new configuration files
4. **Document any new properties** added to configuration files

---

**Repository**: [taskflow-config-repo](https://github.com/venkateshprathipati/taskflow-config-repo)  
**Related Project**: TaskFlow Microservices Application  
**Config Server**: Spring Cloud Config  
**Supported Profiles**: dev, test, prod
