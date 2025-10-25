**🌟 Task 44 - Host Static Website on HTTPD Container Using Docker Compose on App Server 2**

**📌 Task Description**

The **Nautilus application development team** has shared static website content that needs to be hosted using the **Apache HTTPD web server** inside a container. The requirements for setup on **App Server 2** in **Stratos DC** are:

- Use a Docker Compose file located at **`/opt/docker/docker-compose.yml`**
- Use the official **`httpd:latest`** image
- Name the container **`httpd`** (service name can be any)
- Map container port **80** to host port **6400**
- Mount host directory **`/opt/itadmin`** to container's **`/usr/local/apache2/htdocs`** (do not modify contents)

👉 **Your task:** Deploy Apache HTTPD container using Docker Compose with volume mounting for static content.

💡 **Note:** You can find the infrastructure details by clicking on the **Details of all Users and Servers** button on the top-right section of the page.

---

## 🔹 Step 1: Connect to App Server 2

```bash
ssh steve@stapp02
sudo su -
```

**Purpose**: Connect to Application Server 2 as steve user and switch to root for Docker Compose operations.

---

## 🔹 Step 2: Verify Docker and Docker Compose

```bash
docker --version
docker compose version
```

**Purpose**: Confirm Docker and Docker Compose plugin are installed and available.

**Expected Output**:
```
Docker version 24.x.x
Docker Compose version v2.x.x
```

---

## 🔹 Step 3: Check static content directory

```bash
ls -la /opt/itadmin
```

**Purpose**: Verify the static website content exists in the source directory.

**Expected Content**: HTML files, CSS, images, or other static web assets.

**Example Output**:
```
-rw-r--r-- 1 root root 1234 Mar 15 10:30 index.html
-rw-r--r-- 1 root root 5678 Mar 15 10:30 style.css
```

---

## 🔹 Step 4: Create Docker Compose directory

```bash
mkdir -p /opt/docker
cd /opt/docker
```

**Purpose**: Ensure the required directory exists and navigate to it.

**Verify location**:
```bash
pwd
```

**Expected Output**: `/opt/docker`

---

## 🔹 Step 5: Create docker-compose.yml file

```bash
vi docker-compose.yml
```

**Purpose**: Create Docker Compose configuration file with service specifications.

---

## 🔹 Step 6: Add Docker Compose configuration

**docker-compose.yml Content:**

```yaml
version: '3'

services:
  webserver:
    image: httpd:latest
    container_name: httpd
    ports:
      - "6400:80"
    volumes:
      - /opt/itadmin:/usr/local/apache2/htdocs
```

**Configuration Breakdown**:
- **version**: Docker Compose file format version
- **services**: Defines service(s) to run
- **webserver**: Service name (can be customized)
- **image**: Uses official Apache HTTPD image
- **container_name**: Sets specific container name
- **ports**: Maps host port 6400 to container port 80
- **volumes**: Mounts host directory to container path

**Save and exit**: Press `Esc`, type `:wq`, press `Enter`

---

## 🔹 Step 7: Verify Docker Compose file syntax

```bash
cat docker-compose.yml
```

**Purpose**: Display file contents to verify correct YAML syntax and configuration.

**YAML Syntax Rules**:
- Indentation matters (use spaces, not tabs)
- Key-value pairs use colon and space
- Lists use dash and space
- Proper nesting structure

---

## 🔹 Step 8: Validate Docker Compose configuration

```bash
docker compose -f /opt/docker/docker-compose.yml config
```

**Purpose**: Validate Docker Compose file syntax and display parsed configuration.

**Expected Result**: Configuration displayed without errors.

---

## 🔹 Step 9: Pull the HTTPD image (optional)

```bash
docker pull httpd:latest
```

**Purpose**: Pre-download the image before starting services (optional step).

**Benefit**: Faster startup time when running docker compose up.

---

## 🔹 Step 10: Start services with Docker Compose

```bash
docker compose -f /opt/docker/docker-compose.yml up -d
```

**Purpose**: Start the Apache HTTPD container in detached mode using Docker Compose.

**Command Options**:
- `-f`: Specify compose file location
- `up`: Create and start containers
- `-d`: Detached mode (background)

**Expected Output**:
```
[+] Running 2/2
 ✔ Network docker_default  Created
 ✔ Container httpd         Started
```

---

## 🔹 Step 11: Verify container is running

```bash
docker ps
```

**Purpose**: Confirm the httpd container is in running state.

**Expected Output**:
```
CONTAINER ID   IMAGE          COMMAND              CREATED         STATUS         PORTS                  NAMES
abc123def456   httpd:latest   "httpd-foreground"   10 seconds ago  Up 9 seconds   0.0.0.0:6400->80/tcp   httpd
```

**Verification Points**:
- ✅ Container name: **httpd**
- ✅ Status: **Up**
- ✅ Port mapping: **0.0.0.0:6400->80/tcp**
- ✅ Image: **httpd:latest**

---

## 🔹 Step 12: Check container logs

```bash
docker logs httpd
```

**Purpose**: View Apache HTTPD startup logs to ensure service started correctly.

**Expected Output**:
```
AH00558: httpd: Could not reliably determine the server's fully qualified domain name...
[date] [mpm_event:notice] [pid 1:tid xxxx] AH00489: Apache/2.4.x (Unix) configured -- resuming normal operations
```

---

## 🔹 Step 13: Verify volume mount

```bash
docker exec httpd ls -la /usr/local/apache2/htdocs
```

**Purpose**: Confirm that host directory is properly mounted inside the container.

**Expected Result**: Should show same files as `/opt/itadmin` on host.

---

## 🔹 Step 14: Test website from host

```bash
curl http://localhost:6400
```

**Purpose**: Test Apache HTTPD is serving static content on the mapped port.

