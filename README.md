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
### `docker-compose.yml`
Defines three services:
- **db**: PostgreSQL database
- **app**: Spring Boot application
- **pgadmin**: pgAdmin for database management

### `Dockerfile`
Builds the Spring Boot application image using `openjdk:21-jdk`.

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
