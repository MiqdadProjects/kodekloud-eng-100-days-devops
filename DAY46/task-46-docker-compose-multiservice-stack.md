# 🌟 Task 46 - Deploy Docker Compose Stack on App Server 1

**📌 Task Description**

The **Nautilus Application Development team** has developed an application to be deployed on a containerized platform. The team wants to test the deployment on **App Server 1** in the Stratos Datacenter using a Docker Compose file before going live. The task is to create a Docker Compose file to deploy a stack with two services: a web service (PHP with Apache) and a database service (MariaDB).

**👉 Task Requirements**:
- Create a Docker Compose file at `/opt/devops/docker-compose.yml` (exact name).
- Deploy two services: `web` (container name: `php_host`) and `db` (container name: `mysql_host`).
- **Web Service**:
  - Use image: `php:apache`.
  - Map container port 80 to host port 6100.
  - Map host volume `/var/www/html` to container volume `/var/www/html`.
- **DB Service**:
  - Use image: `mariadb:latest`.
  - Map container port 3306 to host port 3306.
  - Map host volume `/var/lib/mysql` to container volume `/var/lib/mysql`.
  - Set environment variables: `MYSQL_DATABASE=database_host`, `MYSQL_USER` (non-root user), `MYSQL_PASSWORD` (complex password), `MYSQL_ROOT_PASSWORD`.
- Deploy the stack in detached mode.

**💡 Note**: Infrastructure details are available via the **Details of all Users and Servers** button in the KodeKloud lab interface.

---

## 🔹 Step 1: Connect to App Server 1

```bash
ssh tony@stapp01
sudo su -
```

**Purpose**: Connect to App Server 1 as the `tony` user and switch to `root` for Docker operations.

---

## 🔹 Step 2: Navigate to the DevOps Directory

```bash
cd /opt/devops
```

**Purpose**: Navigate to the directory where the Docker Compose file will be created.

**Verify Location**:
```bash
pwd
ls -la
```

---

## 🔹 Step 3: Create the Docker Compose File

```bash
vi /opt/devops/docker-compose.yml
```

**Purpose**: Create and edit the Docker Compose file with the required configuration.

---

## 🔹 Step 4: Add Docker Compose Content

**Docker Compose File**:

```yaml
version: '3.8'

services:
  web:
    image: php:apache
    container_name: php_host
    ports:
      - "6100:80"
    volumes:
      - /var/www/html:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:latest
    container_name: mysql_host
    environment:
      MYSQL_DATABASE: database_host
      MYSQL_USER: devuser
      MYSQL_PASSWORD: ComplexP@ss123
      MYSQL_ROOT_PASSWORD: rootP@ss456
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
```

**Key Configurations**:
- **Version**: Uses `3.8` for compatibility with modern Docker Compose features.
- **Web Service**:
  - `image: php:apache`: Uses PHP with Apache for the web server.
  - `container_name: php_host`: Specifies the exact container name.
  - `ports: ["6100:80"]`: Maps host port 6100 to container port 80.
  - `volumes: /var/www/html:/var/www/html`: Maps host directory to container’s web root.
  - `depends_on: db`: Ensures the DB service starts first.
- **DB Service**:
  - `image: mariadb:latest`: Uses the latest MariaDB image.
  - `container_name: mysql_host`: Specifies the exact container name.
  - `ports: ["3306:3306"]`: Maps host port 3306 to container port 3306.
  - `volumes: /var/lib/mysql:/var/lib/mysql`: Persists database data on the host.
  - `environment`: Sets `MYSQL_DATABASE=database_host`, `MYSQL_USER=devuser`, `MYSQL_PASSWORD=ComplexP@ss123`, `MYSQL_ROOT_PASSWORD=rootP@ss456`.

**Save and Exit**: Press `Esc`, type `:wq`, press `Enter`.

---

## 🔹 Step 5: Verify Docker Compose File

```bash
cat /opt/devops/docker-compose.yml
```

