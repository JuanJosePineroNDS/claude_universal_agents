---
description: Comprehensive Java and Spring Boot code review for patterns, security, and performance
---

# Java Review

Run a comprehensive Java code review using the `java-reviewer` agent.

## Workflow

1. **Detect project type**: Maven (`pom.xml`) or Gradle (`build.gradle`)
2. **Identify framework**: Spring Boot, Quarkus, Micronaut, or plain Java
3. **Run the java-reviewer agent** on changed or specified files
4. **Review checklist**:
   - Spring Boot best practices (injection, configuration, profiles)
   - JPA/Hibernate patterns (N+1, lazy loading, projections)
   - Security (authentication, authorization, input validation)
   - Exception handling (global handlers, proper HTTP status codes)
   - Thread safety and concurrency patterns
   - Logging (structured, appropriate levels, no sensitive data)
   - Test coverage (unit, integration, slice tests)
5. **Report** findings grouped by severity: critical > warning > suggestion

## Usage

```
/java-review              # Review all uncommitted changes
/java-review src/main/    # Review specific directory
```