**Expected Output**: HTML content from the mounted static website files.

**Success Indicators**:
- ✅ HTTP 200 OK response
- ✅ HTML content from /opt/itadmin displayed
- ✅ Static website accessible

---

## 🔹 Step 15: Test with HTTP headers

```bash
curl -I http://localhost:6400
```

**Purpose**: View HTTP response headers to confirm Apache is serving requests.

**Expected Output**:
```
HTTP/1.1 200 OK
Date: [timestamp]
Server: Apache/2.4.x (Unix)
Content-Type: text/html
...
```

---

## 🔹 Step 16: View Docker Compose services status

```bash
docker compose -f /opt/docker/docker-compose.yml ps
```

**Purpose**: Display status of all services defined in the compose file.

**Expected Output**:
```
NAME      IMAGE          COMMAND              SERVICE      CREATED         STATUS         PORTS
httpd     httpd:latest   "httpd-foreground"   webserver    2 minutes ago   Up 2 minutes   0.0.0.0:6400->80/tcp
```

---

## 🔹 Step 17: Inspect the service

```bash
docker compose -f /opt/docker/docker-compose.yml logs webserver
```

**Purpose**: View logs for the specific service defined in compose file.

---

## 📋 Quick Command Reference

For quick copy-paste, execute on **App Server 2**:

```bash
# Connect to server
ssh steve@stapp02
sudo su -

# Verify prerequisites
ls -la /opt/itadmin
docker compose version

# Create Docker Compose file
mkdir -p /opt/docker
cd /opt/docker
cat > docker-compose.yml << 'EOF'
version: '3'

services:
  webserver:
    image: httpd:latest
    container_name: httpd
    ports:
      - "6400:80"
    volumes:
      - /opt/itadmin:/usr/local/apache2/htdocs
EOF

# Start services
docker compose -f /opt/docker/docker-compose.yml up -d

# Verify and test
docker ps
docker logs httpd
curl http://localhost:6400
```

---

## 💡 Additional Tips

- **Compose v2 syntax**: Use `docker compose` (not `docker-compose`)
- **Environment variables**: Use `.env` file for configuration values
- **Multiple services**: Add more services in same compose file
- **Service dependencies**: Use `depends_on` for startup order
- **Network isolation**: Compose creates default network automatically

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: YAML syntax errors**
**Symptoms**: Error parsing docker-compose.yml
**Solution**: Check indentation and syntax
```bash
docker compose -f /opt/docker/docker-compose.yml config
# Fix indentation issues (use spaces, not tabs)
```

### **Issue 2: Port already in use**
**Symptoms**: Error "port is already allocated"
**Solution**: Stop conflicting service or change port
```bash
ss -tlnp | grep :6400
# Change port in docker-compose.yml or stop conflicting service
```

### **Issue 3: Volume mount not working**
**Symptoms**: Container shows empty directory
**Solution**: Verify host path exists and permissions
```bash
ls -la /opt/itadmin
# Ensure directory exists with correct permissions
```

### **Issue 4: Container exits immediately**
**Symptoms**: Container status shows "Exited"
**Solution**: Check logs and configuration
```bash
docker logs httpd
docker compose -f /opt/docker/docker-compose.yml logs
```

---

## 🚨 Task-Specific Challenge & Solution

**🔍 Main Challenge Encountered:**

The primary challenge was **deploying Apache HTTPD web server using Docker Compose** with specific requirements for container naming, port mapping, and volume mounting for static content hosting.

**💡 Solution Approach:**

1. **Compose File Creation**: Created docker-compose.yml at specified location `/opt/docker/`
2. **Service Definition**: Defined webserver service using httpd:latest image
3. **Container Naming**: Used `container_name: httpd` to meet specific naming requirement
4. **Port Configuration**: Mapped host port 6400 to container's standard HTTP port 80
5. **Volume Mounting**: Mounted host directory `/opt/itadmin` to Apache document root
6. **Service Deployment**: Used `docker compose up -d` to start service in background
7. **Verification Process**: Confirmed container running and static content accessible

**🎯 Key Success Factors:**
- **YAML syntax correctness** using proper indentation and structure
- **Docker Compose v2 usage** with `docker compose` command (not hyphenated)
- **Container naming** using `container_name` directive for specific name requirement
- **Port mapping** exposing container port 80 on host port 6400
- **Volume path accuracy** mounting to exact Apache document root path
- **Detached mode** using `-d` flag to run services in background
- **Static content preservation** mounting without modifying host directory contents

**⚠️ Critical Configuration Details:**
- **Compose file location** must be at `/opt/docker/docker-compose.yml`
- **Service vs container name** service name can vary, container name must be "httpd"
- **Port mapping format** uses `"host:container"` notation in quotes
- **Volume syntax** uses absolute paths for both host and container
- **Apache document root** is `/usr/local/apache2/htdocs` in official image

**🔒 Docker Compose Benefits:**
- **Declarative configuration** defines desired state in YAML file
- **Service orchestration** manages multiple containers together
- **Reproducibility** same compose file creates identical deployments
- **Easy management** start/stop/restart services with single commands

**⚠️ Important Docker Compose Concepts**:
- **Services** logical representation of containers in compose file
- **Networks** automatic network creation for service communication
- **Volumes** persistent data storage and host directory mounting
- **Container naming** explicit names vs auto-generated names

---

## ⚠️ Important Production Notes

🔧 **Infrastructure as Code**: Docker Compose provides declarative container deployment configuration

🔐 **Volume Mounting**: Host directory binding enables dynamic content updates without container rebuild

📊 **Service Management**: Single compose file manages entire application stack lifecycle

🛡️ **Static Content Hosting**: Read-only volume mount protects source content from container modifications