**Purpose**: Confirm the file contains the correct YAML configuration.

**Validation Checklist**:
- ✅ Correct `version: '3.8'`
- ✅ Service names: `web` and `db`
- ✅ Container names: `php_host` and `mysql_host`
- ✅ Correct port mappings: `6100:80` (web), `3306:3306` (db)
- ✅ Volume mappings: `/var/www/html` and `/var/lib/mysql`
- ✅ Environment variables for DB service

---

## 🔹 Step 6: Deploy the Docker Compose Stack

```bash
docker compose up -d
```

**Purpose**: Start the services in detached mode to run in the background.

**Expected Output**:
```
[+] Running 2/2
 ⠿ Container mysql_host  Started
 ⠿ Container php_host   Started
```

---

## 🔹 Step 7: Verify Running Containers

```bash
docker ps
```

**Purpose**: Confirm both containers (`php_host` and `mysql_host`) are running.

**Expected Output**:
```
CONTAINER ID   IMAGE            COMMAND                  CREATED         STATUS         PORTS                              NAMES
abc123def456   php:apache       "docker-php-entryp..."   10 seconds ago  Up 10 seconds  0.0.0.0:6100->80/tcp               php_host
xyz789abc123   mariadb:latest   "docker-entrypoint..."   10 seconds ago  Up 10 seconds  0.0.0.0:3306->3306/tcp             mysql_host
```

---

## 🔹 Step 8: Test Web Service Accessibility

```bash
curl http://localhost:6100
```

**Purpose**: Verify the PHP web service is accessible on port 6100.

**Expected Output**: Depends on the content in `/var/www/html`. If empty, it may return a default Apache or PHP page.

---

## 🔹 Step 9: Test Database Service Connectivity

```bash
docker exec -it mysql_host mysql -u devuser -pComplexP@ss123 -e "SHOW DATABASES;"
```

**Purpose**: Connect to the MariaDB container and verify the `database_host` database exists.

**Expected Output**:
```
+--------------------+
| Database           |
+--------------------+
| database_host      |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

---

## 🔹 Step 10: Check Container Logs

```bash
docker logs php_host
docker logs mysql_host
```

**Purpose**: Ensure no errors in the web or database services.

**Expected Logs (php_host)**:
```
[Sat Oct 25 15:39:00.000000 2025] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.57 (Unix) PHP/8.2.12 configured
```

**Expected Logs (mysql_host)**:
```
[note] Starting MariaDB ... started
```

---

## 🔹 Step 11: Clean Up (Optional)

```bash
docker compose down
```

**Purpose**: Stop and remove containers if testing is complete, preserving volumes.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 1**:

```bash
# Connect and navigate
ssh tony@stapp01
sudo su -
cd /opt/devops

# Create Docker Compose file
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web:
    image: php:apache
    container_name: php_host
    ports:
      - "6100:80"
    volumes:
      - /var/www/html:/var/www/html
    depends_on:
      - db

  db:
    image: mariadb:latest
    container_name: mysql_host
    environment:
      MYSQL_DATABASE: database_host
      MYSQL_USER: devuser
      MYSQL_PASSWORD: ComplexP@ss123
      MYSQL_ROOT_PASSWORD: rootP@ss456
    ports:
      - "3306:3306"
    volumes:
      - /var/lib/mysql:/var/lib/mysql
EOF

