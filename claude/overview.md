# Project Overview

Building an Electronic Medical Records (EMR) system specifically for optometry practices. The primary test customer is an optometrist who can provide domain expertise and real-world validation.

## High Level Goals

- Java/Spring Boot ecosystem - Dependency injection, data access, security, testing, testing, linting, formatting
- Event-driven architecture - Kafka, eventual consistency, distributed systems patterns
- Supply chain security - SLSA, SBOMs, vulnerability management, build provenance
- Data structures & algorithms - Hash tables, tries, priority queues in real applications
- Kubernetes - Container orchestration, service discovery, scaling, networking
- Observability - Distributed tracing, metrics, logging, SLOs, incident response

## Architecture Goals

- **Event-driven microservices** using Java and Spring Boot
- **Apache Kafka** for inter-service communication
- **HIPAA compliant** from day one
- **Real-time updates** for practice workflow
- **Web-based frontend** for providers and staff
- **Patient portal** for appointment scheduling and records access

## Technical Stack

- **Backend:** Java 17+, Spring Boot 3.x, Spring Data JPA
- **Messaging:** Apache Kafka
- **Database:** PostgreSQL (per service)
- **Build:** Gradle with Kotlin DSL, Supply chain security
- **Testing:** JUnit 5, Testcontainers, contract testing
- **Security:** Spring Security, JWT tokens
- **Documentation:** OpenAPI/Swagger
- **Container Orchestration:** Kubernetes (AWS EKS)
- **Infrastructure as Code:** Terraform
- **Cloud Platform:** AWS
- **Container Registry:** Amazon ECR
- **Deployment:** Helm charts, GitOps with ArgoCD
- **Observability:** OpenTelemetry (OTel), Prometheus, Grafana, Jaeger
- **Monitoring:** AWS CloudWatch, custom SLIs/SLOs
- **Alerting:** Grafana alerts, PagerDuty integration
