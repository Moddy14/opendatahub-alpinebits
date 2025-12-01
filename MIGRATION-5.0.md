<!--
SPDX-FileCopyrightText: NOI Techpark <digital@noi.bz.it>

SPDX-License-Identifier: CC0-1.0
-->

# Migration Guide: 4.0.1 → 5.0.0

## Overview

Version 5.0.0 represents a major upgrade from Java 8 to Java 11 with full Jakarta EE namespace migration.

**Migration Effort:** 1-3 days (depending on your application complexity)
**Breaking Changes:** Yes - requires code changes in your application
**Rollback:** Not recommended after deployment

---

## Breaking Changes

### 1. Java 11 Required

**Minimum Java version is now 11** (LTS).

**What you need:**
```bash
java -version
# Must show: openjdk version "11.x.x" or later
```

**How to install Java 11:**
- Download from: https://adoptium.net/temurin/releases/?version=11
- Or use: `sudo apt install openjdk-11-jdk` (Ubuntu/Debian)
- Or use: `brew install openjdk@11` (macOS)

---

### 2. Jakarta Namespace Migration

All `javax.*` namespaces have been migrated to `jakarta.*`:

#### 2.1 Jakarta Servlet (jakarta.servlet.*)

**ALREADY migrated in 4.0.1** - No action needed if you're on 4.0.1+

```java
// OLD (4.0.0 and earlier)
import javax.servlet.*;

// NEW (4.0.1+)
import jakarta.servlet.*;
```

---

#### 2.2 Jakarta Persistence (jakarta.persistence.*)

**NEW in 5.0.0** - Requires code changes!

```java
// OLD (4.0.1 and earlier)
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.EntityManager;

// NEW (5.0.0+)
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.EntityManager;
```

**Your application changes:**
1. Update all `import javax.persistence.*` to `import jakarta.persistence.*`
2. Update `persistence.xml` (see section 3 below)
3. Upgrade to Hibernate 6.x or compatible JPA provider

---

#### 2.3 Jakarta XML Binding (jakarta.xml.bind.*)

**NEW in 5.0.0** - Requires code changes!

```java
// OLD (4.0.1 and earlier)
import javax.xml.bind.*;

// NEW (5.0.0+)
import jakarta.xml.bind.*;
```

**Your application changes:**
1. Update all `import javax.xml.bind.*` to `import jakarta.xml.bind.*`
2. Update all `@XmlRootElement`, `@XmlAdapter` imports

---

### 3. Hibernate 6.x

Version 5.0.0 uses **Hibernate 6.5.2.Final** (jakarta.persistence).

**If you're using JPA in your application:**

#### 3.1 Update persistence.xml

```xml
<!-- OLD (4.0.1) -->
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence
             http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd"
             version="2.1">

    <properties>
        <property name="javax.persistence.jdbc.driver" value="..."/>
        <property name="javax.persistence.jdbc.url" value="..."/>
    </properties>
</persistence>

<!-- NEW (5.0.0) -->
<persistence xmlns="https://jakarta.ee/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="https://jakarta.ee/xml/ns/persistence
             https://jakarta.ee/xml/ns/persistence/persistence_3_1.xsd"
             version="3.1">

    <properties>
        <property name="jakarta.persistence.jdbc.driver" value="..."/>
        <property name="jakarta.persistence.jdbc.url" value="..."/>
    </properties>
</persistence>
```

#### 3.2 Update Your Dependencies

```xml
<dependencies>
    <!-- OLD -->
    <dependency>
        <groupId>it.bz.opendatahub.alpinebits</groupId>
        <artifactId>alpinebits-db-api</artifactId>
        <version>4.0.1</version>
    </dependency>

    <!-- NEW -->
    <dependency>
        <groupId>it.bz.opendatahub.alpinebits</groupId>
        <artifactId>alpinebits-db-api</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

#### 3.3 Hibernate 6.x API Changes

Some Hibernate APIs have changed. Check the official migration guide:
- https://docs.jboss.org/hibernate/orm/6.5/migration-guide/migration-guide.html

**Common issues:**
- Some deprecated methods removed
- Transaction handling may need adjustments
- H2 database compatibility (upgrade H2 if needed)

---

### 4. Maven Dependency Updates

Update your `pom.xml`:

```xml
<properties>
    <!-- Minimum versions -->
    <java.version>11</java.version>
    <maven.compiler.release>11</maven.compiler.release>

    <!-- AlpineBits -->
    <alpinebits.version>5.0.0</alpinebits.version>
