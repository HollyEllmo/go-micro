# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Architecture

This is a Go-based microservices architecture project with the following services:

- **broker-service**: API Gateway/Broker service running on port 8080 (external) → 80 (internal)
- **authentication-service**: User authentication service with PostgreSQL backend on port 8081 (external) → 80 (internal)  
- **logger-service**: Logging service with MongoDB backend on port 8082 (external) → 80 (internal)
- **front-end**: Web interface for testing microservices interactions on port 80

**Databases:**
- **PostgreSQL**: User data storage on port 5433 (external) → 5432 (internal)
- **MongoDB**: Log data storage on port 27017

The project uses Docker Compose for orchestration and follows standard Go project layout with `cmd/` directories for main applications.

## Development Commands

### Running the Project
```bash
# Start all containerized services with Docker Compose
cd project/
docker-compose up

# Build and start with Makefile (recommended)
cd project/
make up_build    # Builds and starts all containerized services
make up          # Starts without building
make down        # Stops all services

# Start individual services (for development)
cd broker-service/
go run ./cmd/api/

cd authentication-service/
go run ./cmd/api/

cd logger-service/
go run ./cmd/api/

cd front-end/
go run ./cmd/web/
```

### Building Services
```bash
# Build all services with Makefile
cd project/
make build_broker    # Build broker service
make build_auth      # Build auth service
make build_logger    # Build logger service  
make build_front     # Build frontend

# Manual builds
cd broker-service/
CGO_ENABLED=0 go build -o brokerApp ./cmd/api

cd authentication-service/
CGO_ENABLED=0 go build -o authApp ./cmd/api

cd logger-service/
CGO_ENABLED=0 go build -o loggerApp ./cmd/api

# Build Docker images
docker build -f broker-service.dockerfile -t broker-service .
docker build -f authentication-service.dockerfile -t authentication-service .
docker build -f logger-service.dockerfile -t logger-service .
```

## Code Architecture

### Service Structure Pattern
All services follow consistent structure with:
- **cmd/api/main.go**: Service entry point with HTTP server setup
- **cmd/api/routes.go**: Chi router configuration with CORS middleware  
- **cmd/api/handlers.go**: HTTP handlers returning JSON responses
- **cmd/api/helpers.go**: Utility functions
- **data/models.go**: Data models and database operations (auth/logger services)

### Broker Service
- API Gateway/orchestrator for microservice communication
- **Dependencies**: Chi router v5.2.2, Chi CORS v1.2.2

### Authentication Service
- User registration, login, and validation
- **Database**: PostgreSQL with pgx/v4 driver
- **Dependencies**: golang.org/x/crypto for password hashing
- **DSN**: Configured via environment variable

### Logger Service
- Centralized logging with MongoDB storage
- **Database**: MongoDB with official Go driver v1.17.4
- **Dependencies**: go.mongodb.org/mongo-driver

### Front-end Service  
- **main.go**: Web server serving HTML templates
- **templates/**: Modular HTML templates with Bootstrap 5.1.3
  - `base.layout.gohtml`: Base template structure
  - `header/footer.partial.gohtml`: Reusable components
  - `test.page.gohtml`: Testing interface

### API Patterns
- RESTful JSON APIs with consistent response structure:
  ```json
  {
      "error": false,
      "message": "response message", 
      "data": null
  }
  ```
- CORS enabled for all origins with credentials support
- Single endpoint pattern in broker service: `POST /`

### Docker Configuration
- Multi-stage builds using `golang:1.20-alpine` → `alpine:latest`
- Statically linked binaries with `CGO_ENABLED=0`
- Port mapping and restart policies defined in docker-compose.yml
- **Service ports**: broker-service:8080, authentication-service:8081, logger-service:8082
- **Database ports**: PostgreSQL:5433, MongoDB:27017
- Persistent volumes for database data in `./db-data/`

## Project Context

This appears to be an educational/tutorial microservices project demonstrating:
- Microservices communication patterns via broker service
- Docker containerization and multi-service orchestration
- Database integration (PostgreSQL for users, MongoDB for logs)
- User authentication with password hashing
- Centralized logging architecture
- Go web service fundamentals with Chi router
- Template-based frontend for service testing

The architecture supports inter-service communication, persistent data storage, and is expandable for additional microservices and production-ready features.