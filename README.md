🛒 M2_Ecom – Spring Boot + PostgreSQL + Docker Compose
This project is a Spring Boot e-commerce application integrated with a PostgreSQL database and pgAdmin for database management, orchestrated using Docker Compose. It includes configurations to handle timezone and authentication issues for seamless connectivity.

📌 Prerequisites

Docker
Docker Compose
Maven
JDK 17 or higher


📂 Project Structure
M2_Ecom/
├── src/                    # Spring Boot source code
├── pom.xml                # Maven dependencies
├── docker-compose.yml     # Docker Compose configuration
├── application.properties # Spring Boot configuration
└── README.md             # Project documentation


⚙️ Configuration
docker-compose.yml
Sets up PostgreSQL and pgAdmin, creating the ecomdb database automatically.
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

init.sql (create in the same directory):
CREATE DATABASE ecomdb;

application.properties
Configures Spring Boot to connect to PostgreSQL with proper timezone settings.
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


🚀 Running the Application
1️⃣ Start PostgreSQL and pgAdmin
docker-compose up -d


PostgreSQL: localhost:5432
pgAdmin: http://localhost:5050
Email: pgadmin4@pgadmin.org
Password: admin



Configure pgAdmin:

Log in to pgAdmin.
Add a server:
Host: postgres_container
Port: 5432
Username: root
Password: root
Database: ecomdb



2️⃣ Build and Run Spring Boot
Set the JVM timezone to avoid Asia/Calcutta errors:
$env:JAVA_TOOL_OPTIONS="-Duser.timezone=Asia/Kolkata"

Build and run:
mvn clean package
mvn spring-boot:run

Or run the JAR:
java -Duser.timezone=Asia/Kolkata -jar target/M2_Ecom-0.0.1-SNAPSHOT.jar

In IntelliJ:

Add -Duser.timezone=Asia/Kolkata to Run > Edit Configurations > VM options.

3️⃣ Verify Database Connection
In psql:
docker exec -it postgres_container psql -U root -d ecomdb

Run:
SELECT current_database(), current_user, current_setting('TimeZone');

Expected:
 current_database | current_user | current_setting
------------------|--------------|-----------------
 ecomdb           | root         | Asia/Kolkata

4️⃣ Access the Application

REST API: http://localhost:8080
Actuator: http://localhost:8080/actuator


🛑 Stopping Containers
docker-compose down

Clear database data:
docker-compose down -v


🧰 Useful Commands

List containers:docker ps


Access PostgreSQL:docker exec -it postgres_container psql -U root -d ecomdb


Check timezone:SHOW TIMEZONE;


View logs:docker logs postgres_container




🐞 Troubleshooting

Authentication Error (FATAL: password authentication failed for user "root"):
Check for conflicting PostgreSQL instances:netstat -ano | findstr :5432

Stop local PostgreSQL:net stop postgresql-x64-XX


Reset container:docker compose down -v
docker compose up -d




Timezone Error (FATAL: invalid value for parameter "TimeZone": "Asia/Calcutta"):
Ensure ?timezone=Asia/Kolkata in spring.datasource.url.
Set -Duser.timezone=Asia/Kolkata.


Database Not Found:
Verify ecomdb exists:docker exec -it postgres_container psql -U root -d postgres -c "\l"




Debug Logs:logging.level.org.springframework=DEBUG
logging.level.org.hibernate=DEBUG
logging.level.org.postgresql=DEBUG




🔐 Security Note
Use secure credentials in production:

Create .env:POSTGRES_USER=secure_user
POSTGRES_PASSWORD=secure_password


Update application.properties and docker-compose.yml to use environment variables.



