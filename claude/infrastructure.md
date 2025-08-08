# AWS Infrastructure & Kubernetes Deployment

## Cloud Architecture

- **EKS Cluster** - Managed Kubernetes for microservices orchestration
- **RDS PostgreSQL or DSQL** - Managed databases per service with Multi-AZ for HA
- **Amazon MSK** - Managed Kafka for event streaming
- **Application Load Balancer** - Traffic distribution and SSL termination
- **Route 53** - DNS management and health checks
- **CloudFront** - CDN for frontend assets and API caching
- **S3** - Static assets, diagnostic images, backups
- **Secrets Manager** - Database credentials, API keys, certificates
- **Parameter Store** - Application configuration
- **CloudWatch** - AWS native logging, metrics, and alarms
- **ADOT Collector** - Managed OpenTelemetry data collection
- **Prometheus** - Self-managed or Amazon Managed Prometheus (AMP)
- **Grafana** - Self-managed or Amazon Managed Grafana (AMG)
- **VPC** - Network isolation with private/public subnets
- **NAT Gateway** - Outbound internet access for private subnets

## Security & Compliance (AWS)

- **IAM Roles** - Service-to-service authentication
- **KMS** - Encryption key management for HIPAA compliance
- **WAF** - Web application firewall for API protection
- **GuardDuty** - Threat detection and monitoring
- **CloudTrail** - API audit logging for compliance
- **Config** - Resource compliance monitoring
- **Inspector** - Container and application security scanning

## Terraform Modules

```txt
modules/
├── networking/          # VPC, subnets, security groups
├── eks/                # EKS cluster, node groups, RBAC
├── rds/                # PostgreSQL instances per service
├── msk/                # Managed Kafka cluster
├── observability/      # Prometheus, Grafana, ADOT setup
├── monitoring/         # CloudWatch, alerting, dashboards
├── security/           # IAM roles, KMS keys, secrets
└── storage/            # S3 buckets, backup policies
```

## Kubernetes Resources

- **Namespaces** - Environment isolation (dev, staging, prod) + monitoring namespace
- **Deployments** - Microservice pods with rolling updates
- **Services** - Internal service discovery and load balancing
- **Ingress** - External traffic routing with AWS Load Balancer Controller
- **ConfigMaps** - Application configuration per environment
- **Secrets** - Database credentials, API keys (from AWS Secrets Manager)
- **PersistentVolumes** - EBS volumes for stateful workloads
- **HorizontalPodAutoscaler** - Automatic scaling based on CPU/memory
- **NetworkPolicies** - Pod-to-pod communication restrictions
- **ServiceMonitor** - Prometheus metrics collection
- **PrometheusRule** - Alerting rules and recording rules
- **DaemonSet** - ADOT collectors on each node
- **StatefulSet** - Prometheus, Grafana, Jaeger persistent storage

## Native Image & Performance Optimization

### GraalVM Native Image

- **Fast Startup** - Sub-second cold start times (critical for serverless and autoscaling)
- **Low Memory Footprint** - Reduced heap usage for better container density
- **Smaller Images** - Native binaries produce significantly smaller container images
- **AOT Compilation** - Ahead-of-time compilation for predictable performance

### Native Image Benefits for EMR System

- **Cost Optimization** - Lower memory usage reduces EKS node requirements
- **Improved Patient Experience** - Faster response times during peak clinic hours
- **Better Autoscaling** - Quick startup enables responsive horizontal scaling
- **Resource Efficiency** - More services per node, lower AWS costs

### Build Configuration

```kotlin
// build.gradle.kts - Native image support
tasks.named<org.springframework.boot.gradle.tasks.bundling.BootBuildImage>("bootBuildImage") {
    builder.set("paketobuildpacks/builder:tiny")
    environment.set(mapOf(
        "BP_NATIVE_IMAGE" to "true",
        "BP_JVM_VERSION" to "17"
    ))
}
```

### Native Image Considerations

- **Build Time** - Longer compilation times (acceptable for CI/CD)
- **Reflection Configuration** - Spring Boot 3.x handles most automatically
- **Testing Strategy** - Test both JVM and native modes
- **AWS Lambda Integration** - Excellent fit for event-driven components

## CI/CD Pipeline

1. **Code Push** - Developer commits trigger GitHub Actions
2. **Build & Test** - Gradle build, unit tests, integration tests with Testcontainers
3. **Native Image Build** - GraalVM native compilation and testing
4. **Security Scan** - SAST/DAST, dependency vulnerability checks
5. **Container Build** - Docker image build and push to ECR
6. **Infrastructure Deploy** - Terraform apply for infrastructure changes
7. **Application Deploy** - Helm chart deployment via ArgoCD
8. **Observability Validation** - Verify metrics/traces are flowing correctly
9. **Health Checks** - Automated smoke tests and SLO validation
10. **Alert Testing** - Validate alerting rules and notification channels

## Infrastructure Setup (AWS)

1. **AWS Account Setup** - Configure billing alerts, IAM users, and basic security
2. **Terraform Bootstrap** - S3 bucket for state, DynamoDB for locking
3. **Network Foundation** - VPC, subnets, security groups, NAT gateway
4. **EKS Cluster** - Managed Kubernetes with worker nodes and RBAC
5. **Database Layer** - RDS PostgreSQL instances for each service
6. **Messaging Infrastructure** - Amazon MSK (Kafka) cluster
7. **Monitoring Stack** - CloudWatch, Prometheus, Grafana
8. **CI/CD Pipeline** - GitHub Actions, ArgoCD, container registry

## Project Structure

```txt
optometry-emr/
├── services/                    # Microservices source code
│   ├── patient-service/
│   ├── scheduling-service/
│   ├── clinical-service/
│   └── billing-service/
├── shared/                      # Common libraries and models
│   ├── events/
│   ├── security/
│   ├── observability/          # OTel configuration, custom metrics
│   └── testing/
├── infrastructure/              # Terraform modules and environments
│   ├── modules/
│   │   ├── observability/      # Prometheus, Grafana, ADOT
│   │   └── monitoring/         # CloudWatch, alerting
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── prod/
│   └── scripts/
├── k8s/                        # Kubernetes manifests and Helm charts
│   ├── charts/
│   │   ├── microservices/
│   │   └── observability/      # Prometheus, Grafana, Jaeger charts
│   ├── manifests/
│   └── environments/
├── observability/              # Monitoring configuration
│   ├── dashboards/             # Grafana dashboard JSON
│   ├── alerting/               # Prometheus alerting rules
│   ├── slos/                   # Service level objective definitions
│   └── runbooks/               # Incident response procedures
├── frontend/                   # React/Angular frontend application
├── docker/                     # Docker compose for local development
└── .github/                    # CI/CD workflows
    └── workflows/
```
