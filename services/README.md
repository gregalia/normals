# Optometry EMR Services

This directory contains the microservices for the optometry EMR system.

## Gradle Version Catalog for Multi-Service Architecture

### Why We Use Version Catalogs

This EMR system will have **multiple microservices** (patient-service, scheduling-service, clinical-service, billing-service, etc.), each with their own `build.gradle.kts` file. Without centralized dependency management, we'd have problems:

**❌ Problems without Version Catalogs:**

- Same dependency declared with different versions across services
- Difficult to upgrade dependencies consistently
- Version conflicts when services communicate
- Manual maintenance of versions in multiple build files

**✅ Benefits of Version Catalogs:**

- **Centralized Management:** All versions defined once in `gradle/libs.versions.toml`
- **Consistency:** All services use identical dependency versions
- **Type Safety:** IDE autocompletion for dependency names
- **Easy Updates:** Change version once, affects all services
- **Multi-Module Support:** Perfect for microservice architectures

### How Spring Boot BOM Works with Version Catalogs

Spring Boot uses a **Bill of Materials (BOM)** to manage dependency versions automatically. Combined with version catalogs, this creates a powerful dependency management strategy.

#### Version Control Flow

1. **Plugin Version Controls Everything:**

   ```kotlin
   // In gradle/libs.versions.toml (shared across ALL services)
   springBoot = "3.5.3"

   // In each service's build.gradle.kts
   alias(libs.plugins.springBoot)  // All services use Spring Boot 3.5.3
   ```

2. **BOM Import:** The Spring Boot plugin automatically imports `spring-boot-dependencies-3.5.3.pom` which contains ~300+ managed dependency versions.

3. **Automatic Version Resolution:**

   ```kotlin
   // In ANY service - no version specified, Gradle looks it up in the BOM
   implementation(libs.spring.boot.starter.web)
   implementation(libs.spring.kafka)
   ```

4. **Result:** **Every service** gets the exact same dependency versions that Spring Boot 3.5.3 was tested with.

#### Multi-Service Architecture Example

```txt
services/
├── gradle/libs.versions.toml        # Single source of truth for ALL services
├── patient-service/build.gradle.kts # Uses libs.spring.boot.starter.web
├── clinical-service/build.gradle.kts # Uses libs.spring.boot.starter.web (same version!)
├── billing-service/build.gradle.kts  # Uses libs.spring.kafka (same version!)
└── scheduling-service/build.gradle.kts
```

All services automatically stay in sync because they reference the same catalog.

#### What to Version vs. What to Let BOM Manage

**✅ Remove versions (Let Spring Boot BOM manage):**

- All `spring-boot-starter-*` dependencies
- `spring-kafka`, `spring-security-test`, etc.
- Any dependency listed in Spring Boot's BOM

**🔧 Keep explicit versions in catalog:**

- Database drivers (PostgreSQL, etc.) - need specific versions for DB compatibility
- Third-party libraries not managed by Spring Boot
- Dependencies where you specifically need a different version

#### Version Catalog Best Practice

```toml
[versions]
springBoot = "3.5.3"  # This controls ALL Spring ecosystem versions across ALL services
postgresql = "42.7.7"  # DB driver - keep explicit for compatibility

[libraries]
# No version.ref - managed by Spring Boot BOM
spring-boot-starter-web = { group = "org.springframework.boot", name = "spring-boot-starter-web" }
spring-kafka = { group = "org.springframework.kafka", name = "spring-kafka" }

# Explicit version - not managed by Spring Boot
postgresql = { group = "org.postgresql", name = "postgresql", version.ref = "postgresql" }
```

#### Upgrading Dependencies Across All Services

To upgrade the entire Spring ecosystem across **all microservices**:

1. Change `springBoot = "3.5.4"` in `gradle/libs.versions.toml`
2. **Every service** automatically gets compatible versions
3. No need to touch individual service build files
4. No need to research individual dependency compatibility

##### Supply Chain Security

- Generate lock files: `./gradlew :patient-service:dependencies --write-locks`
- Generate verification hashes: `./gradlew :patient-service:dependencies --write-verification-metadata sha256`

This follows Spring Boot's **"convention over configuration"** principle and is essential for maintaining consistency in microservice architectures.

## Services

- `patient-service/` - Patient demographics, medical records, and clinical data management
