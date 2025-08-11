# Reproducible Builds and Supply Chain Security

## Overview

Healthcare systems require comprehensive supply chain security to protect patient data and ensure regulatory compliance. This section outlines our approach to secure software supply chain management.

## Dependency Management & Verification

### Gradle Dependency Locking

```kotlin
// build.gradle.kts
dependencyLocking {
    lockAllConfigurations()
}
```

**Generate lock files:**

```bash
./gradlew dependencies --write-locks
```

### Cryptographic Verification

```bash
# Generate dependency verification metadata with SHA-256 hashes
./gradlew --write-verification-metadata sha256 dependencies
```

Creates `gradle/verification-metadata.xml` with cryptographic hashes for all dependencies to detect tampering or substitution attacks.

## Software Bill of Materials (SBOM)

### SBOM Generation

```kotlin
// build.gradle.kts
plugins {
    id("org.cyclonedx.bom") version "1.8.2"
}

cyclonedxBom {
    includeConfigs = ["runtimeClasspath"]
    skipConfigs = ["compileClasspath", "testCompileClasspath"]
    projectType = "application"
    schemaVersion = "1.4"
    destination = file("build/reports")
    outputName = "patient-service-bom"
    outputFormat = "json"
}
```

**Generate SBOM:**

```bash
./gradlew cyclonedxBom
```

**Output includes:**

- Complete dependency inventory with versions
- License information for compliance
- Known vulnerabilities (CVE references)
- Package URLs (PURLs) for precise identification

## Vulnerability Scanning

### OWASP Dependency Check

```kotlin
// build.gradle.kts
plugins {
    id("org.owasp.dependencycheck") version "8.4.0"
}

dependencyCheck {
    nvd.apiKey = System.getenv("NVD_API_KEY")
    failBuildOnCVSS = 7.0f  // Fail build on high severity vulnerabilities
    suppressionFile = "dependency-check-suppressions.xml"
    analyzers {
        assemblyEnabled = false
        nuspecEnabled = false
        nugetconfEnabled = false
    }
}
```

### License Compliance

```kotlin
// build.gradle.kts
plugins {
    id("com.github.jk1.dependency-license-report") version "2.5"
}

licenseReport {
    allowedLicensesFile = file("config/allowed-licenses.json")
    filters = arrayOf(com.github.jk1.license.filter.LicenseBundleNormalizer())
}
```

## Build Provenance & Attestations

### SLSA Build Provenance

```yaml
# .github/workflows/build.yml
name: Build with Provenance
on: [push, pull_request]

jobs:
  build:
    permissions:
      id-token: write # For SLSA provenance
      contents: read
      attestations: write
    steps:
      - uses: actions/checkout@v4
      - name: Build application
        run: ./gradlew bootJar

      - name: Generate artifact hash
        id: hash
        run: |
          echo "hashes=$(sha256sum build/libs/*.jar | base64 -w0)" >> "$GITHUB_OUTPUT"

      - name: Generate SLSA provenance
        uses: slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@v1.9.0
        with:
          base64-subjects: "${{ steps.hash.outputs.hashes }}"
```

### Sigstore Integration

```bash
# Keyless signing with Cosign
cosign sign --yes $CONTAINER_IMAGE

# Attach SBOM as attestation
cosign attest --yes --predicate sbom.json --type cyclonedx $CONTAINER_IMAGE
```

## Container Security

### Multi-stage Hardened Builds

```dockerfile
# Build stage
FROM gradle:8-jdk17-alpine AS builder
WORKDIR /app
COPY build.gradle.kts settings.gradle.kts ./
COPY gradle gradle
COPY src src
RUN ./gradlew bootJar --no-daemon

# Runtime stage - distroless for minimal attack surface
FROM gcr.io/distroless/java17-debian12:nonroot
WORKDIR /app
COPY --from=builder /app/build/libs/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Container Scanning

```yaml
# .github/workflows/container-scan.yml
- name: Run Trivy vulnerability scanner
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: "${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}"
    format: "sarif"
    output: "trivy-results.sarif"

- name: Upload Trivy scan results
  uses: github/codeql-action/upload-sarif@v2
  with:
    sarif_file: "trivy-results.sarif"
```

## Runtime Security

### Security Context (Kubernetes)

```yaml
# k8s/patient-service-deployment.yaml
securityContext:
  runAsNonRoot: true
  runAsUser: 65534
  runAsGroup: 65534
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false
  capabilities:
    drop:
      - ALL
```

### Network Policies

```yaml
# k8s/network-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: patient-service-netpol
spec:
  podSelector:
    matchLabels:
      app: patient-service
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: api-gateway
      ports:
        - protocol: TCP
          port: 8080
```

## HIPAA Compliance Requirements

### Audit Trail

- **Build provenance**: SLSA attestations for all artifacts
- **Deployment tracking**: GitOps with ArgoCD audit logs
- **Access control**: RBAC for CI/CD pipelines and registries
- **Change management**: Required approvals for production deployments

### Encryption Requirements

- **At rest**: Container registry encryption, artifact signing
- **In transit**: TLS 1.3 for all communications, mTLS between services
- **Build time**: Encrypted CI/CD secrets, secure credential management

### Vulnerability Management

- **SLA**: Critical vulnerabilities patched within 24 hours
- **Scanning frequency**: Daily automated scans, immediate on new dependencies
- **Exception process**: Documented risk acceptance for suppressed vulnerabilities
- **Reporting**: Weekly vulnerability reports to security team

## Implementation Phases

### Phase 1: Foundation (Week 1-2)

- [ ] Gradle dependency locking and verification
- [ ] OWASP dependency check integration
- [ ] Basic SBOM generation
- [ ] Container vulnerability scanning

### Phase 2: Attestation (Week 3-4)

- [ ] SLSA build provenance implementation
- [ ] Sigstore keyless signing
- [ ] Container image attestation
- [ ] Policy enforcement with OPA

### Phase 3: Production Hardening (Month 2)

- [ ] Comprehensive vulnerability management
- [ ] HIPAA audit trail implementation
- [ ] Incident response procedures
- [ ] SOC 2 evidence collection automation

## Tools & Dependencies

### Build-time Security

```kotlin
// Security scanning plugins
id("org.owasp.dependencycheck") version "8.4.0"
id("org.cyclonedx.bom") version "1.8.2"
id("com.github.jk1.dependency-license-report") version "2.5"
```

### Runtime Security Tools

- **Trivy**: Container and dependency vulnerability scanning
- **Falco**: Runtime security monitoring
- **OPA Gatekeeper**: Kubernetes policy enforcement
- **Prometheus**: Security metrics and alerting

### Compliance & Audit

- **SLSA**: Build provenance attestation
- **Sigstore**: Keyless artifact signing
- **CycloneDX**: SBOM generation and management
- **SPDX**: License compliance reporting

## Security Contacts

- **Security Team**: security@normals.health
- **Incident Response**: ir@normals.health
- **Compliance Officer**: compliance@normals.health

## References

- [SLSA Framework](https://slsa.dev/)
- [Sigstore Documentation](https://docs.sigstore.dev/)
- [OWASP Dependency Check](https://owasp.org/www-project-dependency-check/)
- [CycloneDX SBOM Standard](https://cyclonedx.org/)
- [NIST Secure Software Development Framework](https://csrc.nist.gov/Projects/ssdf)
