## **README.md for J2EE Security with Database**

# 🔐 J2EE Security with Database Integration

> Demonstrates securing a Jakarta EE application using a database-backed IdentityStore, JPA persistence, and container-managed JDBC connection pooling.

---

## 📑 Table of Contents

1. [🚧 Prerequisites](#-prerequisites)
2. [🛠️ Database Setup](#️-database-setup)
3. [🔌 Configure JDBC Pool & DataSource](#-configure-jdbc-pool--datasource)
4. [⚙️ JPA & `persistence.xml` Configuration](#-jpa--persistencexml-configuration)
5. [🔍 Theory Behind Persistence & Security](#-theory-behind-persistence--security)
6. [📂 Project Structure](#-project-structure)
7. [🛡️ Security Components](#️-security-components)
8. [🚀 Deploy & Run](#-deploy--run)
9. [📷 Screenshots](#-screenshots)
10. [🤝 Contributing](#-contributing)
11. [📜 License](#-license)

---

## 🚧 Prerequisites

* **Java 11+**, **Maven**, **WildFly/Payara/Tomcat**
* **MySQL** (or any relational DB)
* Basic knowledge of JPA and Jakarta Security APIs

---

## 🛠️ Database Setup

Follow these steps to create the user and roles tables:

1. **Login to MySQL**:

   ```bash
   mysql -u root -p
   ```
2. **Create database**:

   ```sql
   CREATE DATABASE j2ee_security_db;
   USE j2ee_security_db;
   ```

---

## 🔌 Configure JDBC Pool & DataSource

Configure your application server to expose a JNDI datasource:

### WildFly (standalone.xml)

```xml
<datasources>
  <datasource jndi-name="java:/jdbc/SecurityDS" pool-name="SecurityPool">
    <connection-url>jdbc:mysql://localhost:3306/security_demo</connection-url>
    <driver>mysql</driver>
    <security>
      <user-name>dbuser</user-name>
      <password>dbpass</password>
    </security>
  </datasource>
</datasources>
```

### Tomcat (context.xml)

```xml
<Resource name="jdbc/SecurityDS"
          auth="Container"
          type="javax.sql.DataSource"
          driverClassName="com.mysql.cj.jdbc.Driver"
          url="jdbc:mysql://localhost:3306/security_demo"
          username="dbuser" password="dbpass"
          maxTotal="20" maxIdle="5"/>
```

---

## ⚙️ JPA & `persistence.xml` Configuration

Place under `src/main/resources/META-INF/persistence.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<persistence xmlns="https://jakarta.ee/xml/ns/persistence" version="3.0">
    <persistence-unit name="j2eeSecuredAppDB" transaction-type="JTA">
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <jta-data-source>j2ee_security_db</jta-data-source>
        <properties>
            <property name="hibernate.dialect" value="org.hibernate.dialect.MySQLDialect"/>
            <property name="hibernate.transaction.jta.platform" value="org.hibernate.engine.transaction.jta.platform.internal.SunOneJtaPlatform"/>
            <property name="hibernate.hbm2ddl.auto" value="update"/>
            <property name="hibernate.show_sql" value="true"/>
        </properties>
    </persistence-unit>
</persistence>
```

---

## 🔍 Theory Behind Persistence & Security

* **Connection Pooling**: Reuses physical DB connections, reducing overhead.
* **JTA & JPA**: Container-managed transactions ensure consistency across multiple resources.
* **IdentityStore with JPA**: Retrieves users/roles from DB, promoting separation of concerns.
* **BCrypt hashing**: Securely stores passwords; compare via `IdentityStore.validate()`.

---

## 📂 Project Structure

```
src/main/java/com/tharindu/security_db/
├── config/
├── DAO/
├── DTO/
├── Util/
├── Entity/
    └── User.java              # User Entity Class
├── security/
│   ├── AppIdentityStore.java  # Implements IdentityStore via JPA
│   └── AuthMechanism.java     # HttpAuthenticationMechanism
├── service/
    └── UserService.java        # User Login Service handling class
├── servlet/
│   ├── Login.java              # Handles login POST (No Design)
│   └── Profile.java            # Handles Profile (No Design)
└── webapp/
    ├── user/
         └── index.jsp        # Users UI
    ├── login.jsp
    ├── home.jsp
    ├── index.jsp
    └── WEB-INF/
             └── web.xml    # Security constraints
```

---

## 🛡️ Security Components

* **`DatabaseIdentityStore`**: Loads credentials and roles via JPA, returns `CredentialValidationResult`.
* **`AuthMechanism`**: Calls `identityStore.validate()`, manages login and logout flows.
* **Servlet Constraints**: `web.xml` secures URLs (e.g., `/admin/*` to ADMIN).
* **EJB Annotations**: If present, `@RolesAllowed` on session beans.

---

## 🚀 Deploy & Run

1. Build: `mvn clean package`
2. Deploy WAR to your Jakarta EE container.
3. Access:

   * Login: `/login.jsp`
   * Home: `/home.jsp`
   * Admin area: `/admin/*`

---

## 📷 Screenshots

```
FrontEnd design not created -- This Repo focuses to Backend configurations
```

---

## 🤝 Contributing

Fork → branch → PR. Please include unit/integration tests for security flows.

---

## 📜 License

MIT © 2025 Tharindu714

---

> Securing your app from the ground up with DB, JPA, and Jakarta Security! 🚀
