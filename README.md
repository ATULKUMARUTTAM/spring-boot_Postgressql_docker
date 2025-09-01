# 🛒 M2_Ecom – Spring Boot + PostgreSQL + Docker Compose

A Spring Boot e-commerce app with PostgreSQL and pgAdmin, using Docker Compose for seamless setup and timezone/auth handling.

## 📌 Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- [Maven](https://maven.apache.org/)
- [JDK 17+](https://adoptium.net/)

## 📂 Project Structure

```
M2_Ecom/
├── src/                    # Spring Boot source code
├── pom.xml                # Maven dependencies
├── docker-compose.yml     # Docker Compose config
├── init.sql               # Database init script
├── application.properties # Spring Boot config
└── README.md             # Project docs
```

## ⚙️ Configuration

### `docker-compose.yml`
Sets up PostgreSQL and pgAdmin, auto-creating `ecomdb`.

```yaml
version: '3.8'
services:
  postgres:
    container_name: postgres_container
    image: postgres:14
    environment:
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
      PGDATA: /data/postgres
      TZ: Asia/Kolkata
    volumes:
      - postgres:/data/postgres
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "5432:5432"
    networks:
      - postgres
    restart: unless-stopped
  pgadmin:
    container_name: pgadmin_container
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: ${PGADMIN_DEFAULT_EMAIL:-pgadmin4@pgadmin.org}
      PGADMIN_DEFAULT_PASSWORD: ${PGADMIN_DEFAULT_PASSWORD:-admin}
      PGADMIN_CONFIG_SERVER_MODE: 'False'
    volumes:
      - pgadmin:/var/lib/pgadmin
    ports:
      - "5050:80"
    networks:
      - postgres
    restart: unless-stopped
networks:
  postgres:
    driver: bridge
volumes:
  postgres:
  pgadmin:
```

**`init.sql`** (create in project root):
```sql
CREATE DATABASE ecomdb;
```

### `application.properties`
Connects Spring Boot to PostgreSQL with `Asia/Kolkata` timezone.

```properties
spring.application.name=M2_Ecom

# PostgreSQL Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/ecomdb?timezone=Asia/Kolkata
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA and Hibernate Configuration
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.jdbc.time_zone=Asia/Kolkata

# Actuator and Management Settings
management.endpoints.web.exposure.include=*
management.info.env.enabled=true
management.endpoint.shutdown.enabled=true

# Server Settings
server.port=8080
server.shutdown=graceful

# Application Info
info.app.name=LPU_APP
info.app.description=Learning Purpose Application
info.app.version=1.0.0
info.app.contact.name=John Doe
```

## 🚀 Running the Application

1. **Start PostgreSQL and pgAdmin**  
   ```bash
   docker-compose up -d
   ```
   - PostgreSQL: `localhost:5432`
   - pgAdmin: `http://localhost:5050`
     - Email: `pgadmin4@pgadmin.org`
     - Password: `admin`

   **Configure pgAdmin**:
   - Log in to pgAdmin.
   - Add server:
     - Host: `postgres_container`
     - Port: `5432`
     - Username: `root`
     - Password: `root`
     - Database: `ecomdb`

2. **Build and Run Spring Boot**  
   Set JVM timezone to avoid `Asia/Calcutta` errors:
   ```powershell
   $env:JAVA_TOOL_OPTIONS="-Duser.timezone=Asia/Kolkata"
   ```
   Build and run:
   ```bash
   mvn clean package
   mvn spring-boot:run
   ```
   Or run JAR:
   ```bash
   java -Duser.timezone=Asia/Kolkata -jar target/M2_Ecom-0.0.1-SNAPSHOT.jar
   ```
   **In IntelliJ**:
   - Add `-Duser.timezone=Asia/Kolkata` to `Run > Edit Configurations > VM options`.

3. **Verify Database Connection**  
   In `psql`:
   ```bash
   docker exec -it postgres_container psql -U root -d ecomdb
   ```
   Run:
   ```sql
   SELECT current_database(), current_user, current_setting('TimeZone');
   ```
   Expected:
   ```
    current_database | current_user | current_setting
   ------------------|--------------|-----------------
    ecomdb           | root         | Asia/Kolkata
   ```

4. **Access the Application**  
   - REST API: `http://localhost:8080`
   - Actuator: `http://localhost:8080/actuator`

## 🛑 Stopping Containers

```bash
docker-compose down
```
Clear database:
```bash
docker-compose down -v
```

## 🧰 Useful Commands

- List containers: `docker ps`
- Access PostgreSQL: `docker exec -it postgres_container psql -U root -d ecomdb`
- Check timezone: `SHOW TIMEZONE;`
- View logs: `docker logs postgres_container`

## 🐞 Troubleshooting

- **Authentication Error**:
  - Check for conflicting PostgreSQL:
    ```powershell
    netstat -ano | findstr :5432
    ```
  - Stop local PostgreSQL:
    ```powershell
    net stop postgresql-x64-XX
    ```
  - Reset container:
    ```bash
    docker compose down -v
    docker compose up -d
    ```
- **Timezone Error**:
  - Use `?timezone=Asia/Kolkata` in `spring.datasource.url`.
  - Set `-Duser.timezone=Asia/Kolkata`.
- **Database Not Found**:
  - Verify `ecomdb`:
    ```bash
    docker exec -it postgres_container psql -U root -d postgres -c "\l"
    ```
- **Debug Logs**:
  ```properties
  logging.level.org.springframework=DEBUG
  logging.level.org.hibernate=DEBUG
  logging.level.org.postgresql=DEBUG
  ```

## 🔐 Security Note

Use secure credentials in production:
1. Create `.env`:
   ```env
   POSTGRES_USER=secure_user
   POSTGRES_PASSWORD=secure_password
   PGADMIN_DEFAULT_EMAIL=your_email@example.com
   PGADMIN_DEFAULT_PASSWORD=secure_password
   ```
2. Update `application.properties`:
   ```properties
   spring.datasource.username=${POSTGRES_USER}
   spring.datasource.password=${POSTGRES_PASSWORD}
   ```
3. Update `docker-compose.yml`:
   ```yaml
   environment:
     POSTGRES_USER: ${POSTGRES_USER}
     POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
   ```

## 📚 Resources

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [PostgreSQL JDBC](https://jdbc.postgresql.org/)
- [Docker Compose](https://docs.docker.com/compose/)
- [pgAdmin](https://www.pgadmin.org/docs/)