</properties>
```

---

## Step-by-Step Migration for Your Application

### Step 1: Update Java Version (30 minutes)

1. Install Java 11 (see section 1 above)
2. Update `JAVA_HOME`:
   ```bash
   export JAVA_HOME=/path/to/jdk-11
   export PATH=$JAVA_HOME/bin:$PATH
   ```
3. Verify:
   ```bash
   java -version
   mvn -version
   ```

---

### Step 2: Update AlpineBits Dependency (5 minutes)

In your `pom.xml`:

```xml
<!-- Change version -->
<dependency>
    <groupId>it.bz.opendatahub.alpinebits</groupId>
    <artifactId>alpinebits-servlet-impl</artifactId>
    <version>5.0.0</version>  <!-- was 4.0.1 -->
</dependency>
```

---

### Step 3: Migrate jakarta.persistence Imports (1-2 hours)

**If you use alpinebits-db module:**

1. Find all files with `javax.persistence`:
   ```bash
   grep -r "import javax.persistence" src/
   ```

2. Replace:
   ```bash
   # Linux/macOS/Git Bash
   find src/ -name "*.java" -type f -exec sed -i 's/import javax\.persistence\./import jakarta.persistence./g' {} +

   # Windows PowerShell
   Get-ChildItem -Recurse -Filter *.java | ForEach-Object {
       (Get-Content $_.FullName) -replace 'import javax\.persistence\.', 'import jakarta.persistence.' | Set-Content $_.FullName
   }
   ```

3. Update `persistence.xml` (see section 3.1 above)

---

### Step 4: Migrate jakarta.xml.bind Imports (1-2 hours)

**If you use JAXB/XML processing:**

1. Find all files:
   ```bash
   grep -r "import javax.xml.bind" src/
   ```

2. Replace:
   ```bash
   # Linux/macOS/Git Bash
   find src/ -name "*.java" -type f -exec sed -i 's/import javax\.xml\.bind\./import jakarta.xml.bind./g' {} +

   # Windows PowerShell
   Get-ChildItem -Recurse -Filter *.java | ForEach-Object {
       (Get-Content $_.FullName) -replace 'import javax\.xml\.bind\.', 'import jakarta.xml.bind.' | Set-Content $_.FullName
   }
   ```

---

### Step 5: Test Your Application (2-4 hours)

```bash
# Clean build
mvn clean compile

# Run tests
mvn clean test

# Integration tests
mvn clean verify

# If using Docker
docker-compose build
docker-compose up -d
```

**Expected issues:**
- Compilation errors if imports not updated
- Test failures if persistence.xml not updated
- ClassNotFoundException for javax.* classes

---

### Step 6: Deploy

```bash
# Production build
mvn clean package -DskipTests

# Deploy WAR/JAR as usual
```

---

## Compatibility Matrix

| Component | Version 4.0.1 | Version 5.0.0 |
|-----------|---------------|---------------|
| Java | 8 | 11+ |
| Servlet API | javax.servlet 3.1 | jakarta.servlet 6.0 |
| Persistence API | javax.persistence 2.2 | jakarta.persistence 3.1 |
| XML Binding | javax.xml.bind 2.3 | jakarta.xml.bind 4.0 |
| Tomcat | 9.0.x | 10.1+ |
| Hibernate | 5.6.x | 6.5.x |
| Jackson | 2.17.1 | 2.18.2 |

---

## Troubleshooting

### Issue: ClassNotFoundException for javax.persistence.*

**Solution:** You forgot to update imports. Run the sed command from Step 3.

---

### Issue: Tests fail with "No Persistence provider"

**Solution:** Update your `persistence.xml` namespace and property names (see section 3.1).

---

### Issue: JAXB Marshalling errors

**Solution:** Update all `javax.xml.bind` imports to `jakarta.xml.bind`.

---

### Issue: Servlet container errors

**Solution:** Ensure you're using Tomcat 10.1+ or Jakarta Servlet 6.0 compatible container.

---

## Rollback

**NOT RECOMMENDED** - Version 5.0.0 uses different namespaces than 4.0.1.

If you must rollback:
1. Revert code changes (imports)
2. Downgrade AlpineBits dependency to 4.0.1
3. Use Java 8
4. Revert persistence.xml

---

## Support

For questions or issues:
- GitHub Issues: https://github.com/noi-techpark/odh-alpinebits/issues
- Documentation: https://github.com/noi-techpark/odh-alpinebits

---

## Credits

Migration performed by Claude Code with comprehensive testing and validation.
