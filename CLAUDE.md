# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Architecture

This is a Go-based microservices architecture project with the following services:

- **broker-service**: API Gateway/Broker service running on port 8080 (external) → 80 (internal)
- **front-end**: Web interface for testing microservices interactions on port 80

The project uses Docker Compose for orchestration and follows standard Go project layout with `cmd/` directories for main applications.

## Development Commands

### Running the Project
```bash
# Start all services with Docker Compose
cd project/
docker-compose up

# Start individual services
cd broker-service/
go run ./cmd/api/

cd front-end/
go run ./cmd/web/
```

### Building Services
```bash
# Build broker service binary
cd broker-service/
CGO_ENABLED=0 go build -o brokerApp ./cmd/api

# Build Docker images
docker build -f broker-service.dockerfile -t broker-service .
```

## Code Architecture

### Broker Service Structure
- **main.go**: Service entry point with HTTP server setup
- **routes.go**: Chi router configuration with CORS middleware
- **handler.go**: HTTP handlers returning JSON responses
- **Dependencies**: Chi router v5.2.2, Chi CORS v1.2.2

### Front-end Service Structure  
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

## Project Context

This appears to be an educational/tutorial microservices project demonstrating:
- Basic microservices communication patterns
- Docker containerization
- Go web service fundamentals
- Template-based frontend development

The architecture is expandable for additional services, database integration, authentication, and production-ready features.