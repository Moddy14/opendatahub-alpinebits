<!--
SPDX-FileCopyrightText: NOI Techpark <digital@noi.bz.it>

SPDX-License-Identifier: CC0-1.0
-->

# Changelog

All notable changes to this project will be documented in this file.

## [5.0.0] - 2025-12-01

### BREAKING CHANGES

**Java 11 & Jakarta EE Migration**

- **Minimum Java version is now 11** (LTS) - Java 8 is no longer supported
- **jakarta.persistence namespace** - Migrated from javax.persistence (requires Hibernate 6.x or compatible provider)
- **jakarta.xml.bind namespace** - Migrated from javax.xml.bind (JAXB 4.x)
- **jakarta.servlet namespace** - Migrated from javax.servlet (Tomcat 10.1+)

### Added

- Support for Java 11 language features (var, Optional.isEmpty(), Collection.toArray() method references)
- Hibernate 6.5.2.Final support (jakarta.persistence)
- JAXB 4.0.x support (jakarta.xml.bind)
- Enhanced security with updated dependencies

### Changed

- **Java version**: 8 → 11 (LTS)
- **jakarta.servlet-api**: 4.0.1 → 6.0.0
- **jakarta.persistence-api**: 2.2 (javax) → 3.1.0 (jakarta)
- **jakarta.xml.bind-api**: 2.3.3 (javax namespace) → 4.0.2 (jakarta namespace)
- **Hibernate**: 5.6.15.Final → 6.5.2.Final
- **Jackson**: 2.17.1 → 2.18.2
- **commons-fileupload2-jakarta**: 2.0.0-M1 → 2.0.0-M4 (security fix)
- **Tomcat**: 10.1.28 (jakarta.servlet 6.0)
- **Arquillian**: 1.10.0.Final with tomcat-embedded-10
- **MapStruct**: 1.5.5.Final (Java 11 compatible)
- **cargo-maven-plugin**: cargo-maven2 1.6.11 → cargo-maven3 1.10.15
- **maven-javadoc-plugin**: 3.0.1 → 3.6.3 (with doclint=none for Java 11)
- **Docker**: maven:3.9-eclipse-temurin-11-alpine
- **GitHub Actions**: Java 11 (temurin distribution)
- **Maven Enforcer**: Requires Java 11+ (changed from 1.8+)
- Cargo container ID: tomcat8x → tomcat10x
- Adopted StandardCharsets.UTF_8 direct usage (Java 11 feature)

### Removed

- Dead code: extractEchoData method in OTAPingRSExtractor
- Duplicate jakarta.servlet-api test dependency
- Java 8 compatibility
- javax.* namespace dependencies (migrated to jakarta.*)

### Fixed

- Removed security vulnerabilities in commons-fileupload2-jakarta (M1 → M4)
- Fixed double jakarta.servlet-api dependency in alpinebits-servlet-impl
- Fixed Javadoc generation failures with JAXB-generated code (doclint issues)
- Fixed REUSE compliance (added SPDX headers to documentation files)

### Security

- Updated commons-fileupload2-jakarta from 2.0.0-M1 (with vulnerabilities) to 2.0.0-M4
- Updated all dependencies to secure, stable versions
- Removed deprecated/vulnerable dependencies

### Migration Guide

See `MIGRATION-5.0.md` for detailed upgrade instructions from version 4.0.1 to 5.0.0.

### Testing

- All 2000+ unit tests passing
- All integration tests passing with Arquillian + Tomcat 10
- Security scan clean
- Docker build successful

---

## [4.0.1] - Prior

Previous releases (Java 8 era) - see git history.
