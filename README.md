# M2_Ecom - Spring Boot E-commerce Application

A Spring Boot e-commerce application with PostgreSQL and pgAdmin, fully containerized using Docker and Docker Compose.

## 📌 Prerequisites
- Docker
- Docker Compose
- Maven
- JDK 21

## 📂 Project Structure
```
M2_Ecom/
├── src/                    # Spring Boot source code
├── pom.xml                # Maven dependencies
├── Dockerfile             # Builds Spring Boot app image
├── docker-compose.yml     # Docker Compose configuration
├── application.properties # Spring Boot configuration
└── README.md              # Documentation
```

## ⚙️ Configuration

### `Dockerfile`
```dockerfile
FROM openjdk:21-jdk
WORKDIR /app
COPY target/myapp.jar app.jar
ENTRYPOINT ["java","-jar","app.jar"]
```

### `docker-compose.yml`
```yaml
version: '3.8'
services:
  db:
    image: postgres:latest
    container_name: postgres-db
    environment:
      POSTGRES_DB: ecomdb
      POSTGRES_USER: root
      POSTGRES_PASSWORD: root
    ports:
      - "5432:5432"
    networks:
      - ecom-network
  app:
    build: .
    container_name: springboot-app
    ports:
      - "8080:8085"
    depends_on:
      - db
    networks:
      - ecom-network
  pgadmin:
    image: dpage/pgadmin4:8
    container_name: pgadmin
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin
      PGADMIN_CONFIG_SERVER_MODE: "False"
      PGADMIN_CONFIG_MASTER_PASSWORD_REQUIRED: "False"
    ports:
      - "5050:80"
    depends_on:
      - db
    networks:
      - ecom-network
networks:
  ecom-network:
    driver: bridge
```

### `pom.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.5.0</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.atuluttam</groupId>
	<artifactId>M2_Ecom</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>M2_Ecom</name>
	<description>Demo project for Spring Boot</description>
	<url/>
	<licenses>
		<license/>
	</licenses>
	<developers>
		<developer/>
	</developers>
	<scm>
		<connection/>
		<developerConnection/>
		<tag/>
		<url/>
	</scm>
	<properties>
		<java.version>21</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<version>42.7.3</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
			<version>3.5.0</version>
		</dependency>
	</dependencies>
	<build>
		<finalName>myapp</finalName>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

### `application.properties`
Configures the Spring Boot application to connect to the PostgreSQL database with JPA/Hibernate settings.

## 🚀 Build & Run Steps
1. **Package Spring Boot JAR**
   ```bash
   mvn clean package -DskipTests
   ```
   Generates: `target/myapp.jar`

2. **Build Docker Image & Start Containers**
   ```bash
   docker compose up --build -d
   ```
   - `--build`: Rebuilds the Spring Boot image after changes
   - `-d`: Runs containers in the background

3. **Verify Running Containers**
   ```bash
   docker ps
   ```
   Expected output:
   - `springboot-app` (image: `myapp:v2`)
   - `postgres-db` (image: `postgres:latest`)
   - `pgadmin` (image: `dpage/pgadmin4:8`)

4. **Access Services**
   - Spring Boot app: [http://localhost:8080](http://localhost:8080)
   - pgAdmin: [http://localhost:5050](http://localhost:5050)
     - Email: `admin@admin.com`
     - Password: `admin`
   - In pgAdmin, connect to:
     - Host: `db`
     - Port: `5432`
     - User: `root`
     - Password: `root`
     - Database: `ecomdb`

5. **Check Database Inside Container**
   ```bash
   docker exec -it postgres-db psql -U root -d ecomdb
   ```
   Run in psql:
   ```sql
   SELECT current_database(), current_user, current_setting('TimeZone');
   ```

## 🛑 Stop Containers
```bash
docker compose down
```
To remove volumes (fresh database):
```bash
docker compose down -v
```

## 🧰 Useful Commands
- View logs:
  ```bash
  docker logs springboot-app
  docker logs postgres-db
  ```
- Rebuild only Spring Boot app:
  ```bash
  docker compose build app
  ```
- Restart services:
  ```bash
  docker compose restart
  ```

## ✅ Quick Start
Run the following to start everything:
```bash
mvn clean package -DskipTests
docker compose up --build -d
```
This will bring up the Spring Boot app, PostgreSQL, and pgAdmin, ready to use.
