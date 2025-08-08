# Observability & SRE Stack

## Core Tools

- **OpenTelemetry (OTel)** - Auto-instrumentation for metrics, logs, and traces
- **Prometheus** - Time-series metrics collection and storage
- **Grafana** - Unified dashboards for metrics, traces, and logs
- **Jaeger** - Distributed tracing for request flow visualization
- **AWS CloudWatch** - Native AWS metrics and log aggregation
- **AWS Distro for OpenTelemetry (ADOT)** - Managed OTel collection
- **Alert Manager** - Alert routing and notification management
- **PagerDuty** - Incident response and escalation

## SRE Metrics & SLIs

### Golden Signals (RED Method)

- Request Rate: requests/second per service
- Error Rate: 4xx/5xx errors percentage
- Duration: response time percentiles (p50, p95, p99)

### EMR-Specific SLIs

- Patient registration completion rate
- Appointment booking success rate
- Clinical data save latency
- Insurance verification time
- Prescription generation accuracy

### Infrastructure Metrics (USE Method)

- CPU/Memory utilization per pod
- Database connection pool usage
- Kafka consumer lag
- Storage IOPS and latency

## Observability Architecture

```txt
[Spring Boot Services] → [OTel Agent] → [ADOT Collector] → [Prometheus + Jaeger]
                                                        ↓
[Grafana Dashboards] ← [Alert Manager] ← [Prometheus Rules]
        ↓
[PagerDuty] ← [Critical Alerts]
```

## Tracing Scenarios (Jaeger)

- **Patient Registration Flow:** Frontend → Patient Service → Notification Service → Audit Service
- **Appointment Booking:** Frontend → Scheduling Service → Patient Service → Calendar Integration
- **Clinical Exam Workflow:** Frontend → Clinical Service → Patient Service → Billing Service
- **Insurance Verification:** Billing Service → External Insurance API → Patient Service Update
- **Prescription Generation:** Clinical Service → Validation Service → Patient Portal → Print Service

## Grafana Dashboard Structure

```txt
dashboards/
├── business/                    # Practice efficiency metrics
│   ├── patient-flow.json        # Wait times, throughput
│   ├── appointment-metrics.json # Booking rates, cancellations
│   └── clinical-operations.json # Exam completion, prescription accuracy
├── technical/                   # System health and performance
│   ├── service-overview.json    # RED metrics per service
│   ├── infrastructure.json      # K8s cluster, database health
│   └── kafka-monitoring.json    # Message throughput, consumer lag
└── security/                    # HIPAA compliance and audit
    ├── access-patterns.json     # Login attempts, data access
    ├── audit-trail.json         # Sensitive operations tracking
    └── compliance-metrics.json  # Encryption status, retention policies
```

## SLOs & Error Budgets

```yaml
# Service Level Objectives
patient-service:
  availability: 99.9% # 43 minutes downtime/month
  latency_p99: 200ms
  error_rate: <0.1%

scheduling-service:
  availability: 99.95% # 22 minutes downtime/month
  latency_p95: 150ms
  booking_success: >99

clinical-service:
  availability: 99.99% # 4 minutes downtime/month (critical)
  data_accuracy: 99.99%
  save_latency_p95: 100ms
```
