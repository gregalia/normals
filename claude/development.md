# Development Guide

## Development Priorities

### Phase 1 (MVP)

1. Basic patient registration and demographics
2. Simple appointment scheduling
3. Clinical data entry for basic eye exams
4. Prescription generation and printing
5. Basic audit logging for HIPAA compliance

### Phase 2 (Core Features)

1. Insurance verification and claims processing
2. Frame and contact lens inventory management
3. Patient portal for appointment scheduling
4. Automated appointment reminders
5. Real-time patient queue management

### Phase 3 (Advanced Features)

1. Diagnostic image management and storage
2. Advanced reporting and analytics
3. Multi-location practice support
4. Integration with external labs and suppliers
5. Mobile app for patients

## Getting Started

### Local Development Setup

1. Set up local Kafka cluster (Docker Compose)
2. Create shared libraries for common models and events
3. Implement Patient Service as the foundational service
4. Add Scheduling Service with Kafka integration
5. Build basic web frontend for patient registration and scheduling

### Development Workflow

1. **Feature Development** - Local Docker environment with hot reload
2. **Testing** - Unit tests, integration tests with Testcontainers
3. **Container Build** - Multi-stage Docker builds for optimized images
4. **Infrastructure Testing** - Terraform plan/apply in dev environment
5. **Kubernetes Deployment** - Helm charts with environment-specific values
6. **Monitoring** - Application metrics, health checks, log aggregation

## Notes

- Focus on clean domain modeling - optometry has specific terminology and workflows
- Prioritize data accuracy and audit trails over performance initially
- Design APIs to be frontend-agnostic (support web + future mobile apps)
- Consider event sourcing for critical data like prescriptions and billing
- Plan for multi-tenancy if targeting multiple practices eventually
- Use infrastructure as code (Terraform) for all AWS resources to ensure reproducibility
- Implement GitOps workflows for both infrastructure and application deployments
- Design for cloud-native patterns: 12-factor apps, health checks, graceful shutdowns
- Follow AWS Well-Architected Framework principles for security, reliability, and cost optimization