# Deploy and verify
docker compose up -d
docker ps
curl http://localhost:6100
docker exec -it mysql_host mysql -u devuser -pComplexP@ss123 -e "SHOW DATABASES;"
```

---

## 💡 Common Docker Compose Issues & Fixes

### **Issue 1: Invalid YAML Syntax**
**Problem**: YAML parsing errors (e.g., indentation issues).
**Fix**: Ensure 2-space indentation and valid syntax.
```bash
docker compose config
```

### **Issue 2: Image Pull Failure**
**Problem**: `php:apache` or `mariadb:latest` fails to pull.
**Fix**: Verify image availability and network connectivity.
```bash
docker pull php:apache
docker pull mariadb:latest
```

### **Issue 3: Port Already in Use**
**Problem**: Host ports 6100 or 3306 are occupied.
**Fix**: Check and free ports.
```bash
netstat -tuln | grep 6100
netstat -tuln | grep 3306
kill -9 <pid>
```

### **Issue 4: Volume Permission Issues**
**Problem**: Containers fail to access `/var/www/html` or `/var/lib/mysql`.
**Fix**: Set appropriate permissions.
```bash
chmod -R 755 /var/www/html
chmod -R 660 /var/lib/mysql
chown -R mysql:mysql /var/lib/mysql
```

---

## 🔧 Troubleshooting Deployment Failures

### **Error: Container Failed to Start**
**Symptoms**: `docker ps -a` shows `Exited` status.
**Solution**: Check logs for errors.
```bash
docker logs php_host
docker logs mysql_host
```

### **Error: Database Connection Failure**
**Symptoms**: Web service cannot connect to `mysql_host`.
**Solution**: Verify environment variables and network.
```bash
docker inspect mysql_host | grep IPAddress
docker exec php_host ping <mysql_host_ip>
```

### **Error: Volume Not Persisting**
**Symptoms**: Data in `/var/lib/mysql` not retained after `docker compose down`.
**Solution**: Ensure host directory exists.
```bash
mkdir -p /var/lib/mysql
ls -ld /var/lib/mysql
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered**:
The primary challenge was configuring a Docker Compose stack with precise requirements for container names, port mappings, volume mounts, and database credentials, ensuring both services work together seamlessly.

**💡 Solution Approach**:
1. **Directory Setup**: Navigated to `/opt/devops` and created `docker-compose.yml`.
2. **YAML Configuration**: Defined services with correct images, ports, volumes, and environment variables.
3. **Dependency Management**: Used `depends_on` to ensure the DB starts before the web service.
4. **Verification**: Tested container status, web accessibility, and database connectivity.
5. **Troubleshooting**: Addressed potential issues like port conflicts or volume permissions.

**🎯 Key Success Factors**:
- **Accurate YAML**: Correct syntax and indentation for `docker-compose.yml`.
- **Environment Variables**: Properly set `MYSQL_USER`, `MYSQL_PASSWORD`, and `MYSQL_DATABASE`.
- **Volume Mapping**: Ensured host paths `/var/www/html` and `/var/lib/mysql` were correctly mounted.
- **Port Configuration**: Mapped ports `6100:80` and `3306:3306` as specified.
- **Testing**: Validated services with `curl` and `mysql` commands.

**⚠️ Critical Compose Fixes**:
- Use `version: '3.8'` for compatibility.
- Specify exact container names (`php_host`, `mysql_host`).
- Ensure complex password (`ComplexP@ss123`) meets security requirements.
- Validate volume paths exist on the host.

**🔒 Docker Compose Best Practices Applied**:
- **Clear Structure**: Organized services with readable YAML.
- **Dependency Handling**: Used `depends_on` for startup order.
- **Persistent Storage**: Mounted volumes for data persistence.
- **Secure Credentials**: Used complex passwords for DB access.

**⚠️ Important Troubleshooting Concepts**:
- **YAML Validation**: Use `docker compose config` to catch syntax errors.
- **Container Logs**: Check logs for startup issues.
- **Network Connectivity**: Ensure services can communicate.
- **Volume Permissions**: Verify host directory permissions.

---

## ⚠️ Important Production Notes

🔧 **Docker Compose Debugging**: Use `docker compose logs` and `docker inspect` to diagnose issues.
🔐 **Security**: Avoid exposing `MYSQL_ROOT_PASSWORD` in production; use secrets management.
📊 **Volume Management**: Ensure host directories (`/var/www/html`, `/var/lib/mysql`) are backed up.
🛡️ **Port Security**: Restrict host ports (6100, 3306) with firewall rules in production.

*Last Updated: October 25, 2025*