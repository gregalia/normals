# Optometry EMR System

Electronic Medical Records system for optometry practices built with Java and
Spring Boot.

## Architecture

- **Microservices**: Event-driven architecture using Apache Kafka
- **Backend**: Java 21, Spring Boot 3.5.3
- **Database**: PostgreSQL per service
- **Messaging**: Apache Kafka
- **Build**: Gradle with Kotlin DSL
- **Testing**: JUnit 5, Testcontainers
- **Container**: Docker, Kubernetes deployment
- **Cloud**: AWS EKS, RDS, MSK

## Services

- **Patient Service**: Demographics, medical history, insurance
- **Scheduling Service**: Appointments, provider calendars
- **Clinical Service**: Exam data, prescriptions, diagnostics
- **Billing Service**: Charges, insurance claims, payments
- **Notification Service**: Appointment reminders, alerts

## Requirements

- Java 21+
- Docker and Docker Compose
- AWS CLI (for cloud deployment)

## Development Setup

```bash
# Clone repository
git clone <repository-url>
cd normals

# Build all services
./gradlew build

# Run patient service
./gradlew :services:patient-service:bootRun

# Run tests
./gradlew test

# Format code
./gradlew spotlessApply
```

## Project Structure

```
normals/
├── services/                    # Microservices
│   ├── patient-service/         # Patient management
│   └── gradle/                  # Shared build configuration
├── claude/                      # Project documentation
├── infrastructure/              # Terraform (future)
└── docker/                      # Local development (future)
```

## Build Commands

```bash
# Build specific service
./gradlew :services:patient-service:build

# Run all tests
./gradlew check

# Generate native image
./gradlew :services:patient-service:nativeCompile

# Dependency security scan
./gradlew dependencyCheckAnalyze
```

## Compliance

- HIPAA compliant design
- Audit logging for all patient data access
- Encryption at rest and in transit
- Role-based access control

## Technology Stack

| Component        | Technology                |
| ---------------- | ------------------------- |
| Language         | Java 21                   |
| Framework        | Spring Boot 3.5.3         |
| Build            | Gradle 8.14.2             |
| Database         | PostgreSQL                |
| Messaging        | Apache Kafka              |
| Testing          | JUnit 5, Testcontainers   |
| Security         | Spring Security, JWT      |
| Documentation    | OpenAPI/Swagger           |
| Containerization | Docker                    |
| Orchestration    | Kubernetes                |
| Cloud Platform   | AWS                       |
| Monitoring       | OpenTelemetry, Prometheus |
